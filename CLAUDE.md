# CLAUDE.md
## Bay Area International Food Map — Project Instructions

This file defines how Claude should behave in this project: code standards, review criteria, performance requirements, and agent roles.

---

## Project Context

Single-file static web application (`index.html`). Vanilla JS, no framework, no build step. Designed to scale to a real-served product — CDN-hosted, potentially behind a high-traffic marketing campaign. Code must be written as if it will serve real users at scale, even in the MVP.

---

## Code Review Criteria

All code written or reviewed in this project must pass the following gates. These apply whether Claude is writing new code or reviewing a diff.

### 1. Correctness

- [ ] Logic matches the spec in `PRD.md` exactly — no undocumented deviations
- [ ] State mutations only happen through `appState` — no direct DOM writes that bypass state
- [ ] `render()` is idempotent — calling it twice with the same state produces the same output
- [ ] All event handlers are attached exactly once — no duplicate bindings on re-render
- [ ] Edge cases handled: 0 results, all filters cleared, slider at min/max, panel already open

### 2. Performance — High QPS / Real Service Standards

This is the most critical gate. The app will be served at scale; treat every interaction as a hot path.

#### DOM & Rendering
- [ ] **No layout thrash.** Never interleave reads and writes to the DOM in the same synchronous block. Batch all reads first, then all writes.
- [ ] **No full re-render on hover.** `mouseenter`/`mouseleave` must update the hover card via direct DOM property assignment (`element.style.left`, `textContent`), never by calling `render()`.
- [ ] **`render()` must complete in under 16ms** (one frame at 60fps). Profile with `performance.now()` before and after if the dot count grows beyond 50.
- [ ] **SVG dot layer cleared with `innerHTML = ""`** not by iterating `removeChild` in a loop — single DOM write is faster.
- [ ] **Avoid `querySelectorAll` inside event handlers.** Cache DOM references at initialization time.
- [ ] **No forced synchronous layouts.** Properties like `offsetWidth`, `getBoundingClientRect()` inside a write loop force a layout — read them all first into variables, then write.

#### Event Handling
- [ ] **Debounce the rating slider** at 16ms (one frame). Firing `render()` on every `input` event at high slider speed creates frame drops.
- [ ] **Throttle is wrong here; debounce is correct.** Debounce ensures the final value is always rendered; throttle can drop the last event.
- [ ] **Use event delegation** on the SVG dot layer — one `mousemove` listener on `<g class="map-dots">`, not one listener per dot. At 100+ dots this becomes measurable.
- [ ] **Passive event listeners** on scroll/touch events: `{ passive: true }`. Prevents jank on touch devices.
- [ ] **Clean up event listeners** if elements are replaced (e.g., on `renderDots`, old listeners must not accumulate).

#### Memory
- [ ] **No memory leaks.** Every `addEventListener` must have a corresponding path to removal or use a delegated pattern.
- [ ] **Image preloading.** Unsplash photos should use `loading="lazy"` and not be preloaded eagerly — avoids bandwidth waste on unused restaurant cards.
- [ ] **WeakRef or index lookup for restaurant data** — do not store restaurant objects on DOM elements (`element._data = obj`). Store the `id` as a `data-id` attribute and look up from the array.

#### Network
- [ ] **Unsplash URLs must include size parameters** (`?w=400&q=80`) to avoid loading full-resolution images into a 240px card.
- [ ] **No inline `onload` or `onerror` handlers on `<img>` tags** — use JS event delegation for error fallback.
- [ ] **Content Security Policy headers** (document in comments if not yet enforced): `img-src 'self' https://source.unsplash.com https://images.unsplash.com`.

### 3. Security

- [ ] **No `innerHTML` with user-controlled data.** All restaurant data is hardcoded, but if it ever comes from an API, sanitize before DOM insertion.
- [ ] **No `eval()`, `new Function()`, or dynamic script injection** of any kind.
- [ ] **`data-*` attributes only store IDs** — never full objects or URLs from untrusted sources.
- [ ] **Unsplash URLs are constructed from a whitelist keyword**, not from user input.
- [ ] External `<script>` tags (Google Fonts) use `crossorigin="anonymous"` where applicable.

### 4. Accessibility

- [ ] All interactive elements (`<button>`, `<input>`) are keyboard-reachable
- [ ] Genre pills have `aria-pressed` reflecting active state
- [ ] Slider has `aria-label="Minimum rating"` and `aria-valuetext` updated live
- [ ] Hover card is not the only path to detail — dot `click` also opens the panel
- [ ] Right panel traps focus when open; restores focus to the triggering dot on close
- [ ] Color is never the sole differentiator — genre name is always visible alongside color

### 5. Code Quality

- [ ] **No comments that describe what the code does** — only comments that explain *why* (a non-obvious constraint, a workaround, a performance decision)
- [ ] **Variable names are full words**, not abbreviations: `restaurant` not `r`, `genreColor` not `gc`
- [ ] **Functions are single-responsibility** — if a function does two things, split it
- [ ] **No magic numbers** — all numeric constants (`280`, `400`, `16ms`) are named CSS variables or JS constants at the top of `<script>`
- [ ] **Consistent naming:** HTML classes use `kebab-case`, JS variables use `camelCase`, CSS custom properties use `--prefix-name`
- [ ] File stays under 500 lines — if it grows past this, flag for refactor discussion

### 6. Kinfolk Design Compliance

- [ ] Zero `border-radius` on any card, panel, or container (pills are exempt — they use `--radius-pill: 999px`)
- [ ] Zero `box-shadow` anywhere
- [ ] Zero gradients anywhere
- [ ] Color palette matches tokens exactly — no hardcoded hex values outside of `genreColors` and the Kinfolk token block
- [ ] Typography: Cormorant Garamond for display, Jost for UI — no other fonts
- [ ] Hover states change `color` only — no scale, no translate, no shadow (dots are an exception: scale 1.4 on hover is specced)

---

## Performance Budget

| Metric | Budget | How to verify |
|---|---|---|
| Initial render (all 10 dots) | < 16ms | `performance.now()` wrap around `render()` |
| Slider debounce window | 16ms | Check debounce implementation |
| Panel slide animation | 300ms CSS only | No JS in the animation path |
| Hover card appear | 150ms CSS only | No JS in the animation path |
| File size (HTML+CSS+JS) | < 150KB | `wc -c index.html` |
| Unsplash image request size | < 40KB per image | Add `?w=400&q=75` to URLs |

---

## Agent Roles

Two specialized agents are defined in `.claude/agents/`:

| Agent | File | Trigger |
|---|---|---|
| Code Reviewer | `.claude/agents/code-reviewer.md` | After any code change to `index.html` |
| QA Agent | `.claude/agents/qa-agent.md` | Before marking any phase complete |

### When to invoke Code Reviewer
- After writing or modifying any JavaScript in `index.html`
- After adding or changing any CSS that affects layout, z-index, or transitions
- After any change that touches event handlers

### When to invoke QA Agent
- After each phase checkpoint in `TODO.md`
- Before marking a TODO item `✅`
- When a bug is reported

---

## File Conventions

| File | Rule |
|---|---|
| `index.html` | Single file. All CSS in `<style>`, all JS in `<script>` at end of `<body>`. |
| `TODO.md` | Update checkbox state after every completed task. |
| `PROJECT.md` | Update milestone status when a full phase completes. |
| `CLAUDE.md` | This file — update when standards change, never delete sections. |

---

## What Claude Should Never Do in This Project

- Never install npm packages or introduce a build step
- Never add a framework (React, Vue, Alpine) without explicit user approval
- Never use `document.write()`
- Never add `console.log` statements to committed code
- Never commit code that fails the performance gate (render > 16ms)
- Never use `!important` in CSS
- Never store state in the DOM (use `appState` as the single source of truth)
