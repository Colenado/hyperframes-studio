---
name: hyperframes-studio
description: "End-to-end motion graphics design and video production. Huashu-Design's design planning (brand assets, motion character, anti-slop) paired with HyperFrames' deterministic rendering pipeline (GSAP, TTS, captions, scene transitions). Self-contained — no parent skill dependencies."
version: 1.0.0
tags: ["creative", "video", "animation", "motion-graphics", "hyperframes"]
prerequisites:
  commands: [node, ffmpeg, npx]
---

# HyperFrames Studio

**Design-first, render-deterministic motion graphics.** Plan the visual direction, motion character, and brand identity before writing a single GSAP tween. Then compose production HTML and render to MP4 with the HyperFrames engine.

## Philosophy

- **Design identity before composition.** Layout, colors, fonts, motion character, and brand assets are phase 1, not phase 4. Every GSAP tween traces back to a decision made in DESIGN.md.
- **Layout before animation.** Hero frames are CSS-only static HTML. GSAP timelines only appear after direction is locked. Changes are cheap until you add tweens.
- **Self-contained, not bridge.** This skill carries its own design planning depth and rendering rules. Load one skill, get the whole pipeline.

## When to Use

- Motion graphics videos (explainers, product promos, title cards, social cutdowns)
- TTS-narrated tutorials with captions
- Music-only text animations (no voiceover)
- Multi-scene compositions with scene transitions

**When NOT to use:** Pure web prototyping (use a front-end skill), pure data slides (use a deck skill), one-off meme generation.

## The 5-Phase Pipeline

### Phase 1: Design Identity

Define visual and motion direction. Output: `DESIGN.md`.

1. **Run the brand asset protocol.** For branded projects: load `skill_view("hyperframes-studio", "references/design-identity.md")` for the protocol steps (5-10-2-8 search rule, asset priority, extraction methods). Collect logo → product shots → UI screenshots → colors → fonts. Save results to `templates/brand-spec.md`.
2. **Answer the 4 position questions** and document in DESIGN.md:
   - Narrative role per scene (hero / transition / data / quote / closer)
   - Audience distance (phone / laptop / projection)
   - Visual temperature (explosive / cinematic / fluid / technical / warm)
   - Capacity check (thumbnails — does content fit per scene?)
3. **Design the motion character.** See `references/design-identity.md` for the full framework. Document energy level, primary+accent vocabulary, transition character, stagger pattern, and easing variety rule.
4. **Write a one-paragraph narrative arc** — story, emotional beats, key moments.

**Gate:** No HTML until DESIGN.md exists and user signs off.

### Phase 2: Layout Prototyping (Hero Frames)

Static CSS-only HTML for each scene's peak moment. No GSAP, no data attributes.

1. Scaffold the project:
   ```
   project/
   ├── DESIGN.md
   ├── SCRIPT.md           (skip if music-only)
   ├── storyboard/
   │   ├── scene-01.html
   │   └── ...
   └── production/
   ```
2. Write SCRIPT.md first (voiceover drives timing). Skip for music-only projects.
3. Each hero frame: 1920×1080 or 1080×1920 viewport, CSS only, real copy, brand assets, animation intent comment. Start from `templates/hero-frame.html`.
4. Run the density check from `references/layout-density.md` — content should use ≤25% of vertical dead space.
5. The gray-box rule: honest placeholder > sloppy SVG.

**Gate:** Show all hero frames. Get layout/typography/color/scene-order feedback. No GSAP written yet.

### Phase 3: Direction Lock

Iterate hero frames based on feedback. CSS-only edits, no GSAP. If the user wants to see motion, prototype with CSS transitions (cheap), not GSAP (expensive).

**Lock criteria:** All hero frames approved — layout, colors, fonts, scene order final.

**Gate:** Do NOT proceed to Phase 4 until every scene is locked.

### Phase 4: Production Composition

Translate hero frames into HyperFrames-compatible HTML with GSAP entrances.

1. **Check prerequisites:** `npx hyperframes doctor`
2. **Choose composition structure:**
   - **Single-file** (simpler, Pi-friendly, 3-10 scenes): One `index.html`, absolute-positioned scenes, one GSAP timeline, black-overlay crossfade. See `references/single-file-composition.md`.
   - **Sub-compositions** (10+ scenes, shader transitions): Scenes in `compositions/` as `<template>` files, loaded via `data-composition-src`.
3. **Apply composition rules** from `references/composition.md`: deterministic, no `repeat: -1`, synchronous timelines, `autoAlpha`, `gsap.from()` entrances only, `window.__timelines` registration.
4. **Animate** with GSAP following `references/gsap.md`: vary eases per the DESIGN.md motion character, use at least 3 eases per scene, match transition type to energy level.
5. **Add transitions** using `references/transitions.md`. One primary (60-70% of boundaries) + 0-1 accent. No exit animations except on final scene.
6. **Add audio** using `references/audio.md`. TTS: `npx hyperframes tts`. Music-only: leave `<audio>` placeholder. Captions: `npx hyperframes transcribe`.
7. **Validate:** `npx hyperframes lint --strict && npx hyperframes validate && npx hyperframes inspect`. Fix all errors.

**Gate:** Lint, validate, and inspect pass before any render.

### Phase 5: Render & Deliver

1. Draft render: `npx hyperframes render --quality draft --output draft.mp4`
2. Check pacing, transition readability, and scene timing.
3. Final render: `npx hyperframes render --quality high --output final.mp4`
4. Verify: file size, duration (ffprobe), audio track present if expected.

## Common Patterns

### A: Talking-Head Explainer
Phase 1 → SCRIPT.md → hero frames → TTS → GSAP + captions → render with audio.

### B: Product Promo
Phase 1 (brand asset protocol required) → hero frames with product shots → lock → GSAP entrances with product reveals → transitions → render.

### C: Music-Only Text Animation
No SCRIPT.md, no TTS, no captions. Timing from content density (~5-6s sparse, ~8-12s dense). Single-file composition. Leave `<audio>` placeholder for user's music.

## Pitfalls

- **Skipping Phase 1-3.** The whole point is avoiding the "write → render → hate → rewrite" loop. Hero frames are cheap; GSAP timelines aren't.
- **Adding GSAP during hero frame iteration.** Keep Phase 2 CSS-only.
- **One ease to rule them all.** Use ≥3 eases per scene. Main content uses primary vocabulary, accents use a contrasting ease.
- **Every scene, different transition.** Pick ONE primary (60-70%) + 0-1 accent. Consistency builds viewer expectation.
- **Static holds with no motion.** 3+ seconds of stillness feels dead. Add subtle movement (pulse, breathe, drift) on hold periods. Cycle: 3-4s.
- **Not running lint before render.** Catches missing data-composition-id, overlapping tracks, unregistered timelines.
- **Forgetting `window.__timelines` registration.** Each composition must register its timeline.
- **Building timelines asynchronously.** The capture engine reads `window.__timelines` synchronously after page load.
- **Using `<br>` in wrapped text.** Forced breaks + natural wraps = unwanted extra breaks. Use `max-width`. Exception: deliberate multi-word display titles.
- **Applying feedback to the wrong deliverable.** If you're producing blog + video + talk, confirm which format the feedback applies to before editing.

## References (Load on Demand)

| Load with | What it covers |
|-----------|---------------|
| `skill_view("hyperframes-studio", "references/design-identity.md")` | Brand asset protocol (5-10-2-8), position questions, motion character framework, DESIGN.md template |
| `skill_view("hyperframes-studio", "references/composition.md")` | HyperFrames data attributes, timeline contract, non-negotiable rules, clip schema |
| `skill_view("hyperframes-studio", "references/composition.md")` | GSAP API for HyperFrames: tweens, eases, stagger, timelines, transforms (bundled in this file) |
| `skill_view("hyperframes-studio", "references/transitions.md")` | Transition selection by energy level, implementation patterns (CSS overlay, push, zoom, flash), rules |
| `skill_view("hyperframes-studio", "references/audio.md")` | TTS, captions, music-only workflow, audio placeholder |
| `skill_view("hyperframes-studio", "references/animation-pitfalls.md")` | Animation-specific anti-patterns and fixes |
| `skill_view("hyperframes-studio", "references/layout-density.md")` | Content density measurement and dead-space fixes |
| `skill_view("hyperframes-studio", "references/single-file-composition.md")` | Single-file composition pattern (Pi-friendly) |
| `skill_view("hyperframes-studio", "templates/hero-frame.html")` | Boilerplate HTML for a single hero frame |
