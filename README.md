# BOH Designer

A self-contained product builder for [Badge of Honour](https://badgeofhonour.com.au/).

**Live at:** https://obeelenius.github.io/boh-designer/

---

## File structure

```
boh-designer/
├── index.html          # The entire application
├── favicon.ico         # Site favicon
├── images/             # AS Colour product images
│   ├── 1001/FRONTSHOTS/ + BACKSHOTS/
│   ├── 5001/FRONTSHOTS/ + BACKSHOTS/
│   ├── 5082/FRONTSHOTS/ + BACKSHOTS/
│   ├── 5101/FRONTSHOTS/ + BACKSHOTS/
│   └── 5171/FRONTSHOTS/ + BACKSHOTS/
└── README.md
```

## Products

| Code | Name | Colours |
|------|------|---------|
| 1001 | Carrie Tote Bag | 14 |
| 5001 | Staple Tee | 70+ |
| 5082 | Heavy Faded Tee | 17 |
| 5101 | Supply Hood | 21 |
| 5171 | Box Hood | 6 |

## Deployment

GitHub Pages from `main` branch. Push to deploy.

## Adding products/colours

1. Add images to `images/{code}/FRONTSHOTS/` and `BACKSHOTS/`
2. Follow AS Colour naming: `{CODE}_{NAME}_{COLOUR}.jpg` / `{CODE}_{NAME}_{COLOUR}_BACK.jpg`
3. Update `PRODUCT_COLOURS` and `COLOUR_PALETTE` in `index.html` (JS: CONSTANTS region)
4. Commit and push

## Tech

Plain HTML/CSS/JS — no framework, no build step. Canvas 2D API for preview rendering.