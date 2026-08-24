# Matrix Rain

Full-screen animated Matrix rain experiments. Every page is a single self-contained
static HTML file with vanilla JavaScript rendering to a `<canvas>` — no build step,
no dependencies. Open any file directly in a browser, or visit the live pages below.

All pages share the same core engine: columns of half-width katakana, digits, and
symbols fall with a bright glowing head glyph and a smoothly fading trail, redrawn
from scratch every frame over pure black (no compositing residue). Each variant page
has a small nav in the top-left corner for quickly flipping between them.

## Pages

### Baseline — [zeblawrence.github.io/matrix-video](https://zeblawrence.github.io/matrix-video/)

`index.html` — the classic effect. Columns fall at random speeds with long trails,
and the rain color slowly cycles console green → neon blue → neon red → back to
green over 90 seconds, with smooth-stepped blends that linger near each pure color.
The mouse cursor is hidden for a clean screensaver look.

Tuning constants at the top of the script:

| Constant | Effect |
|---|---|
| `FONT_SIZE` | Glyph size and column spacing (px) |
| `TRAIL_LENGTH` | Rows of visible trail before full fade |
| `MIN_SPEED` / `MAX_SPEED` | Fall speed range, in rows per frame (at 30fps) |
| `COLOR_CYCLE` | Seconds for the full color loop |
| `PALETTE` | Color anchors — add or reorder entries freely; the cycle divides evenly among them |
| `MUTATE_CHANCE` | How often settled glyphs flicker to a new character |

### Depth layers — [/layers.html](https://zeblawrence.github.io/matrix-video/layers.html)

Three sheets of rain at different font sizes stacked back to front. Small glyphs
are dim and slow (background), large glyphs are fast and bright with a strong glow
(foreground), which reads as parallax depth planes.

Edit the `LAYERS` array to add, remove, or restyle sheets — each entry sets
`size`, `speedMul`, `alpha`, `glow`, and `color`.

### Tilt — [/tilt.html](https://zeblawrence.github.io/matrix-video/tilt.html)

The baseline rain on an oversized canvas that slowly rocks back in 3D: a CSS
`perspective()` + `rotateX()` animation pivots at the center of the viewport, so
the top of the rain wall recedes toward a vanishing point while the bottom leans
out toward you — like tilting your head up at a wall of falling code. One full
rock takes 60 seconds.

Tune it in the `@keyframes tilt` block: the `650px` perspective distance
(smaller = more dramatic depth) and the `35deg` maximum angle.

### 3D space — [/space.html](https://zeblawrence.github.io/matrix-video/space.html)

True perspective projection. 260 streams live at fixed depths in 3D world space;
glyph size, on-screen speed, and fog dimming all fall out of the projection, so
depth is continuous rather than layered — near streams are huge, fast, and bright,
far ones tiny and faint. A slow sinusoidal camera sway adds real parallax between
depths.

Key constants: `STREAM_COUNT`, `Z_NEAR` / `Z_FAR` (depth range), `GLYPH_WORLD`
(glyph size in world units), `SWAY` (camera sway amplitude; 0 disables).

### Image mask — [/mask.html](https://zeblawrence.github.io/matrix-video/mask.html)

An image acts as a brightness mask over the rain: it is cover-scaled to fill the
screen and each glyph's brightness is multiplied by the image's luminance beneath
it, so the picture ghosts through the falling code. Bright image areas also lift
trail glyphs toward full head brightness, making the subject pop. Ships with a
Millennium Falcon default; the multi-layer engine (several interleaved sheets of
columns) provides the glyph density that makes images legible.

**Using your own image:** drag and drop any image anywhere on the page, or click
the hint text in the bottom-left corner to open a file picker. High-contrast
images with big bright shapes on dark backgrounds work best.

Tuning constants:

| Constant | Effect |
|---|---|
| `MASK_GAMMA` | Contrast curve: >1 crushes shadows for punch, <1 lifts midtones for photos |
| `MASK_FLOOR` | Minimum brightness so dark areas keep a whisper of rain |
| `MASK_BOOST` | How strongly bright areas lift trail glyphs toward head brightness |
| `MASK_RES` | Mask sampling cell in px (smaller = finer image detail) |
| `LAYERS` | Sheets of rain columns — add entries for more density; `offset: 0.5` interleaves two sheets of the same size |

Note: the drag-and-drop mask and the default image require the page to be served
over HTTP (the live site, or any local server) — browsers block canvas pixel
reads of local images on `file://` pages, in which case the page falls back to a
procedural text mask.

## Running locally

Any static file server works, e.g.:

```bash
python -m http.server 8321
```

then open http://localhost:8321. (Only `mask.html` needs the server — the other
pages work opened directly as files.)
