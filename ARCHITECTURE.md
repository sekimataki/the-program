# Architecture
## Bay Area International Food Map — MVP

> Single-file static web application. No build step, no dependencies, no backend.

---

## File Structure

```
index.html          ← entire application lives here
PRD.md              ← product requirements
ARCHITECTURE.md     ← this file
```

`index.html` is organized into four sequential sections:

```
<head>
  Google Fonts import (Cormorant Garamond + Jost)
  <style> — all CSS

<body>
  .layout            ← CSS Grid root (3 columns)
    .panel-left      ← filters
    .panel-map       ← SVG map + hover card
    .panel-right     ← detail drawer

  <script>
    1. Data           — restaurants[], genreColors{}
    2. State          — appState{}
    3. Render         — render()
    4. Event binding  — sliders, pills, dots, panel close
```

---

## Layer 1 — Data

### `restaurants[]`

Array of plain objects. All content is hardcoded. No fetch calls except Unsplash photo URLs.

```js
const restaurants = [
  {
    id:         "the-bird",
    name:       "The Bird",
    genre:      "East Asian",
    rating:     4.8,
    address:    "SoMa, San Francisco",
    coords:     { x: 210, y: 310 },   // SVG canvas coordinates
    photo:      "https://source.unsplash.com/400x300/?ramen",
    reviewText: "A bowl of tonkotsu that quietly rewrites what you thought ramen could be..."
  },
  // ...9 more
]
```

### `genreColors{}`

Maps each of the 11 genre keys to a hex color. Single source of truth consumed by dots, pills, legend, and tags.

```js
const genreColors = {
  "East Asian":            "#C9845A",
  "Southeast Asian":       "#7BA05B",
  "South Asian":           "#E8A838",
  "Central & West Asian":  "#9B6B9B",
  "Middle Eastern":        "#D4956A",
  "North African":         "#C4863C",
  "West & Central African":"#6B8E6B",
  "East & Southern African":"#8B6B4A",
  "Latin American":        "#D46B6B",
  "European":              "#6B8BAE",
  "Oceania":               "#5BA0A0",
}
```

---

## Layer 2 — State

A single object. **Nothing mutates the DOM directly** — all changes go through `appState` first, then `render()`.

```js
const appState = {
  minRating:    0,               // number: 0.0 – 5.0
  activeGenres: new Set(         // Set<string>: genres currently shown
    Object.keys(genreColors)
  ),
  hoveredId:    null,            // string | null: dot currently hovered
  selectedId:   null,            // string | null: restaurant open in drawer
}
```

### State mutation rules

| Event | Mutation | Triggers |
|---|---|---|
| Slider `input` | `appState.minRating = value` | `render()` |
| Genre pill click | `appState.activeGenres.add/delete(genre)` | `render()` |
| Dot `mouseenter` | `appState.hoveredId = id` | direct DOM (hover card) |
| Dot `mouseleave` | `appState.hoveredId = null` | direct DOM (hover card) |
| "Read Review" click | `appState.selectedId = id` | `openPanel(id)` |
| Panel × close | `appState.selectedId = null` | `closePanel()` |

---

## Layer 3 — Render

`render()` is the **only function that reads `appState` to update the map**. It is called after every state mutation that affects dot visibility (rating or genre filters). Hover and panel interactions bypass `render()` for performance — they update the DOM directly.

```
render()
  ├── filter restaurants[] by appState.minRating
  ├── filter by appState.activeGenres
  ├── for each visible restaurant:
  │     show dot (SVG <circle>) with genre color
  ├── for each hidden restaurant:
  │     hide dot
  └── update result count label
```

### What render() does NOT do

- It does not touch the hover card (managed by mouseenter/mouseleave handlers).
- It does not touch the right panel (managed by openPanel / closePanel).
- It does not recreate dots on every call — dots exist in the SVG and are shown/hidden via `visibility` or `opacity`.

---

## Layer 4 — UI Components

### Left Panel

```
.panel-left
  ├── .masthead           ← wordmark / site title
  ├── .filter-rating
  │     ├── <label>       ← "Minimum Rating"
  │     ├── <input type="range">
  │     └── .rating-value ← live numeric display
  ├── .filter-genres
  │     ├── .genre-pill × 11
  │     └── .filter-reset ← "Select All / Clear"
  └── .result-count       ← "8 places"
```

### Map Panel

```
.panel-map
  ├── <svg class="bay-area-map">
  │     ├── <g class="water">      ← bay + ocean fills
  │     ├── <g class="land">       ← land mass paths
  │     ├── <g class="labels">     ← city text labels
  │     ├── <g class="dots">       ← one <circle> per restaurant
  │     └── <g class="legend">     ← color swatch + genre label × 11
  └── .hover-card                  ← position: absolute, initially hidden
        ├── .hover-photo
        ├── .hover-name
        ├── .hover-address
        ├── .hover-rating
        ├── .hover-genre-tag
        └── .hover-cta             ← "Read Review →"
```

### Right Detail Panel

```
.panel-right                       ← position: fixed, right: 0
                                     transform: translateX(100%) default
                                     transform: translateX(0) when open
  ├── .panel-close                 ← × button
  ├── .detail-photo                ← full-width hero image
  ├── .detail-name
  ├── .detail-genre-tag
  ├── .detail-address
  ├── .detail-rating
  └── .detail-review               ← editorial review text
```

---

## Layer 5 — CSS Architecture

All styles live in a single `<style>` block in `<head>`. No external stylesheet.

### Custom properties (design tokens)

```css
:root {
  /* Palette */
  --color-paper:   #FAF9F7;
  --color-sand:    #F0ECE4;
  --color-stone:   #E0DDD8;
  --color-ink:     #1A1814;
  --color-muted:   #8A8478;
  --color-accent:  #C8946A;

  /* Typography */
  --font-display:  'Cormorant Garamond', serif;
  --font-ui:       'Jost', sans-serif;

  /* Spacing scale */
  --space-xs:  4px;
  --space-sm:  8px;
  --space-md:  16px;
  --space-lg:  24px;
  --space-xl:  40px;

  /* Motion */
  --ease-panel: cubic-bezier(0.4, 0, 0.2, 1);
}
```

### Layout

```css
body {
  display: grid;
  grid-template-columns: 280px 1fr 0px;  /* right panel off-screen */
  height: 100vh;
  overflow: hidden;
}

/* When panel is open, JS adds .panel-open to body */
body.panel-open {
  grid-template-columns: 280px 1fr 400px;
}
```

### Transitions

| Element | Property | Duration | Easing |
|---|---|---|---|
| `.panel-right` | `transform` | 300ms | `var(--ease-panel)` |
| `.hover-card` | `opacity`, `transform` | 150ms | `ease` |
| `.dot` (SVG circle) | `transform` | 120ms | `ease` |
| `.genre-pill` | `background-color`, `color` | 100ms | `ease` |

---

## Event Flow Diagram

```
User action
    │
    ├─ Slider input ──────────────→ appState.minRating = val
    │                                        │
    ├─ Pill click ────────────────→ appState.activeGenres.toggle()
    │                                        │
    │                                   render()
    │                                        │
    │                               filter restaurants[]
    │                               show/hide SVG dots
    │                               update result count
    │
    ├─ Dot mouseenter ────────────→ position hover card
    │                               populate hover card fields
    │                               set opacity: 1
    │
    ├─ Dot mouseleave ────────────→ set opacity: 0
    │
    ├─ "Read Review" click ───────→ appState.selectedId = id
    │                               populate panel fields
    │                               panel: translateX(0)
    │                               body: add .panel-open
    │
    └─ Panel × close ─────────────→ panel: translateX(100%)
                                    body: remove .panel-open
                                    appState.selectedId = null
```

---

## SVG Map Coordinate System

The Bay Area SVG uses a fixed `viewBox="0 0 800 600"`. Restaurant coordinates are manually placed and stored as `{ x, y }` in the data layer.

```
(0,0) ─────────────────────────── (800,0)
  │                                   │
  │   Marin ~(200,80)                 │
  │   SF ~(160,280)                   │
  │   Oakland ~(360,260)              │
  │   Berkeley ~(340,200)             │
  │   San Jose ~(480,480)             │
  │                                   │
(0,600) ────────────────────────(800,600)
```

Dots are `<circle cx={x} cy={y} r="7">` elements inside `<g class="dots">`. Their `fill` is set from `genreColors[restaurant.genre]` at render time.

---

## Performance Notes

- `render()` never creates or destroys DOM nodes — it only toggles visibility on pre-existing dot elements.
- Hover card population is synchronous string assignment (`innerHTML` or `textContent`). No virtual DOM, no diffing.
- All SVG map paths are static. Only the dot layer changes.
- Photos are lazy-loaded via native `loading="lazy"` on `<img>` elements.

---

## Adding a New Restaurant

1. Add an entry to `restaurants[]` in `index.html`.
2. Set `coords: { x, y }` by referencing the SVG coordinate system above.
3. Pick a `genre` from the 11 keys in `genreColors`.
4. Use a descriptive Unsplash keyword in the `photo` URL.
5. Reload `index.html` in the browser. No build step required.
