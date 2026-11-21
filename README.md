# Fractal — Multibrot Deep Zoom

An in-browser deep-zoom renderer for Multibrot fractals (z → zⁿ + c), with a
progressive preview pass, a web-worker pool and PNG export.

Static site — no build step. Just open `index.html`, or visit the GitHub Pages URL.

## Local preview

```sh
python -m http.server 8000
# then open http://localhost:8000
```

## Deploy

The site is published from the `main` branch root via GitHub Pages (a `.nojekyll`
file keeps the assets served as-is).
