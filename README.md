# 🪐 Orrery — Interactive 3D Solar System

A real-time, interactive 3D solar system built with Three.js. Planets are rendered as hand-crafted clay spheres with unique surface details — craters on Mercury, volcanoes on Mars, a raised Great Red Spot on Jupiter, and green continents pressing out of Earth's oceans. Every time you open it, all planets are positioned accurately for that day.

---

## Files

```
orrery.html    ← the entire app, one file
README.md      ← this file
```

---

## Running It

Open the file directly in any modern browser:

```bash
# macOS — opens in default browser
open orrery.html

# Pick a specific browser
open -a "Google Chrome" orrery.html
open -a "Firefox" orrery.html
open -a "Safari" orrery.html
```

Or serve it locally if you prefer:

```bash
python3 -m http.server 8000
# then go to http://localhost:8000/orrery.html
```

> **Requires an internet connection on first load** — Three.js and Google Fonts are fetched from CDN. After that it works offline.

---

## Controls

| Action | How |
|---|---|
| Orbit the camera | Left-drag on empty space |
| Pan (move the Sun off-centre) | Right-drag or middle-drag |
| Zoom | Scroll wheel |
| Trackpad pan | Two-finger horizontal swipe |
| Trackpad zoom | Pinch |
| Drag a planet forward/backward in time | Click and drag any planet along its orbit |
| Select a planet | Click it (highlights orbit ring) |
| Hover a planet | Shows fun facts that cycle every 2 seconds |
| Reset camera | Double-click empty space, or press **reset view** button |
| Toggle planet list sidebar | Click the ◀ / ▶ button on the left edge |

### Time Controls (bottom bar)

- **▶ Play / ⏸ Pause** — animate all planets in real time
- **×1 / ×10 / ×100 / ×365** — speed multiplier (×365 = roughly one year per second)
- **Slider** — scrub to any point in time; drag left for the past, right for the future

---

## Features

**Accurate orbital mechanics** — all eight planets use real orbital periods relative to each other. Drag any planet and the rest move proportionally.

| Planet | Orbital Period |
|---|---|
| Mercury | 88 days |
| Venus | 225 days |
| Earth | 365 days |
| Mars | 687 days |
| Jupiter | 4,333 days |
| Saturn | 10,759 days |
| Uranus | 30,687 days |
| Neptune | 60,190 days |

**Retrograde spin** — Venus and Uranus rotate in the opposite direction to all other planets, which is accurate.

**Real positions on load** — on every page load, the app calculates days since the J2000.0 astronomical epoch (January 1 2000, 12:00 UTC) using `Date.now()`, so every planet starts at its correct real-world position for that day. Open it tomorrow and you'll see tomorrow's alignment.

**Clay planet surfaces** — each planet has a procedurally generated canvas texture with organic clay blotches. Special surface details include:

- **Mercury** — 12 impact craters with dark bowls and raised bright rims
- **Earth** — raised green continents with bump mapping over blue oceans
- **Mars** — 4 clay volcanoes with caldera pits, including Olympus Mons; Valles Marineris canyon
- **Jupiter** — horizontal clay bands and a small peach-red Great Red Spot that physically protrudes via bump mapping
- **Saturn** — golden ring geometry tilted 27°
- **Uranus** — tilted 98° on its side with faint rings

**Asteroid Belt** — 340 lumpy clay asteroids orbiting between Mars and Jupiter, each tumbling on its own axis.

**Kuiper Belt** — 420 icy blue-grey objects beyond Neptune, with higher orbital inclination variance and a slight specular sheen.

**Free camera** — the camera orbits any arbitrary point in space, not just the Sun. Pan with right-drag to explore from any angle.

---

## Technical Details

- **Renderer** — Three.js r128 via CDN, `WebGLRenderer` with ACES filmic tone mapping
- **Planet geometry** — `SphereGeometry` (32×32 segments) with `MeshPhongMaterial` (detailed planets) or `MeshToonMaterial` (other planets)
- **Surface relief** — `bumpMap` canvas textures drive per-pixel normal perturbation; bumpScale is highest on Mars (0.30) and Jupiter's GRS (0.55)
- **Asteroid / Kuiper geometry** — `SphereGeometry` with per-vertex random scaling to create lumpy potato shapes, normals recomputed after deformation
- **Orbital position** — `angle = (elapsedDays / period) * 2π`, position set as `(cos(angle) × orbitRadius, 0, sin(angle) × orbitRadius)`
- **Planet drag** — raycasts onto the ecliptic plane (`Y=0`), computes frame-to-frame angle delta via `atan2` to avoid accumulation jumps
- **Star field** — 3,000 `Points` at radius 900–1300, parented to camera position so they never move
- **Pan** — moves `camTarget` in the camera's screen plane using the camera's right and up vectors derived from `cross(forward, worldUp)`
- **No build tools** — single HTML file, zero dependencies beyond the CDN
