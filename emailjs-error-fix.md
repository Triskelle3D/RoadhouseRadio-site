# EmailJS "Template: One or more dynamic variables are corrupted" — Root Cause & Fix

## What I researched
- EmailJS docs (dynamic variables page, SDK send page)
- EmailJS SDK source / GitHub search
- User site at /Users/admin/Documents/website-dev/mysite-local (form + emailjs.sendForm code)
- The form uses progressive disclosure (`hidden` groups, `tabindex="-1"` on hidden fields)
- Template uses {{name}}, {{email}}, etc. matching form `name=` attributes

## Actual root cause (confirmed pattern)
The error appears IN the delivered email body — it's a **server-side EmailJS template validation failure**, not a JS console error. It means the server parsed the submitted parameters and found variables that don't match the template definition or contain corrupt/invalid values.

Most common specific triggers (in order of likelihood for this form):

1. **Hidden progressive-disclosure fields still serialize** — `sendForm('service_3m0xcbj', 'template_yttsd2a', this)` submits ALL non-disabled inputs in the `<form>`, including those inside groups where `group.hidden = true`. Only `tabindex="-1"` is set; inputs are NOT disabled. So fields like `event_name`, `piece_type`, etc. are submitted as empty strings or partial values even when hidden. If the EmailJS template is missing any `{{event_name}}` / `{{piece_type}}` / etc. variable → server flags them as "corrupted".

2. **Template variables don't cover every submitted field** — The form sends: `name`, `email`, `inquiry`, `message`, `event_name`, `event_dates`, `event_scale`, `event_services`, `piece_type`, `piece_timeline`, `piece_location`, `perf_venue`, `perf_date`, `perf_length`, `tech_type`, `tech_timeline`, `other_topic`. If the EmailJS template only defines a subset (e.g., just the base 4) and the rest are submitted empty → corruption error.

3. **Empty/undefined `inquiry` on load** — The `<select id="cf-inquiry">` starts unselected (`showGroup('')` hides everything). Submitting with `inquiry = ""` or `undefined` can trigger the error if the template expects `{{inquiry}}` to have a real value, or if an unselected `<select>` with no `value=` submits oddly.

4. **Variable > 50KB total** — All dynamic variables combined must be under 50KB (EmailJS limit, per docs). Unlikely here, but possible if a large message is pasted.

5. **Field names containing patterns that conflict with EmailJS parsing** — `event_dates`, `event_scale`, etc. are fine generally, but if any field actually submits a JSON/array value instead of string, the parser fails.

## Specific steps to fix (ordered)

### Immediate / most likely fix
A. **In EmailJS dashboard, add `{{...}}` for EVERY form field** used by the form, not just the visible ones. Template must contain at minimum:
```
{{name}} {{email}} {{inquiry}} {{message}}
{{event_name}} {{event_dates}} {{event_scale}} {{event_services}}
{{piece_type}} {{piece_timeline}} {{piece_location}}
{{perf_venue}} {{perf_date}} {{perf_length}}
{{tech_type}} {{tech_timeline}} {{other_topic}}
```
If any submitted field lacks a matching `{{}}`, EmailJS reports it as corrupted.

B. **Prevent hidden fields from being submitted.** Two ways:
- Change `showGroup()` so hidden groups set inputs to `disabled = true` (not just hidden group + tabindex=-1), and clear `disabled = false` when shown. Disabled inputs are NOT submitted with `sendForm`.
- OR stop using `sendForm(this)`; instead build a clean params object and call `emailjs.send()` with only the visible/active fields.

C. **Initialize `inquiry` select** to a valid default option (e.g., "General Inquiry") so it's never empty/submitted as undefined at submit time.

D. **Verify exact match of `name=` to `{{}}` variables** — case-sensitive, no extra underscores, no `[]` array syntax. The user's form already uses exact matches (`name="name"` → `{{name}}`), which is correct.

E. **Check for hidden fields with empty `name` attributes** inside the hidden groups — any unnamed input can inject unexpected data.

### If using `sendForm()` specifically
`emailjs.sendForm()` collects all form inputs via the native HTMLFormElement serialization. The progressive-disclosure mechanism (toggling `group.hidden`) does NOT prevent serialization of the inputs inside. The correct approach with progressive disclosure is either:
- Add `disabled` handling (see B); OR
- Switch to `emailjs.send(service_3m0xcbj, 'template_yttsd2a', {name: ..., email: ..., ...})` where you manually collect only visible fields.

## Files examined / not modified
- `/Users/admin/Documents/website-dev/mysite-local/index.html` — form + EmailJS code (line 697+ uses `sendForm('service_3m0xcbj', 'template_yttsd2a', this)`)
- No local edits made (research task only)

## Key authoritative references used
- EmailJS docs: "Dynamic variables in templates" — variables replaced from params; missing = empty string; 50KB total limit; triple-bracket `{{{}}}` for unescaped HTML
- EmailJS SDK docs (`emailjs.send` / `sendForm`) — `sendForm` takes HTML form element and submits all non-disabled inputs
- User site's progressive-disclosure mechanism (line 652-670): `group.hidden = !matches`; only `tabindex="-1"` added; inputs remain enabled → they serialize

## Bottom line
The error "Template: One or more dynamic variables are corrupted" almost always means **the template variables don't fully match what's being submitted**, or **submitted values are malformed/empty in a way the parser rejects**. For this form, the primary suspect is hidden progressive-disclosure inputs being submitted empty, combined with an EmailJS template that doesn't define variables for every possible submitted field (`event_*`, `piece_*`, `perf_*`, `tech_*`, `other_topic`). Fix: either add all missing `{{}}` variables to the template, or prevent hidden inputs from serializing via `disabled`, AND initialize the select.
