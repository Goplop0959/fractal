# Fractal — Multibrot Deep Zoom

An in-browser deep-zoom renderer for Multibrot fractals (z → zⁿ + c), with a
progressive preview pass, a web-worker pool and PNG export.

Static site — no build step, no server-side code. Open `index.html`, or visit the
GitHub Pages URL.

**Live:** https://goplop0959.github.io/fractal/Home

## Routes

| Path | What happens |
| --- | --- |
| `/Home` | the app |
| any other path | redirected to `/Home` (query string kept) |
| `/favicon.ico`, `/cutecat.png`, `/icon-192.png`, `/icon-512.png`, `/apple-touch-icon.png`, `/manifest.webmanifest`, `/robots.txt` | served as-is, never redirected |

## Share links

```
/Home?ShareURL=<code>&Type=code     fast share  – settings + zoom + position
/Home?ShareURL=<code>&Type=image    slow share  – the picture itself
```

### The code format

The share links use **`CustomDeterministicEncoder`** — a direct JavaScript port of
`CustomEncoder.py` (`CustomDeterministicEncoder.luau` compatible). The port lives in
`index.html` beside the renderer, and it is byte-for-byte identical to the standalone
page: same alphabet, same FNV-1a nonce, same hidden length, same xorshift nibble
shifting, so a code made in either place opens in the other.

Alphabet: `ZHNgKuyjx7BvVlhRoqTJU5CtmwO0zSr8k2QEIAd416aXLnPMfDFs9WpGbcei3Y`

A code is **16 characters, `-`, 16 characters, `-`, …** — one block per 8 bytes:

* block 1 is the header: the FNV-1a hash of the payload (the *nonce*, which is also
  the per-message key) and the length hidden as `length XOR nonce XOR 0xC39A7F21`.
* every further block is 8 bytes of data, padded with xorshift bytes, XOR-crypted
  with the nonce, then written as 16 nibbles shifted by a xorshift offset.

Decoding reverses it and refuses anything whose hash disagrees, so a mistyped
character gives "invalid or corrupted" instead of a wrong view.

**Fast share (`Type=code`)** — the view state as JSON, inside that envelope:

```json
{"v":1,"p":2,"c":"magma","a":1,"b":1,"x":"-0.5","y":"0","z":"1.6","i":8000,"d":8192}
```

`x`, `y` and `z` (centre and zoom) are **strings**, so negatives, `1e-14` and full
precision survive untouched; `p`, `i`, `d`, `a`, `b`, `v` are plain integers read
back with `parseInt`. A typical view is 12 blocks (~200 characters).

**Slow share (`Type=image`)** — the same envelope around a 7×3 thumbnail:

```json
{"v":1,"t":"image","n":7,"h":3,"px":"rrggbb rrggbb ..."}
```

126 hex characters, one per RGB channel per pixel, exact (no quantisation):
about 22 blocks.

A code that decodes but is not a view or image payload cannot be restored; there is
no code store.

## Files

| file | what it is |
| --- | --- |
| `index.html` | the whole app (renderer, routes, share codec) |
| `cutecat.png` | logo (top left) and PNG favicon |
| `favicon.ico` | multi-size icon (16–256 px) |
| `apple-touch-icon.png`, `icon-192.png`, `icon-512.png` | home-screen / PWA icons |
| `manifest.webmanifest` | PWA manifest |
| `.nojekyll` | serve the files as-is on GitHub Pages |

## Deploy

Published from the `main` branch root via GitHub Pages.

```sh
git add -A && git commit -m "…" && git push
```
