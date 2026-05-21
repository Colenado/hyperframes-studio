# Transitions

Scene transition selection and implementation patterns.

## Selection by DESIGN.md Motion Character

Your DESIGN.md `## Motion` section defines transition type and duration. Implement accordingly:

| DESIGN.md says | Implement as |
|----------------|--------------|
| Crossfade | Fade outgoing opacity→0, incoming →1 over specified duration. Both tweens same length. |
| Blur crossfade | Same as crossfade, add `filter: blur(8px)` to outgoing during the fade. Remove filter on fade-in. CSS-only. |
| Push slide | Outgoing slides offscreen in -X direction, incoming slides in from +X. Requires both scenes rendered simultaneously (different track indices or manual GSAP). |
| Flash-through-white | Brief overlay at full opacity for 0.1s between scenes. Black overlay if dark-themed. Simplest high-energy transition. |
| Zoom-through | Scale outgoing 1→1.5 (opacity 1→0), scale incoming 1.2→1 (opacity 0→1). Cinematic. |

## CSS-Only Crossfade Pattern (Recommended)

Works on any Chrome build, no shaders, no html2canvas. Best for single-file compositions:

```html
<div id="transition-overlay" style="position:absolute;inset:0;background:#000;opacity:0;pointer-events:none;z-index:50;"></div>
```

```js
// Scene N → N+1 transition
tl.to("#transition-overlay", { opacity: 1, duration: 0.3, ease: "power2.in" }, sceneEnd - 0.5);
tl.to("#scene-N", { opacity: 0, duration: 0.01 }, sceneEnd - 0.2);
tl.to("#transition-overlay", { opacity: 0, duration: 0.3, ease: "power2.out" }, sceneEnd - 0.2);
```

## Design Rules

1. **ONE primary transition** for 60-70% of scene boundaries. Plus at most ONE accent transition for important moments. Never a different transition per scene.
2. **Duration matches energy level** from DESIGN.md:
   - Calm: 0.5-0.8s
   - Medium: 0.3-0.5s
   - High: 0.15-0.3s
3. **Transition IS the exit.** Never use `gsap.to()` exit animations on any scene except the final scene. Outgoing content must be fully visible when the transition starts.
4. **Final scene only** may fade out (exit animation allowed).
5. Match transition to DESIGN.md motion character. A flash-through-white doesn't belong in a calm piece. A slow crossfade feels weak in a high-energy launch video.
