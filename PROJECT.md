# Project Tracker
## Bay Area International Food Map

---

## Status: Planning Complete — Ready to Build

---

## Milestones

| # | Milestone | Status |
|---|---|---|
| 1 | Define scope & MVP | ✅ Done |
| 2 | Architecture design | ✅ Done |
| 3 | Write PRD | ✅ Done |
| 4 | Build `index.html` | ⬜ Not started |
| 5 | SVG Bay Area map | ⬜ Not started |
| 6 | Restaurant data layer | ⬜ Not started |
| 7 | Left panel + filters | ⬜ Not started |
| 8 | Map dots + genre colors | ⬜ Not started |
| 9 | Hover card interaction | ⬜ Not started |
| 10 | Right detail panel | ⬜ Not started |
| 11 | Polish + QA | ⬜ Not started |

---

## Build Steps (Milestone 4–11 breakdown)

### Step 1 — HTML scaffold & CSS tokens
- [ ] Page shell (`<!DOCTYPE>`, `<head>`, Google Fonts)
- [ ] CSS custom properties (color tokens, typography, spacing)
- [ ] Three-panel CSS Grid layout (left 280px / center flex / right 400px)
- [ ] Basic `body` reset and typography defaults

### Step 2 — SVG Bay Area map
- [ ] Hand-crafted SVG `viewBox="0 0 800 600"`
- [ ] Water layer (San Francisco Bay, Pacific Ocean)
- [ ] Land layer (SF peninsula, East Bay, Marin, South Bay)
- [ ] City / neighborhood text labels
- [ ] Dot layer `<g class="dots">` (empty, populated by JS)
- [ ] Genre legend

### Step 3 — Data layer
- [ ] `restaurants[]` array (10 entries, 7+ genres)
- [ ] `genreColors{}` map (all 11 genres)
- [ ] `appState{}` object

### Step 4 — Left panel filters
- [ ] Masthead / wordmark
- [ ] Rating range slider + live label
- [ ] Genre pill buttons (11, color-coded, toggle)
- [ ] Select All / Clear control
- [ ] Result count display

### Step 5 — Render function
- [ ] `render()` filters restaurants by `minRating` and `activeGenres`
- [ ] Shows / hides SVG dots
- [ ] Updates result count
- [ ] Wires slider and pill events → state → `render()`

### Step 6 — Hover card
- [ ] Hover card HTML structure (inside `.panel-map`)
- [ ] `mouseenter` / `mouseleave` handlers on dots
- [ ] Populate card fields from restaurant data
- [ ] Position card near dot, clamp to viewport edges
- [ ] Fade-in / fade-out CSS transition

### Step 7 — Right detail panel
- [ ] Panel HTML structure
- [ ] `openPanel(id)` — populate fields + `translateX(0)`
- [ ] `closePanel()` — `translateX(100%)`
- [ ] "Read Review →" click handler
- [ ] × close button
- [ ] `body.panel-open` class toggle for grid reflow

### Step 8 — Polish & QA
- [ ] Test all 11 genre filters
- [ ] Test rating slider at edge values (0, 5, mixed)
- [ ] Test hover card positioning near map edges
- [ ] Test panel open/close animation
- [ ] Verify in Chrome, Firefox, Safari
- [ ] Final typography and spacing pass

---

## Decisions Log

| Date | Decision | Reason |
|---|---|---|
| Jun 2026 | Single `index.html`, no build step | Zero install friction for MVP |
| Jun 2026 | Hand-crafted SVG map, no Mapbox | No API key; more editorial control |
| Jun 2026 | Vanilla JS, no framework | ~150 lines; no overhead justified |
| Jun 2026 | Unsplash URLs for photos | Free, no auth, keyword-matched |
| Jun 2026 | Kinfolk design language | Differentiates from utility map products |
| Jun 2026 | 10 hardcoded restaurants | Enough to prove filters; fast to ship |

---

## Files

| File | Description |
|---|---|
| `PRD.md` | Product requirements document |
| `ARCHITECTURE.md` | Technical architecture reference |
| `PROJECT.md` | This file — progress tracker |
| `index.html` | Application (not yet created) |
