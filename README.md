# BOH Designer

A self-contained product builder for [Badge of Honour](https://badgeofhonour.com.au/) — a custom merchandise printing platform. The tool allows users to configure a merch range step by step: select products and colours, upload artwork, set logo placement, define sizing and pricing, and publish to the Medusa ecommerce backend.

Live at: `[your-netlify-url]`

---

## What it does

The builder is a single HTML file with no external dependencies or build step. It runs entirely in the browser.

**Products tab** — select garments from the range (t-shirt, hoodie, sweatshirt, tote bag, etc.), choose weight (lightweight or heavyweight) and colour per product. Each product shows as a photo thumbnail; selecting one expands its configuration options.

**Artwork tab** — upload a shared logo that applies to all products by default. Individual products can override this with their own artwork. Supports PNG, SVG, and JPG. A logo colour tinting option lets you preview the logo in different ink colours.

**Design tab** — choose logo placement from a set of print positions (chest centre, chest left, front top right, back centre, sleeve, hem) measured from real annotated product images. A size slider scales the logo. The logo can also be dragged directly on the preview canvas to fine-tune position.

**Order tab** — select available sizes (2XS to 4XL), set run exclusivity (unlimited or capped), and configure pricing per size.

**Finalise tab** — review a full order summary, then save as draft, share a link, or publish to store. Publishing generates a structured JSON payload shaped for the Medusa product API.

**Preview panel** — always visible on the right. Shows a live canvas preview of the active product with the uploaded logo overlaid. A thumbnail strip below lets you switch between all selected products. Front/back view toggle is included.

---

## File structure

```
boh-designer/
├── badge-of-honour-builder.html   # The entire application
├── netlify.toml                   # Netlify deployment config
└── README.md
```

The HTML file is fully self-contained — all product images are embedded as base64 data URLs. No server, no build tool, no npm.

---

## Deployment

The site is deployed via [Netlify](https://netlify.com) with continuous deployment from this repo. Any push to `main` triggers an automatic redeploy, usually within 30 seconds.

**To deploy manually (one-off):**
Drag `badge-of-honour-builder.html` into [Netlify Drop](https://app.netlify.com/drop).

**To set up continuous deployment:**
1. In Netlify, go to "Add new site" → "Import an existing project" → GitHub
2. Select the `boh-designer` repo
3. Leave build command and publish directory blank — `netlify.toml` handles routing
4. Deploy

---

## Adding product images

Product images are embedded as base64-encoded JPEGs inside `badge-of-honour-builder.html`. They're stored in two JS constants near the top of the `<script>` block:

- `PRODUCT_IMAGES` — colour-specific photos, keyed as `"productid-weight-colour"` (e.g. `"tshirt-lightweight-red"`)
- `PRODUCT_THUMBNAILS` — small square thumbnails used in the product selector grid, keyed by product ID
- `PRODUCT_PREVIEW_FALLBACKS` — full-size fallback images for products that don't yet have colour variants

**To add a new colour variant:**
1. Prepare the image at 600×600px, JPEG quality ~88
2. Base64-encode it: `base64 -i yourfile.jpg`
3. Add the entry to `PRODUCT_IMAGES` with the correct key
4. Add the colour key to `AVAILABLE_COLOURS` for the relevant product and weight

**When to move images to an assets folder:**
The current approach (base64 in HTML) works well up to around 20–25 product images. Beyond that, the HTML file gets unwieldy. At that point, move images into a `/assets/products/` folder in this repo and update the JS constants to use relative paths (`"./assets/products/tshirt-lightweight-red.jpg"`) instead of data URLs.

---

## Print zone coordinates

Logo placement positions are defined in `PLACEMENT_PRESETS` as canvas fractions (0–1). These were measured by running pixel-detection against annotated product images:

| Placement | cx | cy |
|---|---|---|
| Chest left | 0.36 | 0.285 |
| Chest centre | 0.50 | 0.285 |
| Full front | 0.50 | 0.42 |
| Front top right | 0.558 | 0.252 |
| Back centre | 0.509 | 0.434 |
| Sleeve | 0.22 | 0.32 |
| Hem | 0.50 | 0.75 |

To adjust a position, update the relevant entry in `PLACEMENT_PRESETS` in the script block.

---

## Medusa integration

On publish, the builder generates a JSON payload structured for the Medusa product API. The shape is:

```json
{
  "products": [
    {
      "type": "T-shirt",
      "color": "Red",
      "weight": "lightweight",
      "artwork": { "file": "logo.png", "source": "shared" }
    }
  ],
  "design": {
    "placement": "chest-center",
    "size_pct": 22,
    "logo_color": "as-uploaded"
  },
  "variants": {
    "sizes": ["S", "M", "L", "XL"],
    "prices": { "S": "45.00", "M": "45.00", "L": "45.00", "XL": "47.00" }
  },
  "run": "unlimited",
  "status": "published",
  "created_at": "2026-03-30T..."
}
```

This payload is currently displayed in the UI on publish. Wiring it to the actual Medusa API endpoint requires adding a `fetch` call in the `publish()` function with the appropriate auth headers and endpoint URL.

---

## Tech notes

- No framework, no bundler — plain HTML, CSS, and vanilla JS
- Product preview uses the Canvas 2D API
- Logo drag-and-drop works on both mouse and touch
- Logo colour tinting uses `globalCompositeOperation: 'source-in'` on an offscreen canvas
- Dark mode is supported via CSS custom properties and `prefers-color-scheme`
- Fonts: [Syne](https://fonts.google.com/specimen/Syne) (headings) and [DM Sans](https://fonts.google.com/specimen/DM+Sans) + [DM Mono](https://fonts.google.com/specimen/DM+Mono) (body/labels), loaded from Google Fonts
