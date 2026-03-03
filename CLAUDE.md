# CLAUDE.md — Plantillas HTML para Negocios

## Project Overview

This repository contains self-contained HTML templates for small Latin American
businesses (tiendas, bodegas, restaurantes). Each template is a **single HTML
file** with no external dependencies, no build step, and no server required.

Language of the app UI and code comments: **Spanish**.

---

## Repository Structure

```
plantillas/
├── control-gastos-ingresos.html   # Main product: expense/income tracker app
├── promo-plantilla.html           # Social-media promotional image (1080×1080 px)
└── README.md
```

### File Roles

| File | Purpose |
|---|---|
| `control-gastos-ingresos.html` | Full-featured PWA for tracking income, expenses, accounts receivable/payable, with LocalStorage persistence and CSV export |
| `promo-plantilla.html` | Static promotional card rendered at 1080×1080 px; open in a browser and screenshot |

---

## Architecture & Key Conventions

### Single-file, zero-dependency design
Every template ships everything it needs — HTML structure, CSS styles, and
JavaScript — in **one `.html` file**. There are no npm packages, no bundlers,
no CDN links, and no server. Opening the file in a browser is all that is
needed to run it.

### No build process
There is no `package.json`, Makefile, or CI pipeline. There is nothing to
install or compile. Do not introduce a build step unless the user explicitly
requests it.

### LocalStorage as the only persistence layer
`control-gastos-ingresos.html` saves all user data to
`localStorage` under the key `cgi-data` (serialised JSON). There is no backend.
Data lives only in the user's browser.

### PWA support (inline, HTTPS-only)
The app registers a Service Worker and injects a Web App Manifest — both via
`URL.createObjectURL(blob)` — so no extra files are needed. The Service Worker
only activates when the page is served over HTTPS.

---

## Core JavaScript Patterns (`control-gastos-ingresos.html`)

| Function | Responsibility |
|---|---|
| `agregarFila(tipo)` | Appends a dynamic table row to `body-{tipo}` (`ingresos`, `gastos`, `cobrar`, `pagar`) |
| `eliminarFila(id)` | Removes a row by element id and triggers recalculation |
| `sumarTabla(bodyId)` | Sums all `input.monto` values in a `<tbody>` |
| `resumenCategoria(bodyId)` | Builds a `{category → total}` map from a table body |
| `renderResumen(containerId, map, color)` | Renders the category breakdown bars into a detail column |
| `recalcular()` | Master update — recomputes all totals, updates DOM, fires `autoGuardar()` |
| `recopilarDatos()` | Serialises the entire form state to a plain JS object |
| `guardarLocal()` / `cargarLocal()` | Explicit save/load to/from `localStorage` |
| `autoGuardar()` | Debounced (800 ms) auto-save triggered on every input change |
| `exportarCSV()` | Builds a CSV string from `recopilarDatos()` and triggers a download |
| `limpiarTodo()` | Clears all table rows after a confirmation dialog |
| `cargarEjemplos()` | Populates the tables with sample rows on first load |
| `instalarApp()` | Triggers the browser's PWA install prompt |

### Category constants
```js
CATS_INGRESOS // income categories
CATS_GASTOS   // expense categories
METODOS       // payment methods: Efectivo, Tarjeta, Transferencia, Crédito
ESTADOS       // account states: Pendiente, Pagado, Parcial
```

### Currency helper
```js
let moneda = () => document.getElementById('moneda').value; // e.g. "S/. "
let fmt = v => moneda() + parseFloat(v || 0).toFixed(2);
```

---

## CSS Conventions

- **Reset first**: `* { box-sizing: border-box; margin: 0; padding: 0; }`
- Color-coded semantic classes: `.verde`, `.rojo`, `.azul`, `.naranja` on panel
  headers; `.ingresos`, `.gastos`, `.balance`, `.margen` on summary cards.
- Responsive breakpoints use `@media (max-width: 900px)` and `@media (max-width: 700px)`.
- Print stylesheet hides action buttons and removes box-shadows.
- Badge classes for payment methods: `.badge-efectivo`, `.badge-tarjeta`,
  `.badge-transfer`, `.badge-credito`.

---

## Development Workflow

### Editing a template
1. Open the `.html` file in any text editor.
2. Test by opening the file in a browser (double-click or `file://` URL).
3. For PWA/Service Worker features, serve over HTTPS (e.g. `npx serve .` then
   expose via a tunnel, or use VS Code Live Server with HTTPS).

### Adding a new template
1. Create a new `.html` file at the repo root.
2. Keep it self-contained — all CSS in `<style>`, all JS in `<script>`.
3. Follow the existing naming convention: `kebab-case.html`.
4. Add an entry to `README.md`.

### Git conventions
- Commit messages are written in Spanish (matching existing history).
- Branch names for AI sessions follow the pattern `claude/<session-id>`.

---

## Supported Business Types

The app targets Latin American micro-businesses:

- **Bodega** (corner store / warehouse)
- **Tienda** (general store)
- **Restaurante** (restaurant)
- **Minimarket**
- **Otro** (other)

Currency options pre-configured: PEN (Peru), USD, COP (Colombia), MXN (Mexico),
ARS (Argentina), CLP (Chile).

---

## Promotional Template (`promo-plantilla.html`)

- Fixed canvas: `1080 × 1080 px` (Instagram/social media ratio).
- Dark gradient background with decorative radial blobs via `::before` /
  `::after` pseudo-elements.
- Accent color: `#00d478` (green), used for badge, price, CTA button, and
  feature icons.
- To export as an image: open in Chrome → DevTools → screenshot the element, or
  use a headless browser tool.
- This file is purely presentational; it contains no JavaScript.

---

## What NOT to Do

- Do not add a build system, package manager, or external dependencies unless
  explicitly asked.
- Do not split a template into multiple files (separate CSS/JS files); the
  single-file design is intentional.
- Do not add a backend or database; LocalStorage is the intended storage layer.
- Do not change the UI language from Spanish to English.
- Do not add a linter or formatter configuration unless asked.
