---
title: CSS & HTML5
tags: [frontend, css, html, flexbox, layout, web]
related: [Angular, TypeScript-JavaScript, ASP-NET]
sources: [Css.md, Html5.md, Bootstrap.md]
updated: 2026-04-12
---

# CSS & HTML5

---

## CSS fundamentals

### The box model

Every element is a box: `content → padding → border → margin`.

```css
box-sizing: border-box;   /* width includes padding+border — almost always what you want */
```

### `z-index`

Controls front-to-back stacking order. Higher value = in front. Only affects positioned elements (`position: relative/absolute/fixed/sticky`).

```css
.modal { position: fixed; z-index: 1000; }
```

---

## Flexbox

A CSS layout model for distributing space and aligning items in one direction (row or column).

**Concepts:**
- **Main axis** — the primary direction (row = horizontal, column = vertical)
- **Cross axis** — perpendicular to main axis
- **Flex container** — the parent with `display: flex`
- **Flex items** — the direct children

### Container properties

```css
.container {
  display: flex;
  flex-direction: row;           /* row | row-reverse | column | column-reverse */
  flex-wrap: wrap;               /* nowrap | wrap | wrap-reverse */
  justify-content: space-between; /* main axis alignment */
  align-items: center;           /* cross axis alignment */
  align-content: flex-start;     /* multi-line cross axis */
  gap: 16px;                     /* spacing between items */
}
```

**`justify-content` values:**

| Value | Effect |
|---|---|
| `flex-start` | Pack to main start |
| `flex-end` | Pack to main end |
| `center` | Center on main axis |
| `space-between` | Equal gaps between items |
| `space-around` | Equal space around items |
| `space-evenly` | Equal space everywhere |

**`align-items` values:** `flex-start`, `flex-end`, `center`, `stretch` (default), `baseline`

### Item properties

```css
.item {
  flex: 1;             /* flex-grow: 1, flex-shrink: 1, flex-basis: 0 */
  flex-grow: 2;        /* take twice as much extra space */
  flex-shrink: 0;      /* don't shrink below flex-basis */
  flex-basis: 200px;   /* initial main-axis size */
  align-self: flex-end; /* override container's align-items */
  order: 1;            /* visual order (default 0) */
}
```

---

## CSS Grid

Two-dimensional layout. Use Grid for page-level layout, Flexbox for component-level layout.

```css
.grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);   /* 3 equal columns */
  grid-template-rows: auto 1fr auto;
  gap: 24px;
}

.item {
  grid-column: 1 / 3;    /* span from line 1 to line 3 */
  grid-row: 2;
}
```

---

## Responsive design

```css
/* Mobile-first breakpoints */
@media (min-width: 768px) { /* tablet */ }
@media (min-width: 1024px) { /* desktop */ }

/* Fluid sizes */
font-size: clamp(1rem, 2.5vw, 2rem);
width: min(100%, 800px);
```

---

## CSS variables (custom properties)

```css
:root {
  --primary: #667eea;
  --spacing-md: 16px;
}

.button {
  background: var(--primary);
  padding: var(--spacing-md);
}
```

---

## HTML5 key features

### Local Storage vs Session Storage

| | localStorage | sessionStorage | Cookies |
|---|---|---|---|
| Lifespan | Permanent | Until tab closes | Set by server |
| Scope | All tabs same origin | Per tab | All tabs + server |
| Size | ~5–10 MB | ~5 MB | ~4 KB |
| Sent to server | No | No | Yes (every request) |

```javascript
// localStorage
localStorage.setItem('key', JSON.stringify(data));
const data = JSON.parse(localStorage.getItem('key'));

// sessionStorage — same API
sessionStorage.setItem('key', value);
```

**Storage event** — fires in other tabs when localStorage changes (not in the same tab that changed it).

### Canvas

The `<canvas>` element provides a pixel-based 2D drawing surface via JavaScript.

```html
<canvas id="myCanvas" width="400" height="300"></canvas>
```

```javascript
const ctx = document.getElementById('myCanvas').getContext('2d');
ctx.fillStyle = '#ff0000';
ctx.fillRect(10, 10, 100, 50);
ctx.rotate(Math.PI / 4);   // rotates the coordinate system
```

### getUserMedia (Media API)

Access camera, microphone, or screen:

```javascript
const stream = await navigator.mediaDevices.getUserMedia({ video: true, audio: true });
video.srcObject = stream;
```

### requestAnimationFrame

Schedules visual updates at the correct frame rate (~60fps):

```javascript
function animate() {
  // update logic
  ctx.clearRect(0, 0, canvas.width, canvas.height);
  // draw
  requestAnimationFrame(animate);
}
requestAnimationFrame(animate);
```

Use instead of `setInterval` for smooth animations — browser optimises timing and pauses when tab is hidden.

---

## Bootstrap (CSS framework)

Utility-first responsive CSS framework based on a 12-column grid.

```html
<div class="container">
  <div class="row">
    <div class="col-md-8">Main content</div>
    <div class="col-md-4">Sidebar</div>
  </div>
</div>
```

**Key utilities:**
- `d-flex`, `justify-content-between`, `align-items-center` — Flexbox
- `mt-3`, `p-2` — margin/padding (0–5 scale)
- `text-center`, `text-muted`, `fw-bold`
- `btn btn-primary`, `btn btn-outline-secondary`
- `table table-striped table-hover`
- `form-control`, `form-label`, `form-check`

---

## See also
- [[TypeScript-JavaScript]] — JS for interactivity
- [[Angular]] — Angular uses CSS/SCSS in component styles
- [[ASP-NET]] — Razor views use HTML + CSS
