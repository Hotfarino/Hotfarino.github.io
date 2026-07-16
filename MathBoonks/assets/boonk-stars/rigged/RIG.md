# Boonk Minotaur SVG Rig

Asset: `boonk-minotaur.svg` · Demo/harness: `index.html`

Slender faceted minotaur girl, authored as pure vector: no raster tags, no base64, no gradients. Every moving part is a semantic `<g id="…">` group. `index.html` loads the SVG in an `<object>` and drives it with a small dependency-free `requestAnimationFrame` engine (no CDN — safe for the Tauri/WebKit build).

## Rig convention

Animated groups carry `data-rig=""` and a `style="transform-box: view-box; transform-origin: <x>px <y>px;"`.

- **`transform-box: view-box`** + **absolute-px `transform-origin`** (not `fill-box` + %). Px pivots are anchored in viewBox space, so they do **not** shift when a group's own geometry changes — e.g. adding the axe to `#arm-right` doesn't move its shoulder pivot. (The retired stocky rig used `fill-box`/% and had to have pivots recomputed on every geometry edit; this convention avoids that.)
- `data-rig` **must** be `data-rig=""`, not a bare attribute — `<object type="image/svg+xml">` parses strict XML and rejects valueless attributes.
- viewBox is `120 20 720 1560`.

| ID | Intended motion | Pivot (px) |
| --- | --- | --- |
| `#root` | Whole-body breathe/bob, cheer crouch+leap | `475 1540`, between the feet |
| `#hair-back` | Static mass behind the head | — |
| `#tail` | Idle swish, cheer wag | `358 1050`, tail base at hip |
| `#neck` `#legs` `#boots` `#skirt` `#tunic` `#belt` `#scarf` | Static body/costume (grouped for future motion) | — |
| `#arm-left` | Hand-on-hip; celebrate lift, wince pull-in | `365 585`, left shoulder |
| `#arm-right` | Holds the axe; axe-raise, celebrate, wince | `585 585`, right shoulder |
| `#head` | Idle bob, nod, wince tilt | `475 566`, neck base |
| `#eye-left` | Blink / expression squash (scaleY) | `430 320`, eye center |
| `#eye-right` | Blink / expression squash (scaleY) | `530 320`, eye center |

## The axe (labrys)

A double-bladed **labrys** (fits her Greek/Minotaur theme) lives inside `#arm-right`, so shoulder motion carries it. Wood haft in her leather browns, two symmetric silver blades (light plane + shadow plane) with bright cutting edges, and a gold center band matching her trim. It is posed angled up her right side with the fist gripping the haft; the blade sits up-and-outside her silhouette. `#arm-right` renders **before** `#head`, so the blade can never cover the face — any overlap is occluded by the head.

## Animation engine (`index.html`)

Idle loop: body breathe + bob, tail sway, head bob, and an auto-blink every ~2.6 s.

One-shot gestures use an expressive curve `gc()` — anticipation (small reverse) → action with overshoot (`easeOutBack`) → hold → settle. Buttons: **nod**, **axe** (raise), **cheer** (crouch → leap → arms up → tail wag), **wince**, **blink**. Amplitudes are tuned for this pose — e.g. axe-raise is a modest `−16°` on `#arm-right` so the blade lifts visibly rather than swinging behind the head.

GSAP or any other engine can drive the same groups; just set each group's transform and honor its `transform-origin`.
