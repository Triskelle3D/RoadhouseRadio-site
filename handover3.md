# Roadhouse Radio — Handover #3 (Symbol Animation + Background Timing)

**Purpose:** Hand off the symbol-animation work + background-timing restore. Read alongside `HANDOVER.md` (orig architecture) + `handover2.md` (pre-animation state).

**Date:** 2026-08-28 session. **Profile:** jeeves (`/Users/admin/Documents/Obsidian/Jeeves`).

---

## 1. TL;DR — What changed this round

| Change | Status | Where |
|---|---|---|
| 3 background symbols (artist spiral / ngahere celtic / triskelle triple) replaced opacity-only crossfade with **rotate+scale animation** driven by scroll progress | ✅ Done | `index.html` (`#symbol-layer`, `applySymbolAnimation`) |
| Animation = **scroll = forward/reverse control** (no rAF timer; direct formula from `scrollY / max`) | ✅ Done | `index.html` JS |
| **2nd symbol (ngahere)** — faster in (`0.02` vs `0.09`), longer out (`0.70` vs `0.55`), then extended further (`0.95` at one point); **hold-at-apex** (scale=1, op=1 over middle 30% of phase) added to 2nd + 3rd | ✅ Done | `index.html` (`data-phase-*`, `applySymbolAnimation`) |
| **1st symbol (artist)** — out extended (`0.25` vs `0.18`) | ✅ Done | `index.html` HTML attrs |
| **3rd symbol (triskelle)** — in delayed (`0.40` vs `0.27`), out extended (`0.75` vs `0.54`), then further long out | ✅ Done | `index.html` HTML attrs |
| **Background colour timing** — restored to original `currentPillarKey()` (section-mid-viewport detection); `atmoAt` thresholds and scroll-progress colour logic removed | ✅ Done | `index.html` JS |
| **First symbol visible at very top** (`p < 0.02` guard prevents `raw <= 0` from hiding it); scroll-up-to-top keeps it visible | ✅ Done | `index.html` `applySymbolAnimation` |
| **Rotation direction harmonised** (`visualRot = baselineMirror ? -rot : rot`) so all 3 symbols spin same visual direction despite `scaleX(-1)` on artist/ngahere | ✅ Done | `index.html` JS |

---

## 2. Current Symbol Phase Values (as of this write)

| Symbol | Start | End | Window | Notes |
|---|---|---|---|---|
| `morph-artist` (1st, entry) | 0 | 0.25 | 25% | Starts big at scrollY=0; dissolves only (no grow-in). Out extended from 0.18. |
| `morph-ngahere` (2nd) | 0.02 | 0.70 | 68% | Fastest in + long out; holds at apex (scale 1 / op 1) over middle 30%. |
| `morph-triskelle` (3rd) | 0.40 | 0.75 | 35% | Delayed in (0.40); holds at apex. |

Animation logic (per symbol): `e = easeInOut(raw)` where `raw = (p - start)/(end - start)`; `scale`/`op` = grow (0→1 over first 35%) → hold (1 at 35–65%) → dissolve (1→0 over last 35%). Rotation `rot = 360 * e` continuous; mirrored pair negated.

---

## 3. Background / Atmosphere State (RESTORED — unchanged from `handover2.md`)

- `currentPillarKey()` uses **viewport-mid + section-top** detection (`window.scrollY + innerHeight*0.5`, compare to `getBoundingClientRect().top + scrollY`). **Not** scroll-progress.
- `pillars[]` has **no** `atmoAt` fields (cleaned out).
- Atmosphere crossfade = `style.opacity` swap between `atmo-ngahere` / `atmo-artist` / `atmo-triskelle` / `atmo-contact`; `.scroll-stage` `data-atmo` updated.
- Reduced-motion: static, but `applyPillar(currentPillarKey())` runs once.

---

## 4. File States (post-change)

| File | Role |
|---|---|
| `index.html` | Main page. Symbol-layer (`#symbol-layer`) has 3 `.scroll-symbol` divs with `data-phase-*`. JS `applySymbolAnimation()` drives them; `currentPillarKey()` restored. Contact form + scroll-spy + theme toggle intact. |
| `css/tokens.css` | Design tokens — 4 grounds (`--ground-1..4`), 4 atmospheres (`--atmos-*`), `data-theme` overrides. Unchanged this round. |
| `css/components.css` | Scroll-stage, symbol-mask styles (`-webkit-mask` / `mask`), contact validation (`is-touched`), NZ-map, FM-readout. Unchanged. |
| `css/base.css` | Global base — unchanged. |
| `radio.html` | Radio/HUD — unchanged this round. |
| `triskelle3d.html` / `ngahere-toa-*.html` / `wizard-staffs.html` / `style-guide.html` | Unchanged. |
| `HANDOVER.md` / `handover2.md` | Prior docs preserved; this is the delta. |

---

## 5. Validation / Known Behaviour (as of this write)

- `index.html` loads; `.scroll-symbol` count = 3; `data-phase-*` attributes present.
- At `scrollY = 0` (`p < 0.02`): `morph-artist` visible at `scale(1)`, `opacity=1`; others hidden (scale 0).
- As `p` increases: artist dissolves (1→0); nghahere grows (0→1) at ~2%, holds 35%→65% of its phase, dissolves; triskelle delayed to ~40%.
- Background wash changes when section crosses viewport mid (original behaviour) — **independent of symbol timing**.
- Rotation: all 3 spin same direction (visual check required — `visualRot` logic verified in code).
- Reduced-motion: no scroll-linking/parallax; background colour + symbol state set at load.

---

## 6. Open / Next (carry forward)

- [ ] **Visual verify symbol rotation** — open `http://localhost:8765/index.html`, scroll through; confirm 1st→2nd→3rd handoff and that 2nd/3rd hold at apex visibly.
- [ ] **Background colour sync check** — confirm atmosphere washes still switch at correct sections (independent of faster symbols; should be fine since `currentPillarKey()` restored).
- [ ] **Phase tuning** — user has iterated symbol phases repeatedly; document current values in this file so future edits don't lose positions.
- [ ] **Phase values locked**: artist `0→0.25`, nghahere `0.02→0.70`, triskelle `0.40→0.75`; hold-at-apex on 2nd+3rd (35% grow / 30% hold / 35% dissolve).
- [ ] **Contact form backend** — `mailto:` action (`// TODO (Phase 5)` in `index.html`) still open from `handover2.md`.
- [ ] **Accessibility / QA** — `HANDOVER.md` Phase 6 checks (contrast, keyboard nav, focus rings, `aria-current`, form `aria` labels) still open.
- [ ] `style-guide.html` — does not reference new per-pillar ground tokens (`--ground-1..4`, `--atmos-*`). May need sync.

---

## 7. Interaction Rules / Memory (current session context)

- Profile: `jeeves`. Token-conscious (`caveman` preferred for agent-ops, concise direct for human). Workspace: `/Users/admin/Documents/Obsidian/Jeeves`. Workspace repo: `/Users/admin/Documents/website-dev/mysite-local`.
- User: Matt — direct, no sycophancy, multi-disciplinary (leather/festival/embedded/3D/DJ). Visual verification required before declaring done.
- Prior symbol-phase history (this session): started opacity-only crossfade → rotate/scale → sequential windows → faster windows → hold-at-apex → 2nd faster/longer → 3rd delayed → hold-at-apex on both non-entry → 2nd kept fast/long, 1st extended out, 3rd long out → 2nd faster-in + longer-out + hold at apex; 3rd delayed-in; background restored.
- `mem0` / `memory`: user prefers concise direct responses; dislikes filler. Save durable facts only.

---

*Written at session time. Do not overwrite `HANDOVER.md` or `handover2.md` — this is the delta file for the animation/phase work only.*
