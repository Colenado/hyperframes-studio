# Single-File Composition for Multi-Scene Projects

An alternative to HyperFrames sub-compositions for multi-scene videos. All scenes live in one `index.html` as absolute-positioned divs managed by a single GSAP timeline. Simpler to author, fewer files to manage, and works on lower-powered hardware (Pi, etc.) where the sub-composition `fetch + clone + compile` cycle adds overhead.

## When to Use

- **Low-powered render targets** (Raspberry Pi, VMs, cloud instances without GPU)
- **Short-to-medium projects** (3-10 scenes, under 60s total)
- **No-complexity-cut here** — single file, one timeline, no cross-file reference chasing
- **CSS-only transitions** — no shader dependencies, no WebGL

Do NOT use when:
- Scenes need independent loading/unloading (very long videos, 20+ scenes)
- You need shader-based transitions (requires sub-composition capture → WebGL texture)
- Each scene has heavy media assets (videos, high-res images) that benefit from lazy loading
- Collaborators will work on scenes in parallel (sub-compositions merge cleanly)

## Structure

```html
<div id="root" data-composition-id="main" data-start="0" data-duration="46" data-width="1920" data-height="1080">
  <!-- Scene 1 -->
  <div id="scene-1" class="scene s1" data-start="0" data-duration="7">
    <div class="tag" id="s1-tag">...</div>
    <h1 id="s1-heading">...</h1>
    <!-- ... -->
  </div>

  <!-- Scene 2 -->
  <div id="scene-2" class="scene s2" data-start="7" data-duration="8">
    <!-- ... -->
  </div>

  <!-- Transition overlay for crossfades -->
  <div id="transition-overlay" style="position:absolute;inset:0;background:#000;opacity:0;pointer-events:none;z-index:50;"></div>
</div>

<script>
  window.__timelines = window.__timelines || {};
  const tl = gsap.timeline({ paused: true, defaults: { ease: "power2.out" } });

  // Scene 1 entrances
  tl.to("#scene-1", { opacity: 1, duration: 0.01 }, 0);
  tl.from("#s1-tag", { opacity: 0, y: -12, duration: 0.4 }, 0.2);
  tl.from("#s1-heading", { opacity: 0, y: 30, duration: 0.5 }, 0.4);
  // ...

  // Scene 1 → 2 transition (crossfade via overlay)
  tl.to("#transition-overlay", { opacity: 1, duration: 0.3, ease: "power2.in" }, 5.5);
  tl.to("#scene-1", { opacity: 0, duration: 0.01 }, 5.8);
  tl.to("#transition-overlay", { opacity: 0, duration: 0.3, ease: "power2.out" }, 5.8);

  // Scene 2 entrances
  tl.to("#scene-2", { opacity: 1, duration: 0.01 }, 6);
  // ...

  window.__timelines["main"] = tl;
</script>
```

## Key Rules

### Scene containers
- Each scene: `position: absolute; inset: 0; width: 1920px; height: 1080px; opacity: 0; pointer-events: none`
- The root div contains all scenes stacked on top of each other
- GSAP controls opacity to show/hide scenes at their time windows
- No `class="clip"` needed — the timeline manages visibility explicitly

### Transition overlay pattern
- A single black `<div>` sits above all scenes (`z-index: 50`)
- At each scene boundary, the overlay fades in (covers outgoing scene) → outgoing scene is hidden → overlay fades out (reveals incoming scene)
- This creates a clean crossfade without shader dependencies
- Duration: 0.3s fade-in, then 0.3s fade-out per boundary (0.5-0.7s total transition)
- GSAP timeline order matters: fade in overlay → hide old scene → fade out overlay → new scene animations are already queued

### Entrance animations
- Offset first animation 0.2-0.3s from scene activation (not t=0)
- Vary eases: `power2.out`, `power3.out`, `back.out(1.4)`, `expo.out` — at least 3 distinct eases per scene
- No duplicate entrance pattern within a scene
- Use `tl.from()` to animate TO the CSS position, not `tl.to()`
- Status bars, persistent UI: treat as part of each scene (duplicate across scene divs or use absolute positioning)

### Pitfalls
- **Scene divs are always in the DOM** — all IDs must be unique across scenes
- **CSS scope is global** — use scoped class names (`.s1 h1`, `.s2 .tag`) to avoid style conflicts
- **Status bars are duplicated** in each scene div — the wrong scene's status bar shows briefly during transitions if not hidden. Solution: the transition overlay covers this, but for cleaner frames, hide all other scenes' status bars at the same time the incoming scene's content starts.
- **No `class="clip"`** — the framework's automatic visibility control doesn't apply. The timeline IS the visibility controller. If a tween is mistimed, a scene may show/hide at the wrong moment.
- **Long files** — single-file approach works best for 3-10 scenes. Beyond that, consider sub-compositions for maintainability.

## When to Convert to Sub-Compositions

If the project outgrows the single-file approach:
1. Split each scene div into its own HTML file under `compositions/`
2. Wrap each in `<template id="scene-N-template">`
3. Replace scene divs in root with `<div data-composition-src="compositions/scene-N.html">`
4. Move per-scene GSAP timelines into the sub-composition scripts
5. Keep transitions in the root timeline or use HyperFrames built-in scene transition API
