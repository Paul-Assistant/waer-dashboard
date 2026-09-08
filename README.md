# WÆR.OPS — Live Weather Dashboard

A live weather-linked product dashboard for WÆR.OPS. Built as a single-file static site with real-time data from Open-Meteo, pixel-faithful to the Figma design.

**Live:** https://waer-dashboard.vercel.app  
**Figma:** [🔥 garcy.studio 2026](https://www.figma.com/design/yuzxZRre0i5hX1IOwvDMEW) — `weather-product-dashboard` frame

---

## Stack

- **HTML/CSS/JS** — single `index.html`, no framework, no build step
- **Fonts** — Outfit + Geist via Google Fonts
- **Weather API** — [Open-Meteo](https://open-meteo.com) (free, no key required)
- **Hosting** — Vercel (static)
- **Repo** — GitHub (`Paul-Assistant/waer-dashboard`)

---

## Project Structure

```
waer-dashboard/
├── index.html          # Entire app — markup, styles, scripts
├── logo-mark.svg       # WÆR.OPS logo (also used as favicon)
├── icons/
│   ├── wind.svg
│   ├── droplet.svg
│   ├── sun.svg
│   └── thermometer.svg
├── images/
│   ├── rain-shell.webp
│   ├── winter-insulate.webp
│   └── uv-windbreaker.webp
└── videos/
    ├── storm-test.mp4
    ├── arctic-trial.mp4
    └── uv-endurance.mp4
```

---

## Deploying

**Push to `main` = auto-deploy. That's it.**

Vercel is connected to this repo and watches `main`. Every push triggers a production deployment automatically — no CLI, no manual steps.

```bash
git add -A
git commit -m "your message"
git push origin main
```

Deployment takes ~30 seconds. Verify at https://waer-dashboard.vercel.app after pushing.

> ⚠️ Never run `vercel deploy` or call the Vercel API directly. The GitHub → Vercel auto-deploy is the only deploy path.

---

## Weather Data

Live data fetched from Open-Meteo every **30 seconds**, location fixed to **Reykjavík, Iceland**.

**Endpoint:**
```
https://api.open-meteo.com/v1/forecast
  ?latitude=64.1355
  &longitude=-21.8954
  &current=temperature_2m,relative_humidity_2m,apparent_temperature,
           wind_speed_10m,uv_index,weather_code,cloud_cover
  &wind_speed_unit=kmh
  &timezone=Atlantic/Reykjavik
```

**Fields used:**
| Field | Displayed as |
|---|---|
| `temperature_2m` | Main temperature |
| `apparent_temperature` | Feels Like |
| `wind_speed_10m` | Wind Speed |
| `relative_humidity_2m` | Humidity |
| `uv_index` | UV Index + label |
| `weather_code` | Condition text |

If the API fails, all fields gracefully fall back to `--`.

---

## Design System

Sourced directly from Figma. Do not eyeball — pull values from the source.

| Token | Value |
|---|---|
| Background | `#0d0d0f` |
| Panel | `#161619` |
| Card | `#1f1f24` |
| Accent | `#f9fc50` |
| Text | `#ffffff` |
| Muted | `#8e8e95` |
| Dimmed | `#5c5c64` |

**Fonts:** Outfit (display, headings, values) · Geist (labels, meta, descriptions)

---

## Adding / Updating Assets

### Images
Source images should be 2048×2048px PNG or better. Optimize before committing:

```bash
pip3 install Pillow

python3 -c "
from PIL import Image
img = Image.open('source.png').convert('RGB')
img = img.resize((900, 900), Image.LANCZOS)
img.save('images/output.webp', 'WEBP', quality=85, method=6)
"
```

Target: **< 150 KB per image** at 900px wide (2× retina for 432px display size).

### Videos
Keep videos under **5 MB** each. MP4 with H.264, muted autoplay loop. Drop into `videos/` and reference in `index.html`.

### Icons / SVGs
Export directly from Figma as SVG. Drop into `icons/` and reference with `<img>` — do not inline unless the SVG needs dynamic color changes.

---

## Responsive Breakpoints

| Breakpoint | Layout |
|---|---|
| ≥ 1440px | Full desktop, content max-width 1344px |
| 1024–1439px | Fluid, content calc(100% - 96px) |
| 768–1023px | 2-col stat cards, 2-col product/video grids |
| < 768px | Single column, stacked layout |

---

## Hover Effects

Each weather stat card triggers an environmental effect on hover:

| Card | Effect |
|---|---|
| Wind Speed | Horizontal motion blur + diagonal streaks sweep across (2s) |
| Humidity | Frosted glass vapor overlay + backdrop blur (2.5s) |
| UV Index | Warm yellow glow from top + page brightens (2s) |
| Feels Like | Whole page shivers with decreasing tremors (0.9s) |

Effects are implemented in pure CSS animations triggered by JS class toggling on `<body>`.

---

## Dev Notes

- No build step — open `index.html` directly in a browser to develop locally
- To test the data refresh animation without waiting 30s, open DevTools console and run `fetchWeather()`
- All design values (spacing, font sizes, letter-spacing) are sourced from Figma node inspection — do not approximate
- The slot-machine roll animation only fires when a value actually changes between fetches
