# Roadhouse Radio & Portfolio — Handover Document

**Purpose:** Hand off the project so work can continue on the remaining phases (5 — Content Pass, 6 — QA, plus any follow-ups). This document captures current state, decisions, open items, and the design system so the next AI/developer can pick up where the previous work left off.

> ⚠️ **NOTE TO THE AI CONTINUING THIS PROJECT:** The user has instructed that work on this project should use the **`ui-ux-pro` skill** (the UI/UX Pro Max skill) — invoke it for design/UX decisions, review, and implementation guidance. Apply the skill's patterns for any UI/UX work (layout, color, typography, interaction states, accessibility, responsive checks) rather than inventing ad-hoc rules.

---

## 1. Project Overview

A static HTML/CSS/JS site for **Roadhouse Radio** — an artist/radio hybrid practice. Two entry points, one design system:

| Page | File | Role |
|---|---|---|
| `roadhouseradio.live` | `radio.html` | Domain-root landing — radio-station "HUD/console" voice. Plays the live stream, shows location. |
| `roadhouseradio.live/main` | `index.html` | Main portfolio/landing page — scroll-linked morphing parallax, three pillars + contact form. |

**Detail sub-pages** (share nav/stage/footer structure):
- `triskelle3d.html` — Triskelle 3D detail
- `ngahere-toa-art.html` — Festival art detail (Matt Shortis)
- `ngahere-toa-dj.html` — DJ / composition detail
- `wizard-staffs.html` — Wizard staff detail (kept; staffs also folded into the festival-art pillar)

**Supporting files:** `css/tokens.css`, `css/base.css`, `css/components.css`, `style-guide.html`, `roadmap.md`, `HANDOVER.md` (this file).

---

## 2. Design System (Phase 0 — complete)

### Colors — two palettes, auto-switched by OS `prefers-color-scheme`
- **Light "Weathered Parchment"**: bg `#F2ECE1`, fg `#2B2320`, card `#E8DFCF`, accent copper `#B5651D`, destructive red `#A3312A`
- **Dark "Forge & Charcoal"**: bg `#15130F`, fg `#EDE6D9`, card `#201D18`, accent `#C9822E`, destructive `#C1443C`

All tokens are semantic/role-based in `css/tokens.css` (never raw hex in component CSS). Red "live" pills and form-error states use `--color-destructive`.

### Typography
| Role | Font |
|---|---|
| Display / section headings | **Cinzel** |
| Wordmark / hero only | **Uncial Antiqua** (decorative — headings/body only, never body copy) |
| Body | **Inter** |
| Radio page headings | **Space Grotesk** (HUD voice) |
| Data readouts / telemetry | **Share Tech Mono** |

Two related voices: **Main site = carved rune/metal**, **Radio page = HUD/broadcast console** — tied by the shared color system.

### Motion & interaction standards
- Micro-interactions 150–300ms, meaningful only
- Touch targets ≥ 44×44px, 8px+ spacing
- Focus rings visible, never removed (`:focus-visible` in `base.css`)
- No horizontal scroll, mobile-first breakpoints (768 / 900 / 1024)
- `prefers-reduced-motion` respected throughout

---

## 3. Radio Page (`radio.html`) — state

- **Live pill** top-right + **"Now Broadcasting"** pill — both red (`status-pill--live`)
- **Stream player** wired to the live HLS stream: `https://admin.soundsvagrant.live/hls/sounds_vagrant/live.m3u8` — custom play/pause/volume UI + ended/pause sync JS. The track slider is a placeholder for stream position.
- **FM transmission** block: `SIGNAL ORIGIN — LAT 43.9° / LON -73.1°` + `FM FREQ — 87.7 MHz` (**placeholders** — real values pending)
- **Instagram follow** block (@Roadhouse.Radio) — link-out only; IG embed widget pending
- CTA "Explore" → `/main`; footer "Contact us" → `index.html#contact`
- Minimal nav — "listen + locate" only

---

## 4. Main Site (`index.html`) — state

### Structure
- **Hero**: wordmark (Cinzel), tagline, scroll cue → `#ngahere-toa`
- **Parallax + per-pillar atmosphere** (Phase 3, reworked): fixed background with three atmosphere washes (**ember / LED / forge**) that crossfade as the user scrolls each pillar into view, and a morphing SVG that shifts **stage rig → circuit traces → triskelle** to match. Each pillar's mark tint + number badge + eyebrow recolor to its own energy. `prefers-reduced-motion` → gentle static crossfade (washes + mark). Scroll-spy nav uses `#ngahere-toa`, `#multi-format-artist`, `#triskelle3d`, `#contact`.
- **Pillar 1 — Ngahere toa** (01): artist name, two modality sub-cards:
  - *DJ work* — Festival DJ Sets → `ngahere-toa-dj.html`
  - *Composition work* — Sound Journeys → SoundCloud
- **Pillar 2 — Multi-Format Artist** (02, renamed — no "Festival" in the title): festival art under **Matt Shortis** (interactive art, LEDs, projection mapping, DMX), **wizard staffs folded in** as signature pieces. → `ngahere-toa-art.html`
- **Pillar 3 — Triskelle 3D** (03): 3D printing, micro-electronics, smart home. → `triskelle3d.html`
- **Social/follow** row: SoundCloud + Instagram
- **Contact section** (email form, below)

---

## 5. Contact Form (Phase 4 — complete)

- **Email:** `mailto:roadhouseradio@proton.me` (opens mail client with message composed)
- **Shared fields:** Name, Email, Inquiry-type dropdown, Message
- **Progressive disclosure by inquiry type** — picking a type reveals only its relevant fields, hides the rest, and keeps hidden fields out of the tab order (`tabindex="-1"`). All groups live in markup; JS toggles `hidden` on `.contact-form__group`.

| Inquiry Type | Type-Specific Fields |
|---|---|
| Event management | Event name, Event dates, Scale/audience, Services needed |
| Commission a piece | Piece type, Timeline, Location |
| Performance booking | Venue/event, Date, Set length |
| Technology consultation | Project type (3D print / Home Assistant / IoT build / RF-LoRa / Other), Timeline |
| Other | What's it about? |

- CSS: `.contact-form`, `.contact-form__row`, `.contact-form__group`, `.contact-form__group-head` in `components.css`; validation cue on `:invalid`; focus glow via `--shadow-glow-accent`.

---

## 6. Current Build-Phase Status

1. **Phase 0 — Design System Foundation**: ✅ done
2. **Phase 1 — Main Site Skeleton**: ✅ done
3. **Phase 2 — Radio Page**: ✅ done (UI) — stream wired; coords/IG embed pending
4. **Phase 3 — Scroll Effects & Motion**: ✅ done
5. **Phase 4 — Contact Form**: ✅ done
6. **Phase 5 — Content Pass**: ⏳ **next** — swap real copy/links/images/coords
7. **Phase 6 — QA**: pending — accessibility, performance, cross-device

---

## 7. Open Items (to resolve in Phase 5 / 6)

- [ ] Real live location / coords + FM frequency for the radio page (placeholders used: `LAT 43.9° / LON -73.1°`, `87.7 MHz`)
- [ ] Logo / wordmark / photos (art installs, DJ sets, staffs, the truck) — media-placeholder blocks are reserved with aspect-ratio, so image swaps cause no CLS
- [ ] Bio blurb + real festival/event names (placeholders used until provided)
- [ ] Confirmation on GitHub Pages vs. other host (domain routing)
- [ ] `wizard-staffs.html` — kept as a detail page; now nav-linked to the Multi-Format Artist pillar (`#multi-format-artist`) since staffs are folded into it. Confirm desired orphan/link state.
- [ ] Contact form: wire a backend endpoint if preferred over `mailto:` (`// TODO (Phase 5)` in index.html)
- [ ] Swap Instagram link-out for an embeddable profile widget (no API key needed)
- [ ] Radio track slider: real stream-position once HLS supports it

---

## 8. QA Checklist to Run in Phase 6 (with the `ui-ux-pro` skill)

- [ ] Accessibility: contrast (WCAG AA), full keyboard nav, skip link, focus rings, `aria-current` scroll-spy, form labels/`aria` for progressive disclosure
- [ ] Performance: image optimization (WebP, lazy-load), no CLS, script weight
- [ ] Responsive: 320px–desktop, mobile nav, touch targets ≥ 44px
- [ ] Reduced-motion: parallax/morph crossfade fallback, animation overrides
- [ ] HTML/CSS validity: tag balance, no mismatches (verified clean at handover)
- [ ] Link integrity across all pages + detail sub-pages
