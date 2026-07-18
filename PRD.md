# Product Requirements Document
## Bay Area International Food Map

> A curated, editorial-style discovery experience for international cuisine across the Bay Area.

| Field | Value |
|---|---|
| Version | 1.0 — MVP |
| Status | Draft |
| Author | Product & Design |
| Date | June 2026 |
| Platform | Web — Desktop (single HTML file, no build step) |

---

## 1. Overview

The Bay Area International Food Map is a static, editorial-style web experience that helps food-curious people discover and explore international restaurants by cuisine origin. It is built as a single HTML file with no dependencies, designed to feel like a beautifully curated magazine rather than a utility directory.

The MVP is scoped to a desktop browser experience using hardcoded restaurant data and a hand-drawn SVG map. It validates the core interaction model before any backend or live data integration.

---

## 2. Problem Statement

People interested in trying international cuisine face two friction points:

- **Discovery is fragmented** across Yelp, Google Maps, and word-of-mouth. There is no single place that groups restaurants by cultural origin.
- **Existing tools optimize for utility** (ratings, distance, hours) and lack editorial context, making it hard to understand what a cuisine is before choosing a restaurant.

The Bay Area is one of the most culinarily diverse regions in the world. This product surfaces that diversity in a way that is approachable, visually engaging, and geographically grounded.

---

## 3. Goals & Non-Goals

### 3.1 Goals (MVP)

- Allow users to visually explore Bay Area restaurants by cuisine genre on a stylized map.
- Support filtering by Google rating and cuisine genre simultaneously.
- Deliver a hover + detail drawer interaction that surfaces rich editorial context per restaurant.
- Establish a Kinfolk-inspired visual design language that differentiates this from utility maps.

### 3.2 Non-Goals (MVP)

- Live Google Maps or Mapbox integration.
- Real-time Google Reviews API data.
- User accounts, bookmarks, or personalization.
- Mobile or tablet layouts.
- Backend, CMS, or database of any kind.
- More than 10 hardcoded sample restaurants.

---

## 4. Target User

**Primary persona: The Curious Explorer**

| Attribute | Description |
|---|---|
| Age | 25–40 |
| Location | San Francisco, Oakland, Berkeley, or South Bay |
| Motivation | Wants to try something genuinely new, not just a trendy restaurant |
| Frustration | Yelp surfaces the same 10 popular spots; no cultural context |
| Behavior | Researches before dining out; values editorial recommendations |
| Device | Desktop browser, likely during planning phase (not in-the-moment) |

---

## 5. Feature Requirements

### 5.1 Page Layout

Three-panel CSS Grid:

| Panel | Width | Contents |
|---|---|---|
| Left | 280px fixed | Masthead, filters, result count |
| Center | flex grow | SVG map + floating hover card |
| Right | 400px (off-screen default) | Detail drawer, slides in on selection |

### 5.2 SVG Map

- Hand-crafted SVG illustration of the Bay Area — no map API or tile service.
- Renders land fills, water bodies, major city and neighborhood labels.
- Each restaurant is a colored circle whose fill matches its cuisine genre.
- Restaurant dot positions are manually placed by `(x, y)` coordinate on the SVG canvas.
- Dots scale slightly on hover via CSS transition.

### 5.3 Left Panel — Filters

**Rating Filter**
- Input: range slider (0.0 – 5.0, step 0.1). Default: 0 (show all).
- Live numeric label shows current minimum.
- On change → `appState.minRating = val` → `render()`.

**Cuisine Genre Filter**
- Input: multi-select pill buttons, one per genre. All 11 active by default.
- Each pill uses its genre color as the active background.
- Clicking toggles → `appState.activeGenres` → `render()`.
- "Select All / Clear" affordance for bulk reset.

**Result Count**
- Live count of visible restaurants after filters (e.g., "8 places").

### 5.4 Hover Card

Triggered by `mouseenter` on a dot; dismissed by `mouseleave`.

| Field | Detail |
|---|---|
| Food photo | Unsplash URL, keyword-matched to cuisine |
| Restaurant name | Display heading |
| Address | Neighborhood + city |
| Google rating | Star icon + numeric value |
| Cuisine genre tag | Color-matched pill |
| CTA | "Read Review →" button |

Positioned absolutely within the map panel; clamps to avoid viewport overflow.

### 5.5 Right Detail Panel

Opens on "Read Review →" click. Slides in from off-screen (CSS transform). Closed by × button.

Contents (superset of hover card):
- Full-width hero food photo
- Restaurant name + cuisine genre tag
- Address + Google rating
- Editorial review text (1–2 paragraphs, hardcoded)

### 5.6 Cuisine Genre Color System

| # | Genre | Cultural Region | Color | Hex |
|---|---|---|---|---|
| 1 | East Asian | China, Japan, Korea, Taiwan | Terracotta | `#C9845A` |
| 2 | Southeast Asian | Thailand, Vietnam, Philippines | Sage green | `#7BA05B` |
| 3 | South Asian | India, Pakistan, Sri Lanka, Nepal | Saffron | `#E8A838` |
| 4 | Central & West Asian | Kazakhstan, Uzbekistan, Georgia | Dusty purple | `#9B6B9B` |
| 5 | Middle Eastern | Lebanon, Iran, Israel, Turkey | Warm sand | `#D4956A` |
| 6 | North African | Morocco, Tunisia, Egypt | Amber | `#C4863C` |
| 7 | West & Central African | Nigeria, Senegal, Ghana | Forest | `#6B8E6B` |
| 8 | East & Southern African | Ethiopia, Kenya, South Africa | Umber | `#8B6B4A` |
| 9 | Latin American | Mexico, Peru, Brazil, Argentina | Clay rose | `#D46B6B` |
| 10 | European | France, Italy, Spain, Greece | Slate blue | `#6B8BAE` |
| 11 | Oceania | Australia, Polynesia, Hawaii | Sea teal | `#5BA0A0` |

---

## 6. Data Model

All data is hardcoded as a JavaScript array literal inside `index.html`. No network requests are made except for Unsplash photo URLs.

### Restaurant Object Schema

| Field | Type | Required | Notes |
|---|---|---|---|
| `id` | `string` | Yes | Unique slug, e.g. `"the-bird"` |
| `name` | `string` | Yes | Display name |
| `genre` | `string` | Yes | One of 11 genre keys |
| `rating` | `number` | Yes | e.g. `4.7` |
| `address` | `string` | Yes | Neighborhood + city |
| `coords` | `{ x, y }` | Yes | SVG canvas coordinates |
| `photo` | `string` | Yes | Unsplash `source.unsplash.com` URL |
| `reviewText` | `string` | Yes | 1–2 paragraph mock review |

MVP will include 10 sample restaurants spanning at least 7 genre categories to demonstrate the filter system meaningfully.

---

## 7. Application State

A single `appState` object is the source of truth. All UI is derived from it. `render()` is the only function that reads `appState` to update the DOM.

| Property | Type | Default | Mutated by |
|---|---|---|---|
| `minRating` | `number` | `0` | Rating slider |
| `activeGenres` | `Set<string>` | all 11 | Genre pill click |
| `hoveredId` | `string \| null` | `null` | Dot mouseenter / mouseleave |
| `selectedId` | `string \| null` | `null` | "Read Review" click |

---

## 8. Visual Design System

### 8.1 Aesthetic Direction

Kinfolk-inspired: editorial, unhurried, photography-forward. The experience should feel like a well-curated magazine about food culture, not a search utility.

### 8.2 Typography

| Role | Typeface | Weight | Usage |
|---|---|---|---|
| Display / Masthead | Cormorant Garamond | 300, 400 | Page title, restaurant names in card |
| Body / UI | Jost | 300, 400, 500 | Filter labels, addresses, review text |
| Data / Ratings | Jost | 500 | Numeric values, genre tags, counts |

### 8.3 Color Tokens

| Token | Hex | Role |
|---|---|---|
| `--color-paper` | `#FAF9F7` | Page and panel backgrounds |
| `--color-sand` | `#F0ECE4` | Cards, hover surfaces |
| `--color-stone` | `#E0DDD8` | Borders, dividers |
| `--color-ink` | `#1A1814` | Primary text, headings |
| `--color-muted` | `#8A8478` | Secondary text, metadata |
| `--color-accent` | `#C8946A` | CTAs, active states |

### 8.4 Motion

| Interaction | Animation | Duration |
|---|---|---|
| Right panel open/close | `translateX(100%) → 0` | 300ms `cubic-bezier(0.4, 0, 0.2, 1)` |
| Hover card appear | `opacity 0→1` + `translateY(4px)→0` | 150ms ease |
| Dot hover | `scale(1) → scale(1.4)` | 120ms ease |
| Genre pill toggle | `background-color` | 100ms |

All motion is CSS transitions only — no JavaScript animation library.

---

## 9. Technical Specification

| Concern | Choice | Rationale |
|---|---|---|
| Deliverable | Single `index.html` | Zero install; open in any browser |
| Map | Inline SVG | No API key; full style control |
| Data | JS array literal | No backend required; directly editable |
| Photos | Unsplash `source.unsplash.com` URLs | Free, no auth, keyword-driven |
| Typography | Google Fonts CDN | No local font files |
| CSS | Custom properties + CSS Grid | Editorial control; no utility framework |
| JavaScript | Vanilla ES6 | No framework; ~150 lines total |
| Animation | CSS transitions only | No JS library; hardware-accelerated |

---

## 10. Out of Scope (Post-MVP)

- Real map tile integration (Mapbox, Google Maps)
- Live data via Google Places API or Yelp Fusion API
- User-generated content: reviews, photos, ratings
- Search bar or text-based lookup
- Mobile / responsive layout
- Saved lists or user accounts
- Share functionality (deep-link to a specific restaurant)
- Accessibility audit (WCAG 2.1 AA compliance)
- Analytics instrumentation
- Restaurant submission workflow

---

## 11. Success Criteria

The MVP ships when:

1. A user can load the page, apply a genre filter, and see dots update on the map with no errors.
2. Hovering a dot displays the hover card with all required fields populated.
3. Clicking "Read Review" opens the right panel with a smooth slide-in animation.
4. The design reads as editorial and distinct from Google Maps / Yelp in a side-by-side comparison.
5. The file opens correctly in Chrome, Firefox, and Safari without any `npm install` or build step.
