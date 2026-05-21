# Composition Rules

HyperFrames data attributes, timeline contract, and non-negotiable rules.

## Root Structure

```html
<!doctype html>
<html>
  <body>
    <div id="stage" data-composition-id="main" data-start="0"
         data-duration="30" data-width="1920" data-height="1080">
      <!-- timed elements go here -->
      <div id="scene-1" data-start="0" data-duration="5" data-track-index="0"
           class="clip">...</div>
    </div>
  </body>
</html>
```

## Data Attributes (Clips)

| Attribute | Required | Values |
|-----------|----------|--------|
| `id` | Yes | Unique |
| `data-start` | Yes | Seconds or clip ID ref |
| `data-duration` | Yes | Seconds |
| `data-track-index` | Yes | Integer. Same-track clips cannot overlap |
| `class="clip"` | Yes for visible timed elements | Framework uses for visibility control |

## Timeline Contract

- Every timeline starts `{ paused: true }`
- Register every timeline: `window.__timelines["<id>"] = tl`
- Duration from `data-duration`, not GSAP timeline length
- Framework nests sub-timelines automatically

## Non-Negotiable Rules

1. **Deterministic.** No `Math.random()`, `Date.now()`, or wall-clock logic.
2. **GSAP only on visual properties.** `opacity`, `x`, `y`, `scale`, `rotation`, `color`, `backgroundColor`, `borderRadius`, `autoAlpha`. Never `visibility` or `display`.
3. **No `repeat: -1`.** Compute finite: `repeat: Math.ceil(duration / cycleDuration) - 1`.
4. **Synchronous timeline construction.** No async/await, setTimeout, or Promises around timeline building. The capture engine reads `window.__timelines` synchronously after page load.
5. **Root has no `<template>`.** Only sub-compositions use `<template>`.
6. **Video muted, audio separate.** Always `<video muted playsinline>` + `<audio>` element.
7. **Content containers use padding.** `.scene { width:100%; height:100%; padding:Npx; display:flex; flex-direction:column; box-sizing:border-box }`. Reserve `position:absolute` for decorative elements only.

## Scene Transition Rules

1. **Always use transitions between scenes.** No jump cuts.
2. **All elements animate IN** via `gsap.from()`. No element appears fully-formed.
3. **No exit animations** except on final scene. The transition IS the exit.
4. **Final scene only** may fade to black.

## GSAP for HyperFrames

Load: `<script src="https://cdn.jsdelivr.net/npm/gsap@3.14.2/dist/gsap.min.js"></script>`

### Core Methods

- `gsap.from(targets, vars)` — entrances. Best for most scene elements.
- `gsap.fromTo(targets, fromVars, toVars)` — explicit start and end.
- `gsap.to(targets, vars)` — exits (final scene only) and reveals.
- `gsap.set(targets, vars)` — immediate. Don't use on elements that enter later in timeline — use `tl.set()` with a time position instead.

### Transforms (prefer over CSS transform)

`x`, `y`, `z`, `xPercent`, `yPercent`, `scale`, `scaleX`, `scaleY`, `rotation`, `rotationX`, `rotationY`, `skewX`, `skewY`, `transformOrigin`.

### Easing Common Values

| Ease | Feel |
|------|------|
| `power1.out` | Gentle |
| `power2.out` | Smooth (default) |
| `power3.out` | Noticeable |
| `power4.out` | Dramatic |
| `expo.out` | Fast entrance |
| `back.out(1.7)` | Overshoot (bouncy) |
| `elastic.out(1,0.3)` | Springy |
| `sine.inOut` | Organic |
| `circ.inOut` | Elegant |
| `none` | Linear |

### Timelines

```js
window.__timelines = window.__timelines || {};
const tl = gsap.timeline({ paused: true, defaults: { duration: 0.6, ease: "power2.out" } });
tl.from(".title", { y: 50, opacity: 0 }, 0.3);
tl.from(".subtitle", { y: 30, opacity: 0 }, 0.5);
window.__timelines["main"] = tl;
```

### Position Parameter (3rd arg)

- Absolute: `0.5`, `2.1`
- Relative to end: `">+0.2"`, `"<"`, `"<+0.3"`
- Labels: `tl.addLabel("act2", 5); tl.from(".x", { y: 30 }, "act2");`

### Stagger

```js
tl.from(".card", { opacity: 0, y: 40, stagger: { amount: 0.6, from: "center" } });
```

### Don't Do

- `repeat: -1`
- `Math.random()`, `Date.now()` in tween values
- Async timeline construction
- Animate `visibility` or `display` (use `autoAlpha`)
- `gsap.set()` on elements not yet in DOM (use `tl.set()` with time position)
