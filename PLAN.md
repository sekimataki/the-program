# Execution Plan
## Bay Area International Food Map — MVP

> Granular task breakdown. Every item is a discrete, testable unit of work.
> Update status: `⬜ todo` → `🔄 in progress` → `✅ done`

---

## Step 1 — HTML Scaffold & CSS Tokens

### 1.1 Page shell
- [ ] `<!DOCTYPE html>` with `lang="en"`
- [ ] `<meta charset="UTF-8">` and `<meta name="viewport" content="width=device-width, initial-scale=1.0">`
- [ ] `<title>Bay Area International Food Map</title>`
- [ ] Google Fonts `<link>` importing Cormorant Garamond (weights 300, 400, 600) and Jost (weights 300, 400, 500)
- [ ] Empty `<style>` block in `<head>` — all CSS goes here
- [ ] Empty `<script>` block at bottom of `<body>` — all JS goes here

### 1.2 CSS reset & base
- [ ] `*, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }`
- [ ] `html, body { height: 100%; overflow: hidden; }` — prevent page scroll; map fills viewport
- [ ] `body { background: var(--color-paper); color: var(--color-ink); font-family: var(--font-ui); }`
- [ ] Remove default `<button>` and `<input>` browser styles

### 1.3 CSS custom properties (design tokens)
- [ ] Palette tokens:
  ```
  --color-paper:   #FAF9F7
  --color-sand:    #F0ECE4
  --color-stone:   #E0DDD8
  --color-ink:     #1A1814
  --color-muted:   #8A8478
  --color-accent:  #C8946A
  --color-accent-light: #F0E4D8
  ```
- [ ] Typography tokens:
  ```
  --font-display: 'Cormorant Garamond', Georgia, serif
  --font-ui:      'Jost', system-ui, sans-serif
  ```
- [ ] Spacing scale:
  ```
  --space-xs: 4px
  --space-sm: 8px
  --space-md: 16px
  --space-lg: 24px
  --space-xl: 40px
  --space-2xl: 64px
  ```
- [ ] Radius tokens:
  ```
  --radius-sm: 4px
  --radius-md: 8px
  --radius-pill: 999px
  ```
- [ ] Shadow tokens:
  ```
  --shadow-card: 0 4px 24px rgba(26,24,20,0.10)
  --shadow-hover: 0 8px 32px rgba(26,24,20,0.14)
  ```
- [ ] Motion tokens:
  ```
  --ease-panel: cubic-bezier(0.4, 0, 0.2, 1)
  --duration-fast: 120ms
  --duration-mid: 150ms
  --duration-panel: 300ms
  ```

### 1.4 Three-panel CSS Grid layout
- [ ] `.layout` on `<body>` or a wrapper `<div>`:
  ```
  display: grid;
  grid-template-columns: 280px 1fr;
  grid-template-rows: 100vh;
  height: 100vh;
  overflow: hidden;
  ```
- [ ] `.panel-left` — `overflow-y: auto; height: 100vh;`
- [ ] `.panel-map` — `position: relative; overflow: hidden;`
- [ ] `.panel-right` — `position: fixed; top: 0; right: 0; width: 400px; height: 100vh; transform: translateX(100%); transition: transform var(--duration-panel) var(--ease-panel); z-index: 100;`
- [ ] `body.panel-open .panel-right { transform: translateX(0); }`

### 1.5 Checkpoint
- [ ] Open `index.html` in browser — see a two-column page (left gray strip, map area)
- [ ] No console errors

---

## Step 2 — SVG Bay Area Map

### 2.1 SVG canvas setup
- [ ] `<svg class="bay-area-map" viewBox="0 0 800 600" xmlns="http://www.w3.org/2000/svg">` fills `.panel-map`
- [ ] `width: 100%; height: 100%;` on the SVG element
- [ ] `preserveAspectRatio="xMidYMid slice"` to cover the panel without distortion

### 2.2 Water layer `<g class="map-water">`
- [ ] Background rect covering full viewBox in ocean color `#C8DCE8` (muted blue-gray)
- [ ] San Francisco Bay polygon (rough outline covering the bay between SF, Oakland, and South Bay)
- [ ] Pacific Ocean implied by the background color behind the peninsula

### 2.3 Land layer `<g class="map-land">`
- [ ] San Francisco peninsula path (roughly correct shape, stylized not precise)
- [ ] East Bay / Oakland / Berkeley land mass
- [ ] Marin headlands (north of Golden Gate)
- [ ] South Bay (San Jose / Palo Alto area) extending south
- [ ] All land fills use `#E8E2D8` (warm off-white) with `stroke: #D4CFC8` subtle border

### 2.4 Landmark details `<g class="map-details">`
- [ ] Golden Gate Bridge — two short vertical lines + a horizontal span line in `#C8946A`
- [ ] Bay Bridge — thin horizontal line connecting SF to Oakland
- [ ] Both bridges use `stroke-width: 2` and a warm accent color

### 2.5 City & neighborhood labels `<g class="map-labels">`
- [ ] San Francisco — larger label, centered on peninsula
- [ ] Oakland — East Bay label
- [ ] Berkeley — above Oakland
- [ ] Marin — north of Golden Gate
- [ ] San Jose — south end of map
- [ ] Palo Alto / Peninsula — mid-south
- [ ] All labels: `font-family: var(--font-ui)` `font-size: 11px` `fill: #8A8478` `letter-spacing: 0.08em` `text-transform: uppercase`
- [ ] Major labels (SF, Oakland): `font-size: 13px` `fill: #5A5750`

### 2.6 Restaurant dot layer `<g class="map-dots">`
- [ ] Empty `<g>` — dots injected by `render()` at runtime
- [ ] Each dot will be `<circle class="restaurant-dot" cx="{x}" cy="{y}" r="7" fill="{genreColor}" data-id="{id}">`
- [ ] CSS for `.restaurant-dot`: `cursor: pointer; stroke: white; stroke-width: 1.5; transition: transform var(--duration-fast) ease; transform-origin: center;`
- [ ] CSS hover: `.restaurant-dot:hover { transform: scale(1.4); }`

### 2.7 Genre legend `<g class="map-legend">` or HTML overlay
- [ ] Position: bottom-left corner of map panel, `position: absolute`
- [ ] Title label: "Cuisine" in small caps
- [ ] One row per genre: colored circle swatch (12px) + genre name label
- [ ] Rendered as HTML `<div class="legend">` over the SVG for easier styling
- [ ] Legend container: `background: rgba(250,249,247,0.92)` `backdrop-filter: blur(4px)` `border-radius: var(--radius-md)` `padding: var(--space-md)`

### 2.8 Checkpoint
- [ ] Map renders with visible land/water distinction
- [ ] Bridge landmarks visible
- [ ] City labels readable
- [ ] Genre legend visible in corner

---

## Step 3 — Data Layer

### 3.1 `genreColors` object
- [ ] Define all 11 genre → hex mappings as a `const` at top of `<script>`
- [ ] Verify all 11 keys exactly match the genre strings used in `restaurants[]`

### 3.2 `restaurants[]` array — 10 entries
Each entry must include all 8 schema fields: `id`, `name`, `genre`, `rating`, `address`, `coords`, `photo`, `reviewText`.

- [ ] **#1** — East Asian (e.g. Japanese ramen, SF)
- [ ] **#2** — Southeast Asian (e.g. Vietnamese pho, Oakland)
- [ ] **#3** — South Asian (e.g. Indian curry, Fremont)
- [ ] **#4** — Middle Eastern (e.g. Lebanese mezze, Berkeley)
- [ ] **#5** — Latin American (e.g. Peruvian ceviche, Mission SF)
- [ ] **#6** — West & Central African (e.g. Senegalese, Oakland)
- [ ] **#7** — European (e.g. French bistro, SF)
- [ ] **#8** — North African (e.g. Moroccan, SF)
- [ ] **#9** — South Asian second entry OR East & Southern African (e.g. Ethiopian, Oakland)
- [ ] **#10** — Central & West Asian OR Oceania

For each entry:
- [ ] `coords` manually placed on SVG coordinate grid (verify dot appears in correct region)
- [ ] `photo` URL uses a descriptive Unsplash keyword (`?ramen`, `?vietnamese-food`, etc.)
- [ ] `reviewText` is 2–3 sentences of editorial-quality prose (not filler lorem ipsum)

### 3.3 `appState` object
- [ ] `minRating: 0`
- [ ] `activeGenres: new Set(Object.keys(genreColors))`
- [ ] `hoveredId: null`
- [ ] `selectedId: null`

### 3.4 Checkpoint
- [ ] `console.log(restaurants.length)` → `10`
- [ ] `console.log(appState.activeGenres.size)` → `11`

---

## Step 4 — Left Panel UI

### 4.1 HTML structure
- [ ] `.panel-left` contains three sections:
  ```
  .panel-masthead
  .panel-filters
  .panel-footer (result count)
  ```
- [ ] `.panel-left` styles: `background: var(--color-paper)` `border-right: 1px solid var(--color-stone)` `padding: var(--space-xl) var(--space-lg)` `display: flex; flex-direction: column; gap: var(--space-xl);`

### 4.2 Masthead
- [ ] Site title in Cormorant Garamond: "Bay Area" (large, thin weight) + line break + "Food Map" (slightly smaller)
- [ ] Subtitle tagline: "International cuisine, mapped." in Jost 300 muted color
- [ ] Thin horizontal rule `<hr>` below masthead using `--color-stone`

### 4.3 Rating filter section
- [ ] Section label: "MINIMUM RATING" — Jost 500, 10px, letter-spacing 0.1em, `--color-muted`
- [ ] `<input type="range" id="rating-slider" min="0" max="5" step="0.1" value="0">`
- [ ] Custom slider track: `height: 2px; background: --color-stone; border-radius: pill`
- [ ] Custom slider thumb: `width: 16px; height: 16px; background: --color-ink; border-radius: 50%; border: none;`
- [ ] Rating display row: star icon (★) + live numeric value (e.g. "4.2+") right-aligned
- [ ] Star icon color: `--color-accent`
- [ ] Live value updates as slider moves — no render() call needed here, just DOM update

### 4.4 Genre filter section
- [ ] Section label: "CUISINE" — same style as rating label
- [ ] Genre pills container: `display: flex; flex-wrap: wrap; gap: var(--space-xs);`
- [ ] 11 `<button class="genre-pill" data-genre="{genre}">` buttons
- [ ] Each pill default state: `background: var(--color-sand); color: var(--color-ink); border: 1px solid var(--color-stone); border-radius: var(--radius-pill); padding: 4px 10px; font-size: 11px; font-family: var(--font-ui); cursor: pointer; transition: all 100ms ease;`
- [ ] Each pill active state: `background: {genreColor}; color: white; border-color: {genreColor};` — applied via inline style + `.active` class
- [ ] Each pill inactive (filtered out) state: `opacity: 0.35; background: var(--color-sand); color: var(--color-muted);`
- [ ] Pills are all active by default on load
- [ ] "Clear all" / "Select all" toggle link below pills: `font-size: 11px; color: var(--color-muted); text-decoration: underline; cursor: pointer;`

### 4.5 Result count
- [ ] `.panel-footer` pushed to bottom via `margin-top: auto`
- [ ] Display: "12 places" — Cormorant Garamond italic, 18px, `--color-muted`
- [ ] Updates when `render()` runs

### 4.6 Checkpoint
- [ ] Left panel renders with masthead, slider, 11 genre pills, result count
- [ ] All 11 pills are visible and colored
- [ ] Slider moves and numeric label updates

---

## Step 5 — Render Function & Filter Logic

### 5.1 `getVisibleRestaurants()`
- [ ] Pure function, no side effects
- [ ] Filters `restaurants[]` by `r.rating >= appState.minRating`
- [ ] Filters by `appState.activeGenres.has(r.genre)`
- [ ] Returns filtered array

### 5.2 `renderDots(visible)`
- [ ] Clears all existing children from `<g class="map-dots">`
- [ ] For each restaurant in `visible`:
  - [ ] Creates `<circle>` with correct `cx`, `cy`, `r="7"`, `fill`, `data-id`
  - [ ] Appends to dot layer
  - [ ] Attaches `mouseenter` and `mouseleave` handlers (see Step 6)
  - [ ] Attaches `click` handler for "Read Review" shortcut (see Step 7)

### 5.3 `updateResultCount(count)`
- [ ] Updates `.panel-footer` text: `"${count} place${count !== 1 ? 's' : ''}"`

### 5.4 `render()`
- [ ] Calls `getVisibleRestaurants()`
- [ ] Calls `renderDots(visible)`
- [ ] Calls `updateResultCount(visible.length)`

### 5.5 Rating slider event binding
- [ ] `input` event on `#rating-slider`
- [ ] Updates live label display
- [ ] Sets `appState.minRating = parseFloat(e.target.value)`
- [ ] Calls `render()`

### 5.6 Genre pill event binding
- [ ] `click` event on each `.genre-pill`
- [ ] Reads `data-genre` attribute
- [ ] Toggles genre in/out of `appState.activeGenres`
- [ ] Toggles `.active` class and inline color styles on the pill
- [ ] Updates "Clear / Select all" link text based on current state
- [ ] Calls `render()`

### 5.7 "Clear / Select all" binding
- [ ] If any genre is inactive → clicking selects all → all pills `.active`
- [ ] If all genres active → clicking clears all → all pills inactive
- [ ] Syncs `appState.activeGenres` accordingly
- [ ] Calls `render()`

### 5.8 Initial render on page load
- [ ] Call `render()` once at the bottom of `<script>` after all bindings

### 5.9 Checkpoint
- [ ] On load, all 10 dots appear on map in correct positions
- [ ] Moving slider to 4.5 hides lower-rated restaurants
- [ ] Clicking a genre pill hides/shows that genre's dots
- [ ] Result count updates correctly in all filter combinations
- [ ] Clearing all genres shows "0 places" and empty map

---

## Step 6 — Hover Card

### 6.1 HTML structure
```html
<div class="hover-card" id="hover-card">
  <div class="hover-card-photo">
    <img id="hover-photo" src="" alt="" loading="lazy">
  </div>
  <div class="hover-card-body">
    <span class="hover-genre-tag" id="hover-genre"></span>
    <h3 class="hover-name" id="hover-name"></h3>
    <p class="hover-address" id="hover-address"></p>
    <div class="hover-rating">
      <span class="star">★</span>
      <span id="hover-rating-value"></span>
    </div>
    <button class="hover-cta" id="hover-cta">Read Review →</button>
  </div>
</div>
```
- [ ] Placed inside `.panel-map` as a direct child
- [ ] `position: absolute; z-index: 50; pointer-events: auto;`

### 6.2 Hover card CSS
- [ ] Default state: `opacity: 0; pointer-events: none; transform: translateY(4px);`
- [ ] Visible state (`.hover-card.visible`): `opacity: 1; pointer-events: auto; transform: translateY(0);`
- [ ] Transition: `opacity var(--duration-mid) ease, transform var(--duration-mid) ease`
- [ ] Card container: `background: white; border-radius: var(--radius-md); box-shadow: var(--shadow-hover); width: 240px; overflow: hidden;`
- [ ] Photo: `width: 100%; height: 140px; object-fit: cover;`
- [ ] Body padding: `var(--space-md)`
- [ ] Genre tag: `font-size: 10px; letter-spacing: 0.08em; text-transform: uppercase; padding: 3px 8px; border-radius: var(--radius-pill); color: white; display: inline-block; margin-bottom: var(--space-xs);`
- [ ] Restaurant name: `font-family: var(--font-display); font-size: 18px; font-weight: 400; margin-bottom: var(--space-xs);`
- [ ] Address: `font-size: 12px; color: var(--color-muted); margin-bottom: var(--space-sm);`
- [ ] Rating: `font-size: 13px; font-weight: 500; color: var(--color-ink);` star in `--color-accent`
- [ ] CTA button: `width: 100%; margin-top: var(--space-sm); padding: var(--space-sm) var(--space-md); background: var(--color-ink); color: white; border: none; border-radius: var(--radius-sm); font-family: var(--font-ui); font-size: 12px; letter-spacing: 0.05em; cursor: pointer; text-align: center;`
- [ ] CTA hover: `background: var(--color-accent);` with `transition: background 100ms ease`

### 6.3 `showHoverCard(restaurant, dotElement)`
- [ ] Populate `#hover-photo` `src` and `alt`
- [ ] Populate `#hover-name` `textContent`
- [ ] Populate `#hover-address` `textContent`
- [ ] Populate `#hover-rating-value` `textContent`
- [ ] Populate `#hover-genre` `textContent` + `background` from `genreColors`
- [ ] Set `#hover-cta` `data-id` attribute to `restaurant.id`
- [ ] Compute position: get dot's bounding rect relative to `.panel-map`; place card above or below depending on available space
- [ ] Add `.visible` class to `#hover-card`

### 6.4 `hideHoverCard()`
- [ ] Remove `.visible` class from `#hover-card`
- [ ] Set `appState.hoveredId = null`

### 6.5 Dot event binding (inside `renderDots`)
- [ ] `mouseenter` on each dot → `showHoverCard(restaurant, dot)`; `appState.hoveredId = id`
- [ ] `mouseleave` on each dot → start a short 80ms delay, then `hideHoverCard()` if mouse is not over `.hover-card`
- [ ] `mouseenter` on `.hover-card` → cancel the hide delay
- [ ] `mouseleave` on `.hover-card` → `hideHoverCard()`

### 6.6 Hover card positioning logic
- [ ] Get `dot.getBoundingClientRect()` and `.panel-map.getBoundingClientRect()`
- [ ] Calculate dot's local `x, y` within the panel
- [ ] Default: place card 16px above the dot, horizontally centered
- [ ] If card would overflow top of panel → place below dot instead
- [ ] If card would overflow right edge → shift left
- [ ] If card would overflow left edge → shift right
- [ ] Apply as `left` and `top` style values

### 6.7 Checkpoint
- [ ] Hovering a dot shows the card with all fields populated correctly
- [ ] Card photo loads (Unsplash)
- [ ] Genre tag has correct background color
- [ ] Moving mouse off dot hides the card
- [ ] Moving mouse from dot onto the card keeps it visible
- [ ] Card does not overflow the map panel at edge dots

---

## Step 7 — Right Detail Panel

### 7.1 HTML structure
```html
<aside class="panel-right" id="panel-right">
  <button class="panel-close" id="panel-close">×</button>
  <div class="detail-photo-wrap">
    <img class="detail-photo" id="detail-photo" src="" alt="" loading="lazy">
  </div>
  <div class="detail-body">
    <span class="detail-genre-tag" id="detail-genre"></span>
    <h2 class="detail-name" id="detail-name"></h2>
    <div class="detail-meta">
      <span class="detail-address" id="detail-address"></span>
      <div class="detail-rating">
        <span class="star">★</span>
        <span id="detail-rating-value"></span>
      </div>
    </div>
    <div class="detail-divider"></div>
    <p class="detail-review" id="detail-review"></p>
  </div>
</aside>
```

### 7.2 Panel CSS
- [ ] `position: fixed; top: 0; right: 0; width: 400px; height: 100vh; z-index: 100;`
- [ ] `background: var(--color-paper); border-left: 1px solid var(--color-stone);`
- [ ] `transform: translateX(100%); transition: transform var(--duration-panel) var(--ease-panel);`
- [ ] `overflow-y: auto; display: flex; flex-direction: column;`
- [ ] `.panel-right.open { transform: translateX(0); }`

### 7.3 Panel interior CSS
- [ ] Close button: `position: absolute; top: var(--space-md); right: var(--space-md); background: none; border: none; font-size: 20px; color: var(--color-muted); cursor: pointer; line-height: 1; z-index: 10;`
- [ ] Close button hover: `color: var(--color-ink);`
- [ ] Hero photo wrapper: `width: 100%; height: 260px; overflow: hidden; flex-shrink: 0;`
- [ ] Hero photo: `width: 100%; height: 100%; object-fit: cover;`
- [ ] `.detail-body`: `padding: var(--space-xl) var(--space-lg); display: flex; flex-direction: column; gap: var(--space-md);`
- [ ] Genre tag: same style as hover card genre tag
- [ ] Name: `font-family: var(--font-display); font-size: 28px; font-weight: 400; line-height: 1.2;`
- [ ] Meta row: address + rating side by side
- [ ] Divider: `height: 1px; background: var(--color-stone); margin: var(--space-sm) 0;`
- [ ] Review text: `font-family: var(--font-ui); font-size: 14px; font-weight: 300; line-height: 1.75; color: var(--color-ink);`

### 7.4 `openPanel(id)`
- [ ] Find restaurant in `restaurants[]` by `id`
- [ ] Populate all `#detail-*` elements
- [ ] Set genre tag `background` from `genreColors`
- [ ] Add `.open` class to `#panel-right`
- [ ] Set `appState.selectedId = id`

### 7.5 `closePanel()`
- [ ] Remove `.open` class from `#panel-right`
- [ ] Set `appState.selectedId = null`
- [ ] After transition ends (300ms), clear photo `src` to stop any loading

### 7.6 Event binding
- [ ] `#panel-close` click → `closePanel()`
- [ ] `#hover-cta` click → `openPanel(data-id)` + `hideHoverCard()`
- [ ] Dot click (as fallback to hover CTA) → `openPanel(id)`
- [ ] Pressing `Escape` key → `closePanel()` if panel is open

### 7.7 Checkpoint
- [ ] Clicking "Read Review" on hover card opens the panel
- [ ] Panel slides in smoothly (300ms)
- [ ] All fields populated correctly
- [ ] × button closes the panel
- [ ] Panel slides out on close
- [ ] Escape key closes the panel
- [ ] Opening panel for a second restaurant updates all fields correctly

---

## Step 8 — Polish & QA

### 8.1 Visual polish
- [ ] Left panel scrollbar hidden on overflow (use `scrollbar-width: none` + `::-webkit-scrollbar`)
- [ ] Map SVG has subtle inner shadow or vignette at edges for depth
- [ ] Dot z-order: hovered dot renders on top (use `raise()` or reorder in DOM)
- [ ] Hover card appears above legend (z-index check)
- [ ] Right panel has a subtle left-edge shadow when open
- [ ] All text is properly anti-aliased (`-webkit-font-smoothing: antialiased`)
- [ ] Rating slider styled consistently across Chrome, Firefox, Safari (requires vendor prefixes)

### 8.2 Interaction edge cases
- [ ] Rapid filter toggling does not cause dot/count desync
- [ ] Hover card does not flicker when moving between nearby dots
- [ ] Opening panel while another is already open replaces content correctly
- [ ] Rating slider at 0 shows all restaurants regardless of genre filter
- [ ] Clearing all genre pills shows 0 dots and "0 places"
- [ ] "Select All" after clearing restores all dots

### 8.3 Content review
- [ ] All 10 restaurant names are plausible and readable
- [ ] All 10 addresses reference real Bay Area neighborhoods
- [ ] All review texts are editorial in tone (not lorem ipsum)
- [ ] All Unsplash photo URLs return relevant food images
- [ ] All coords place dots in geographically correct regions of the SVG

### 8.4 Cross-browser QA
- [ ] Chrome — full interaction walkthrough
- [ ] Firefox — verify slider styling, SVG rendering, transitions
- [ ] Safari — verify `backdrop-filter`, font rendering, CSS transitions

### 8.5 Final typography pass
- [ ] Cormorant Garamond loaded and rendering at correct weights
- [ ] Jost loaded and rendering at correct weights
- [ ] No font fallback visible (system-ui should not appear)
- [ ] Line heights feel editorial, not cramped

### 8.6 Completion
- [ ] All 10 restaurants visible on fresh load
- [ ] Full interaction loop works: filter → hover → read review → close
- [ ] File opens by double-clicking in macOS Finder (no server needed)
- [ ] File size under 150KB (excluding external Unsplash images)
