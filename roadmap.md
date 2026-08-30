# Roadhouse Radio & Portfolio — Project Roadmap

## 1. Site Architecture

```
roadhouseradio.live              → RADIO PAGE (landing, sleek/futuristic)
roadhouseradio.live/main         → MAIN PORTFOLIO SITE (index)
    /main#triskelle3d            → Pillar 1: Triskelle 3D (3D printing, micro-electronics, smart home)
    /main#ngahere-toa            → Pillar 2: Ngahere toa (artist name — DJ + composition)
    /main#multi-format-artist    → Pillar 3: Multi-Format Festival Artist (wizard staffs inside)
    /main#contact                → Contact form (email form, mailto:)
    triskelle3d.html             → Triskelle 3D detail page
    ngahere-toa-art.html         → Festival art detail (Matt Shortis)
    ngahere-toa-dj.html          → DJ / composition detail (SoundCloud)
    wizard-staffs.html           → Wizard staff detail page (kept; staffs also folded into pillar 3)
```

- **roadhouseradio.live** is the first thing anyone hits — pure radio-station energy (stream player, live location, IG feed). One clear CTA: "Explore" to `/main`.
- **/main** (`index.html`) is the landing/advertising page for the artist and the three core services, built as a single scrolling page with anchored nav + a scroll-linked morphing mark.
- **Pillars** (per client clarification):
  1. **Triskelle 3D** — 3D printing, micro-electronics, smart home.
  2. **Ngahere toa** — the artist name, two modalities in one section: **DJ work** (festival DJ sets) + **Composition work** (sound journeys, SoundCloud).
  3. **Multi-Format Festival Artist** — festival art under **Matt Shortis** (micro-electronics, LEDs, projection mapping, DMX), with **wizard staffs folded in** as signature pieces.
- Each pillar links to a dedicated detail page with more description and examples. Contact form is an email form (mailto:) with an inquiry-type dropdown.

---

## 2. Design System

### 2.1 Color — Two Palettes (auto-switches on OS `prefers-color-scheme`, no manual toggle needed unless you want one added later)

**Light Mode — "Weathered Parchment"**

| Token | Hex | Use |
|---|---|---|
| Background | `#F2ECE1` | Page background — warm bone/parchment |
| Foreground | `#2B2320` | Body text — warm near-black |
| Card | `#E8DFCF` | Panels, cards |
| Primary (Deep Red) | `#6B1E23` | Headings accents, primary buttons |
| Primary Foreground | `#F2ECE1` | Text on primary |
| Secondary (Dark Blue) | `#1F3A52` | Secondary buttons, nav accents |
| Secondary Foreground | `#F2ECE1` | Text on secondary |
| Accent (Copper) | `#B5651D` | Links, hover states, dividers, icons |
| Muted | `#D9CDB8` | Subtle backgrounds |
| Muted Foreground | `#6B5F4F` | Secondary/caption text |
| Border | `#C4B69B` | Dividers, input borders |
| Destructive | `#A3312A` | Form errors |

**Dark Mode — "Forge & Charcoal"**

| Token | Hex | Use |
|---|---|---|
| Background | `#15130F` | Page background — deep charcoal |
| Foreground | `#EDE6D9` | Body text — warm bone white |
| Card | `#201D18` | Panels, cards |
| Primary (Deep Red) | `#8B2E2E` | Headings accents, primary buttons |
| Primary Foreground | `#F5EDE0` | Text on primary |
| Secondary (Dark Blue) | `#26405A` | Secondary buttons, nav accents |
| Secondary Foreground | `#EDE6D9` | Text on secondary |
| Accent (Copper) | `#C9822E` | Links, hover states, glowing dividers |
| Muted | `#2A2620` | Subtle backgrounds |
| Muted Foreground | `#A89A82` | Secondary/caption text |
| Border | `#3A342A` | Dividers, input borders |
| Destructive | `#C1443C` | Form errors |

All pairs meet WCAG AA (4.5:1) for body text. Metal/copper accent is used sparingly (dividers, hover glows, icon strokes, DMX/circuit-line motifs) rather than as large fills, so it reads as "metal trim" not "orange theme."

### 2.2 Typography

| Role | Font | Notes |
|---|---|---|
| Display / Section Headings (Main site) | **Cinzel** | Engraved, carved-stone/rune-adjacent capitals — legible, not a novelty font |
| Wordmark / Hero mark only | **Uncial Antiqua** | Used *only* for the logo/hero title treatment — too decorative for body or subheads |
| Body copy (Main site) | **Inter** | Clean, highly legible, lets the display font carry the "Viking" energy |
| Headings (Radio page) | **Space Grotesk** | Techy, geometric — sets the radio page apart as "control panel," not "portfolio" |
| Data readouts (stream status, live indicator, coordinates) | **Share Tech Mono** | Monospace — reads like telemetry/HUD text |

This gives two related-but-distinct voices: **Main site = carved rune/metal**, **Radio page = HUD/broadcast console** — tied together by the shared color system.

### 2.3 Scroll Effect

Hybrid, per your answer: **parallax depth + morphing illustration.**
- 2–3 background layers move at different scroll speeds (parallax) — e.g. a faint rune/circuit-line texture layer, a mid-ground silhouette layer, sharp foreground content.
- A single evolving illustration/SVG morphs through your three pillars as the user scrolls past each section: **carved rune mark → stage/light rig silhouette → circuit board traces**, using scroll-linked SVG path morphing (not video, so it stays crisp, lightweight, and themeable across light/dark mode).
- `prefers-reduced-motion` respected — falls back to a simple crossfade for users who need reduced motion.

### 2.4 Motion & Interaction Standards
- Micro-interactions: 150–300ms, meaningful (not decorative-only)
- Touch targets ≥44×44px, 8px+ spacing
- Focus rings visible and never removed
- No horizontal scroll, mobile-first breakpoints

---

## 3. Page Breakdown

### 3.1 Radio Page (`radio.html` — roadhouseradio.live)
- Full-viewport hero, HUD-styled (Space Grotesk + mono), respects light/dark token swap
- **Live pill** top-right + **"Now Broadcasting"** pill — both red (`status-pill--live`)
- **Stream player**: custom-styled audio player UI (play/pause, volume, "LIVE" pulse indicator) — **wired to the m3u stream** `https://admin.soundsvagrant.live/hls/sounds_vagrant/live.m3u8` (play/pause/volume/ended/pause sync JS)
- **FM transmission** block: `SIGNAL ORIGIN — LAT 43.9° / LON -73.1°` + `FM FREQ — 87.7 MHz` (placeholders)
- **Instagram follow** block (@Roadhouse.Radio) — *swap for IG profile embed widget later*
- CTA "Explore" → `/main`; footer "Contact us" → `index.html#contact`
- Minimal nav — this page's job is "listen + locate," nothing else

### 3.2 Main Site (`index.html` — landing/advertising page)
- **Hero**: wordmark (Cinzel), tagline, scroll cue
- **Scroll-linked morphing mark** (Phase 3): a fixed background SVG morphs **triskelle → stage rig → circuit traces** as the user scrolls each pillar into view, plus 2 parallax depth layers. `prefers-reduced-motion` falls back to a static crossfade.
- **Pillar 1 — Triskelle 3D**: 3D printing, micro-electronics, smart home. Links to `triskelle3d.html`.
- **Pillar 2 — Ngahere toa**: artist name, one section with two modality sub-cards — **DJ work** (festival DJ sets; links to `ngahere-toa-dj.html`) + **Composition work** (in-progress "sound journeys"; links to SoundCloud).
- **Pillar 3 — Multi-Format Festival Artist**: festival art under **Matt Shortis** (interactive art, LEDs, projection mapping, DMX), with **wizard staffs folded in** as signature pieces. Links to `ngahere-toa-art.html`.
- **Social/follow** row: SoundCloud (`https://soundcloud.com/ngaheretoa`) + Instagram (`@Roadhouse.Radio`).
- **Contact section**: email form (below).

### 3.3 Contact Form — Email Form (Phase 4)
Email form (Name, Email, Inquiry-type dropdown, Message). Submission uses `mailto:roadhouseradio@proton.me` — opens the user's mail client with the message composed.

**Progressive disclosure by inquiry type** — choosing a type reveals only the relevant fields, hides the rest, and keeps hidden fields out of the tab order. Inquiry types and their specific fields:

| Inquiry Type | Type-Specific Fields |
|---|---|
| Event management | Event name, Event dates, Scale/audience, Services needed |
| Commission a piece | Piece type, Timeline, Location |
| Performance booking | Venue/event, Date, Set length |
| Technology consultation | Project type (3D print / Home Assistant / IoT build / RF-LoRa / Other), Timeline |
| Other | What's it about? |

All variants share Name/Email/Message. **Open:** wire a backend endpoint later if preferred (`// TODO (Phase 5)`).

---

## 4. Tech Stack & Hosting

- Static site (HTML/CSS/JS or lightweight React) — deployable to GitHub Pages or any static host, matching your self-host plan
- No backend dependency for launch — contact form UI-complete, submission logic stubbed for you to wire up
- Instagram integration via embeddable widget (no API key required for a public profile embed)
- Images optimized (WebP), lazy-loaded, layout-stable (no CLS)

---

## 5. Open Items (waiting on you — build proceeds with placeholders until filled)

- [x] SoundCloud profile link — **resolved**: `https://soundcloud.com/ngaheretoa` (link-out used; embed can be added later)
- [x] Instagram handle — **resolved**: `@Roadhouse.Radio` (link-out used; embed widget can be added later)
- [x] Working `.m3u` stream URL — **resolved**: `https://admin.soundsvagrant.live/hls/sounds_vagrant/live.m3u8` wired into the radio player
- [x] Radio page edit batch — **resolved**: red live pills, "Explore" CTA, FM transmission block, footer "Contact us"
- [x] Pillar restructure — **resolved**: Ngahere toa → DJ + composition; Wizard Staffs folded into Multi-Format Festival Artist
- [x] Contact form — **resolved (Phase 4)**: email form + CSS, progressive disclosure by inquiry type, mailto: submission
- [x] Real contact email — **resolved**: `roadhouseradio@proton.me`
- [ ] Real live location / coords for the radio page (placeholder coords used)
- [ ] Logo / wordmark / photos (art installs, DJ sets, staffs, the truck)
- [ ] Bio blurb + real festival/event names (placeholders used until provided)
- [ ] Confirmation on GitHub Pages vs. other host (for domain routing guidance later)
- [ ] Verify `wizard-staffs.html` — kept as a detail page, but confirm it should stay linked/orphaned

---

## 6. Build Phases

1. **Phase 0 — Design System Foundation**: ✅ done — color tokens, type scale, spacing system, base components (buttons, nav, cards) in both themes
2. **Phase 1 — Main Site Skeleton**: ✅ done — hero, 3 clarified pillars, responsive layout, social links
3. **Phase 2 — Radio Page**: ✅ done (UI) — stream player UI, location fallback, IG follow block, CTA to `/main`. Stream URL + real coords/IG embed pending
4. **Phase 3 — Scroll Effects & Motion**: ✅ done — parallax layers + morphing SVG (triskelle → stage rig → circuit), reduced-motion crossfade fallback
5. **Phase 4 — Contact Form**: ✅ done — email form (mailto:) + styled inputs/select/textarea, validation states, accessibility pass. Backend/real-email hook pending
6. **Phase 5 — Content Pass**: swap in your real copy/links/images/contact email as they come in
7. **Phase 6 — QA**: accessibility (contrast, keyboard nav, alt text), performance, cross-device check

---

