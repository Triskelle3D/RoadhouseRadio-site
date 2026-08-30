# Roadhouse Radio & Portfolio — Handover Document #2

**Purpose:** Hand off the project again so a fresh model can continue development. This supersedes/complements `HANDOVER.md` and `roadmap.md` by recording the **latest round of changes** (the contact-form, background-transition, hero-removal, radio-page, and map requests) plus the **true current state** of every file. Read this alongside `HANDOVER.md` (original architecture) and `roadmap.md` (build phases).

> ⚠️ **NOTE TO THE AI CONTINUING THIS PROJECT:** Work on this project should use the **`ui-ux-pro` skill** (UI/UX Pro Max) for any design/UX decisions, review, and implementation guidance — layout, color, typography, interaction states, accessibility, responsive checks. Apply its patterns rather than inventing ad-hoc rules.

---

## 1. TL;DR — What changed in the last round

| Request | Status | Where |
|---|---|---|
| Contact-form fields turn red **only after the user tries a field** | ✅ Done | `index.html` (JS), `css/components.css` |
| **Smooth** colour transition between pillar backgrounds | ✅ Done | `css/tokens.css` (`--ground-1..4`), `css/components.css` (`.scroll-stage`), `index.html` (JS) |
| **Remove** the hero block ("Roadhouse Radio / tagline / Scroll to explore") | ✅ Done | `index.html` |
| Triskelle 3D gets a **better (non-black)** background colour | ✅ Done | `css/tokens.css` (`--ground-3`), `triskelle3d.html` (`page-maker`) |
| **4 backgrounds** incl. a distinct contact-form colour, smooth between | ✅ Done | `css/tokens.css`, `css/components.css`, `index.html` |
| radio.html: Signal-origin **title and figures on separate lines** | ✅ Done | `radio.html` + `css/components.css` (`.fm-label` / `.fm-figure`) |
| radio.html: new touring text | ✅ Done | `radio.html` |
| radio.html: **stylized NZ map** (bottom ⅔ North Island, light/dark, dot at coords) | ✅ Done | `radio.html` + `css/components.css` (`.nz-map`) |

---

## 2. Current File Map

| File | Role / current state |
|---|---|
| `index.html` | Main portfolio page. Hero removed (sr-only `<h1>` kept). 4-section scroll stage. Contact form with "touched-only" red validation. |
| `radio.html` | Radio/HUD page. Stylized NZ map, separate-line FM readouts, new touring text. Stream player unchanged. |
| `triskelle3d.html` | Maker detail page. Now `class="page-maker"` → steel-blue ground (`--ground-3`). |
| `ngahere-toa-art.html` / `ngahere-toa-dj.html` / `wizard-staffs.html` | Detail pages — unchanged this round. |
| `css/tokens.css` | Design tokens. Now includes `--atmos-contact`, `--ground-1..4` (light + dark + `data-theme` override blocks). |
| `css/base.css` | Global base — unchanged this round. |
| `css/components.css` | Components + scroll-stage + contact validation + NZ-map + FM readout + `page-maker` styles. |
| `style-guide.html` | Style guide — unchanged; does **not** reference the per-pillar ground/atmo tokens. |
| `HANDOVER.md` / `roadmap.md` | Original docs — still largely accurate for architecture/phases; this file records the deltas. |

---

## 3. Design System — Colour Tokens (post-change)

### Per-pillar "ground" (page background) colours — 4 distinct, smooth-transitioned
These are the page background colours **per section**. The `.scroll-stage` transitions `background-color` over **900ms**, so scrolling between sections eases the colour rather than flipping it.

| Section | Light (parchment) | Dark (charcoal) | Atmosphere glow token |
|---|---|---|---|
| 1 — Ngahere toa (ember) | `--ground-1: #F3E7D6` | `#1E160F` | `--atmos-ngahere` |
| 2 — Multi-Format Artist (violet) | `--ground-2: #EEE6F0` | `#181020` | `--atmos-artist` |
| 3 — Triskelle 3D (steel-blue, **not black**) | `--ground-3: #E4E9EC` | `#111A24` | `--atmos-triskelle` |
| 4 — Contact (forest/pine) | `--ground-4: #E7EAE0` | `#14201A` | `--atmos-contact` |

`--atmos-contact` was added to **all four** theme blocks in `tokens.css` (`:root`, dark media-query, `[data-theme=light]`, `[data-theme=dark]`) so the contact glow works in both themes. Same for `--ground-1..4`.

### Where to find them (`css/tokens.css`)
- Light default: lines ~56–67
- Dark media query: lines ~216–227
- `[data-theme=light]` override: ~267–271
- `[data-theme=dark]` override: ~306–313

**Rule:** never reference raw hex in component CSS — always use the semantic tokens.

---

## 4. Contact Form — "Touched-only" Red Validation (this round)

**Goal:** a field must NOT show the red/invalid state until the user has actually tried to use it.

### CSS (`css/components.css`, ~line 575)
- The old always-on rule:
  ```css
  .contact-form__row input:invalid { border-color: ... } /* REMOVED */
  ```
- Replaced with three progressive rules:
  1. `.contact-form :is(input,textarea,select).is-touched:invalid` — red only once a field is marked touched.
  2. A `.contact-form__group .contact-form__row[data-invalid="true"]` rule for the non-`required` disclosure-group fields (flagged only on submit attempt).
  3. `:user-invalid` — the modern native progressive flag (fires after the user edits a field); older browsers fall back to the `.is-touched` rule.

### JS (`index.html`, end of the main `<script>`)
```js
const form = document.getElementById('contact-form');
if (form) {
  form.querySelectorAll('input, select, textarea').forEach(el => {
    el.addEventListener('blur', () => el.classList.add('is-touched'), { once: true });
    if (el.tagName === 'SELECT') {
      el.addEventListener('input', () => el.classList.add('is-touched'), { once: true });
    }
  });
  form.addEventListener('submit', () => {
    form.querySelectorAll('input, select, textarea').forEach(el => {
      if (!el.closest('[hidden]')) el.classList.add('is-touched');
    });
  });
}
```
So: blur a field (or press **Send message** with empty fields) → the invalid fields turn red. On load, nothing is red.

---

## 5. Scroll Stage — 4-section driving (this round)

### Structure (`index.html`)
The `.scroll-stage` now has **four** atmosphere divs (ember / LED / forge / forest):
```html
<div class="scroll-stage" data-atmo="ngahere">
  <div class="scroll-stage__layer scroll-stage__layer--back" id="layer-back"></div>
  <div class="scroll-stage__atmo scroll-stage__atmo--ngahere" id="atmo-ngahere"></div>
  <div class="scroll-stage__atmo scroll-stage__atmo--artist"   id="atmo-artist"></div>
  <div class="scroll-stage__atmo scroll-stage__atmo--triskelle" id="atmo-triskelle"></div>
  <div class="scroll-stage__atmo scroll-stage__atmo--contact"   id="atmo-contact"></div>
  <div class="scroll-stage__mark" id="morph-mark">…</div>
</div>
```

### JS driving (section-based, not scroll-ratio)
```js
const pillars = [
  { atmo: 'ngahere',   morph: 'stage',     id: 'ngahere-toa' },
  { atmo: 'artist',    morph: 'circuit',   id: 'multi-format-artist' },
  { atmo: 'triskelle', morph: 'triskelle', id: 'triskelle3d' },
  { atmo: 'contact',   morph: 'triskelle', id: 'contact' }
];
```
- `currentPillarKey()` picks the **last section whose top has passed the viewport mid-line** — so detection works for any section length, and the contact section gets its own ground + wash.
- `applyPillar(atmoKey)` sets each wash opacity to 0/1, sets `data-atmo` on the stage (which drives the `.scroll-stage` `background-color` + mark tint via CSS), and morphs the SVG mark.
- **Reduced-motion:** no scroll-linking/parallax; still colours the background + wash to the section in view once on load (`applyPillar(currentPillarKey())`).

### CSS (`css/components.css`, ~line 655)
```css
.scroll-stage {
  ...
  background-color: var(--color-background);
  transition: background-color 900ms var(--ease-standard);
}
.scroll-stage[data-atmo="ngahere"]   { background-color: var(--ground-1); }
.scroll-stage[data-atmo="artist"]    { background-color: var(--ground-2); }
.scroll-stage[data-atmo="triskelle"] { background-color: var(--ground-3); }
.scroll-stage[data-atmo="contact"]   { background-color: var(--ground-4); }
```
- Washes crossfade over `900ms` too.
- **Reduced-motion** block sets `.scroll-stage { transition: none }` and drops the old `mark-crossfade` / `atmo-crossfade` keyframes (removed this round — they conflicted with the new system).

> ⚠️ **Note:** The `--ground-1..4` + smooth-transition groundwork was discovered already present in `tokens.css`/`components.css` (from a prior parallel pass) and was completed/verified rather than duplicated. Keep this system, don't re-introduce the old `atmo-crossfade` keyframes.

---

## 6. Hero Removal (this round)

- The visible `<section class="hero container">` (wordmark + tagline + scroll cue) is **gone** from `index.html`.
- Kept a screen-reader-only heading for the page's top-level title:
  ```html
  <h1 class="sr-only">Roadhouse Radio — Ngahere toa, Multi-Format Artist & Triskelle 3D</h1>
  ```
- The old `.hero`, `.hero__wordmark`, `.hero__tagline`, `.hero__scroll-cue` CSS still exists in `components.css` but is now **unused** (safe to leave or prune).

---

## 7. Triskelle 3D — non-black background (this round)

- The Triskelle pillar ground is steel-blue: light `--ground-3: #E4E9EC`, dark `--ground-3: #111A24` (clearly blue, not pure black). Its atmosphere glow is `--atmos-triskelle` (steel blue).
- `triskelle3d.html` now has `<body class="page-maker">`, and `components.css` gives it a distinct steel ground:
  ```css
  .page-maker .scroll-stage {
    background-color: var(--ground-3);
    transition: background-color var(--duration-slow) var(--ease-standard);
  }
  ```

---

## 8. Radio Page (`radio.html`) — this round

### Stylized New Zealand map (new)
A hand-built inline SVG (no external asset), zoomed to the bottom ~⅔ of the North Island, with a pulsing marker dot at the broadcast coordinates (41.28°S / 174.78°E):
```html
<svg class="nz-map" viewBox="0 0 320 220" role="img"
     aria-label="Map of New Zealand showing the broadcast origin near Te Awamutu in the Waikato region of the North Island."
     focusable="false">
  <path class="nz-map__land" d="…North Island silhouette…"/>
  <text class="nz-map__island-name" x="150" y="44">North Island</text>
  <g class="nz-map__marker">
    <circle class="nz-map__dot-glow" cx="196" cy="114" r="6"/>
    <circle class="nz-map__dot-core" cx="196" cy="114" r="4"/>
    <line class="nz-map__cross" …/>
  </g>
</svg>
<p class="nz-map__caption">Broadcast origin — New Zealand</p>
```
- **Light/dark theming:** the SVG uses `fill: color-mix(... var(--color-accent) …)` and `stroke: var(--color-accent)` etc., so it recolours automatically with the OS theme. No separate images needed.
- **Dot animation:** `@keyframes nz-pulse` (disabled under `prefers-reduced-motion`).
- The coordinates in the map marker are the same as the "Signal origin" figure (41.28°S / 174.78°E) — keep them in sync if you change the real coords.

### FM readout — label & figure on separate lines (new)
```html
<p class="fm-reading">
  <span class="fm-label">Signal origin</span>
  <span class="fm-figure">41.28°S / 174.78°E</span>
</p>
<p class="fm-reading">
  <span class="fm-label">FM frequency</span>
  <span class="fm-figure">87.7 MHz</span>
</p>
```
CSS: `.fm-label` (mono uppercase, muted) then `.fm-figure` (mono accent, `display:block`, larger) — stacked on their own lines.

### New touring text
> **"Always touring: Broadcasting along the road. Follow our instagram for the latest stop."**
(`.fm-reading__note`)

### Still placeholder
- Real live coords / FM frequency — the map + figures use `41.28°S / 174.78°E` and `87.7 MHz` as the **current placeholders** (same values used in the map marker + readouts — update together). Old `LAT 43.9° / LON -73.1°` and `87.7 MHz` placeholder text is gone.
- Stream player (HLS) unchanged; IG embed widget still a link-out.

---

## 9. Files Changed This Round (for review / git)

- `index.html` — hero removed, sr-only h1, 4-section scroll-stage JS, contact "touched" JS.
- `radio.html` — NZ map + caption, separate-line FM readouts, new touring text.
- `triskelle3d.html` — `class="page-maker"`.
- `css/tokens.css` — `--atmos-contact`, `--ground-1..4` in all 4 theme blocks.
- `css/components.css` — scroll-stage ground transition + 4 atmo states + reduced-motion cleanup, contact validation (`.is-touched`, `:user-invalid`), `.fm-label`/`.fm-figure`, `.nz-map` styles + `nz-pulse`, `.page-maker` ground.

---

## 10. Validation Performed (as of handover)

- **JS:** `node --check` passes for `index.html`, `radio.html`, `triskelle3d.html` (extracted inline scripts) — no syntax errors.
- **HTML tag balance:** `radio.html` sections/divs/svg all balanced.
- **CSS brace balance:** `tokens.css`, `components.css`, `base.css` open==close (no truncation).
- **Key-feature greps:** sr-only h1 present, `is-touched` present, `atmo-contact` present, `--ground-1..4` in all theme blocks, scroll-stage 900ms transition present, `.nz-map` + `.fm-label` present, `.page-maker` present.

---

## 11. Open Items / Next Steps (carry forward from HANDOVER.md)

- [ ] **Real live coords + FM frequency** — update the radio page placeholder `41.28°S / 174.78°E` and `87.7 MHz` (update **both** the map-marker dot and the readout figures together).
- [ ] Logo / wordmark / photos — swap media-placeholders (aspect-ratio reserved, no CLS).
- [ ] Bio blurb + real festival/event names.
- [ ] Host confirmation (GitHub Pages vs. other) for domain routing.
- [ ] Contact form backend endpoint — currently `mailto:roadhouseradio@proton.me` (`// TODO (Phase 5)` in `index.html`).
- [ ] Swap Instagram link-out for an embeddable profile widget.
- [ ] Radio track slider — real stream position once HLS supports it.
- [ ] Verify `wizard-staffs.html` orphan/link state.

## 12. QA Checklist to Run Next (with the `ui-ux-pro` skill)

- [ ] Accessibility: WCAG AA contrast (esp. new grounds/washes vs text), keyboard nav, focus rings, `aria-current` scroll-spy, form labels/`aria`.
- [ ] Performance: image optimization (WebP, lazy-load), no CLS, script weight.
- [ ] Responsive: 320px–desktop, mobile nav, touch targets ≥ 44px.
- [ ] Reduced-motion: scroll-stage ground/wash static fallback, NZ map dot animation disabled.
- [ ] HTML/CSS validity: tag balance, no mismatches.
- [ ] Link integrity across all pages + detail sub-pages.
