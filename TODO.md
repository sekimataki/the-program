# TODO
## Bay Area International Food Map

> Flat, ordered checklist synthesized from PRD, ARCHITECTURE, PLAN, and PROJECT.
> `✅` done · `🔄` in progress · `⬜` not started

---

## Phase 1 — Planning & Design ✅

- [x] Define MVP scope and non-goals
- [x] Write PRD (`PRD.md`)
- [x] Write architecture doc (`ARCHITECTURE.md`)
- [x] Write granular execution plan (`PLAN.md`)
- [x] Generate Figma UI — 3 screens (Default · Hover Card · Detail Panel)

---

## Phase 2 — HTML Scaffold & CSS Tokens ✅

- [x] Create `index.html` with `<!DOCTYPE html>`, `lang="en"`, charset, viewport meta
- [x] Add `<title>Bay Area International Food Map</title>`
- [x] Load Google Fonts: Cormorant Garamond (300, 400) + Jost (300, 400, 500)
- [x] Write CSS reset (`box-sizing`, `margin`, `padding`, `button`/`input` defaults)
- [x] Lock `html, body` to `height: 100%; overflow: hidden`
- [x] Define all CSS custom properties:
  - [x] Color tokens (`--color-paper`, `--color-sand`, `--color-stone`, `--color-ink`, `--color-muted`, `--color-accent`)
  - [x] Typography tokens (`--font-display`, `--font-ui`)
  - [x] Spacing scale (`--space-xs` through `--space-2xl`)
  - [x] Motion tokens (`--ease-panel`, `--duration-fast`, `--duration-mid`, `--duration-panel`)
- [x] CSS Grid layout: `.layout { grid-template-columns: 280px 1fr }`
- [x] `.panel-left` — `280px`, `overflow-y: auto`, right border
- [x] `.panel-map` — `flex: 1`, `position: relative`, `overflow: hidden`
- [x] `.panel-right` — `position: fixed`, `width: 400px`, `transform: translateX(100%)`, panel transition
- [x] `body.panel-open .panel-right { transform: translateX(0) }`
- [x] **Checkpoint:** open in browser → two-column layout visible, no console errors

---

## Phase 3 — SVG Bay Area Map ✅

- [x] Add `<svg class="bay-area-map" viewBox="0 0 800 600">` filling `.panel-map`
- [x] Water background rect `#C8DCE8` covering full viewBox
- [x] Land layer paths — SF peninsula, East Bay, Marin, South Bay (`fill: #E8E2D8`, `stroke: #D4CFC8`)
- [x] Golden Gate Bridge — horizontal span line + two tower verticals in `#C8946A`
- [x] Bay Bridge — thin horizontal line SF → Oakland
- [x] City labels (`SAN FRANCISCO`, `OAKLAND`, `BERKELEY`, `MARIN`, `SAN JOSE`, `PALO ALTO`) in Jost uppercase, muted
- [x] Empty `<g class="map-dots">` — populated by JS at runtime
- [x] Genre legend HTML overlay (bottom-right corner, `rgba(250,249,247,0.94)` bg, `backdrop-filter: blur(4px)`)
  - [x] 11 rows: color swatch circle + genre name
- [x] CSS: `.restaurant-dot { cursor: pointer; stroke: white; stroke-width: 1.5; transition: transform 120ms ease; transform-origin: center; }`
- [x] CSS: `.restaurant-dot:hover { transform: scale(1.4); }`
- [x] **Checkpoint:** map renders, land/water visible, bridges visible, legend in corner

---

## Phase 4 — Data Layer ✅

- [x] `const genreColors` — all 11 genre → hex mappings
- [x] `const restaurants` — 10 entries, each with:
  - [x] `id`, `name`, `genre`, `rating`, `address`
  - [x] `coords: { x, y }` (SVG coordinate space, verify placement)
  - [x] `photo` (Unsplash URL with `?w=400&q=75` size params)
  - [x] `reviewText` (2–3 sentences, editorial prose)
- [x] Covers at least 7 distinct genres across 10 entries (8 distinct genres)
- [x] `const appState = { minRating: 0, activeGenres: new Set(...), hoveredId: null, selectedId: null }`
- [x] **Checkpoint:** `console.log(restaurants.length)` → `10`, `appState.activeGenres.size` → `11`

---

## Phase 5 — Left Panel UI ✅

- [x] Masthead: "Bay Area" + "Food Map" in Cormorant Garamond, thin weight, tight tracking
- [x] Tagline in Cormorant Garamond italic, muted color
- [x] `<hr>` divider using `--color-stone`
- [x] "MINIMUM RATING" label (Jost 500, 9px, 0.12em letter-spacing)
- [x] `<input type="range">` slider (min 0, max 5, step 0.1, default 0)
- [x] Custom slider CSS: 2px track, 16px square ink thumb (no border-radius per Kinfolk)
- [x] Live rating display: ★ icon (`--color-accent`) + numeric value updates on `input` event
- [x] "CUISINE" section label
- [x] 11 `<button class="genre-pill" data-genre="...">` buttons, flex-wrap
- [x] Pills active state: genre color bg, white text
- [x] Pills inactive state: `opacity: 0.5`, sand bg
- [x] "Clear all / Select all" toggle link
- [x] Result count `.panel-footer` pushed to bottom via `margin-top: auto`
- [x] **Checkpoint:** panel renders, all 11 pills colored, slider moves and label updates

---

## Phase 6 — Render Function & Filter Logic ✅

- [x] `getVisibleRestaurants()` — pure filter by `minRating` AND `activeGenres`
- [x] `renderDots(visible)` — clears `<g class="map-dots">`, creates `<circle>` per restaurant
  - [x] Each `<circle>`: correct `cx/cy`, `r="7"`, genre fill color, `data-id`
- [x] `updateResultCount(n)` — updates footer text: `"N place(s)"`
- [x] `render()` — calls all three above in sequence
- [x] Rating slider `input` event → debounced 16ms → `appState.minRating` → `render()`
- [x] Genre pill `click` event → toggle `appState.activeGenres` → update pill style → `render()`
- [x] "Clear / Select all" click → sync `appState.activeGenres` → `render()`
- [x] Initial `render()` call on page load
- [x] **Checkpoint:** 10 dots on load; filters functional; count correct

---

## Phase 7 — Hover Card ✅

- [ ] Add `.hover-card` HTML inside `.panel-map`: photo, genre tag, name, address, rating, CTA button
- [ ] CSS default: `opacity: 0; pointer-events: none; transform: translateY(4px); position: absolute; z-index: 50;`
- [ ] CSS visible: `.hover-card.visible { opacity: 1; pointer-events: auto; transform: translateY(0); }`
- [ ] CSS transition: `opacity 150ms ease, transform 150ms ease`
- [ ] Card: `width: 240px; background: white; overflow: hidden` (no border-radius per Kinfolk)
- [ ] Photo: `height: 140px; object-fit: cover`
- [ ] Genre tag: genre color bg, white text, uppercase, pill shape
- [ ] CTA: full-width, ink bg, hover → accent bg
- [x] `showHoverCard(restaurant, dotEl)` — populate all fields, compute position, add `.visible`
- [x] Position logic: place above dot by default; clamp to map edges (top/right/left)
- [x] `hideHoverCard()` — remove `.visible`, clear `appState.hoveredId`
- [x] `mouseenter` on dot → `showHoverCard()` after 80ms delay cancellation guard
- [x] `mouseleave` on dot → 80ms delay → `hideHoverCard()` unless mouse is on card
- [x] `mouseenter` on card → cancel hide delay
- [x] `mouseleave` on card → `hideHoverCard()`
- [x] **Checkpoint:** hover shows card with photo; card stays open when mousing onto it; edge dots don't overflow

---

## Phase 8 — Right Detail Panel ✅

- [ ] Add `<aside class="panel-right" id="panel-right">` with close button, hero photo, body content
- [ ] CSS: `position: fixed; right: 0; width: 400px; height: 100vh; overflow-y: auto`
- [ ] CSS: `transform: translateX(100%); transition: transform 300ms cubic-bezier(0.4,0,0.2,1)`
- [ ] `.panel-right.open { transform: translateX(0) }`
- [ ] Hero photo: `height: 260px; object-fit: cover`
- [ ] Genre tag, restaurant name (Cormorant 28px), address, rating row
- [ ] `1px` divider, "REVIEW" label, review text (Jost 300, 14px, 1.75 line-height)
- [ ] `openPanel(id)` — find restaurant, populate all `#detail-*` fields, add `.open`, set `appState.selectedId`
- [ ] `closePanel()` — remove `.open`, `appState.selectedId = null`, clear photo src after 300ms
- [ ] CTA button click → `openPanel(id)` + `hideHoverCard()`
- [ ] × close button → `closePanel()`
- [ ] `Escape` key → `closePanel()` if panel open
- [ ] **Checkpoint:** panel slides in/out; all fields populate; escape closes; opening second restaurant updates content

---

## Phase 9 — Polish & QA ✅

- [x] Hide scrollbar on `.panel-left` (`scrollbar-width: none`, `::-webkit-scrollbar { display: none }`)
- [x] Hover card z-index above legend (z-index: 50 vs legend z-index: 10)
- [x] `-webkit-font-smoothing: antialiased` on `body`
- [x] Range input cross-browser CSS (`::-webkit-slider-thumb`, `::-moz-range-thumb`)
- [x] Rapid filter toggling — verified no dot/count desync (0 → 10 → 0 clean)
- [x] Rating slider edge values: 0 (10 dots), 4.9 (2 dots: Bini's + Maum), 5.0 (0 dots)
- [x] Clear all genres → 0 dots, "0 places"; Select all → 10 dots restored
- [x] Escape key closes panel (verified via dispatchEvent)
- [x] Filter state unchanged by panel interaction
- [x] File size: 43.2 KB (budget: 150KB) ✓

---

## Completed So Far

| Item | Status |
|---|---|
| Scope & MVP definition | ✅ |
| PRD.md | ✅ |
| ARCHITECTURE.md | ✅ |
| PLAN.md | ✅ |
| PROJECT.md | ✅ |
| TODO.md | ✅ |
| Figma UI — 3 screens | ✅ |
| `index.html` | ⬜ |
