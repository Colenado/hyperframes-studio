# Animation Pitfalls

Motion-design-specific anti-patterns and fixes.

| Pitfall | Why it happens | Fix |
|---------|---------------|-----|
| **One ease everywhere** | Copy-pasting `ease: "power2.out"` to every tween | Use ≥3 eases per scene. Main content = primary vocabulary. Accents = contrasting vocabulary. |
| **Every scene, different transition** | Trying to make each scene feel unique | Pick ONE primary transition (60-70%) + 0-1 accent. Viewer builds expectation for how scenes change. |
| **Static holds with no motion** | All tweens finish 3s+ before next scene | Add subtle hold movement: scale pulse (1→1.02), opacity breathe (0.9→1), slow drift. Cycle: 3-4s. |
| **Stagger identical timing** | `stagger: 0.1` on everything | Vary within scenes: headers 0.05-0.08s (crisp), content rows 0.15-0.25s (readable). |
| **All entrances same direction** | Every element from below | Mix entry directions: some from left, some from below, some scale in. Maps to DESIGN.moment roles. |
| **Animating `visibility`/`display`** | GSAP can't tween these | Use `autoAlpha` (opacity + visibility). |
| **`gsap.set()` on late elements** | Element not in DOM at page load | Use `tl.set(sel, vars, time)` with time position. |
| **`<br>` in wrapped text** | Natural wrap + forced break = double break | Use `max-width` for natural wrapping. Exception: deliberate multi-word titles. |
| **Overlapping clips on same track** | Two clips with data-start/duration overlap | Use different `data-track-index` values for overlapping clips. |
| **Async timeline construction** | setTimeout/Promise/async around timeline building | The capture engine reads `window.__timelines` synchronously. Build inline, no await. |
| **Math.random() in animation values** | Wanting variety in stagers/positions | Use `stagger: { from: "random" }` (seeded) or a mulberry32 PRNG. |
| **Text too small for video** | Using web-type sizes on 1080p | Headline ≥60px, body ≥24px, data labels ≥18px for readability. |
