# Design Identity

Brand asset protocol, position questions, motion character, and DESIGN.md template.

## Brand Asset Protocol

When projects involve a specific brand or product:

### Asset Priority (recognition order)

1. **Logo** — any brand must have one. Find via `<brand>.com/brand`, `<brand>.com/press-kit`, or extract inline SVG from homepage HTML.
2. **Product shots** (hardware) — official product page hero images, press kit renders, launch video keyframes via yt-dlp + ffmpeg.
3. **UI screenshots** (software) — App Store / Google Play listing, product dashboard screenshots, launch videos.
4. **Colors** — extract from homepage CSS: `grep -hoE '#[0-9A-Fa-f]{6}' assets/*.html | sort | uniq -c | sort -rn | head -12`
5. **Fonts** — extract from homepage `<link rel="stylesheet">`, Google Fonts URL, or brand guidelines PDF.

### The 5-10-2-8 Rule

Search 5 sources, collect 10 candidates, pick 2 assets, each scoring ≥8/10 on: resolution (≥2000px), copyright clarity, brand fit, style consistency, narrative value. Score and log in brand-spec.md.

### Logo Exception

Logos don't follow 5-10-2-8 — use whatever exists, even if imperfect. Logo is recognition, not decoration.

### brand-spec.md Template

Create at project root after collecting assets:

```markdown
# Brand Spec
> Collected: YYYY-MM-DD

## Logo
- Primary: `assets/brand/logo.svg`
- Light bg: `assets/brand/logo-dark.svg`
- Dark bg: `assets/brand/logo-light.svg`

## Product / UI Assets
- Hero: `assets/brand/product-hero.png`
- Detail: `assets/brand/product-detail-1.png`
- (or) UI screenshots: `assets/brand/ui-feature-*.png`

## Colors
- Primary: #XXXXXX
- Background: #XXXXXX
- Accent: #XXXXXX
- Ink: #XXXXXX

## Typography
- Display: ['font-name', fallback]
- Body: ['font-name', fallback]
- Mono: ['font-name', fallback]

## What NOT to Do
- <colors/fonts/patterns to explicitly avoid>
```

---

## The 4 Position Questions

Before any HTML, answer for the project:

| Question | Options | Why it matters |
|----------|---------|----------------|
| Narrative role per scene | hero / transition / data / quote / closer | Determines information density and element hierarchy |
| Audience distance | 10cm phone / 1m laptop / 10m projection | Determines type scale (24-32px body vs 48-72px) |
| Visual temperature | explosive / cinematic / fluid / technical / warm | Determines color palette and motion aggression |
| Capacity check | sketch 3× 5-second thumbnails per scene | Prevents content overflow and layout surprises |

---

## Motion Character Framework

Document this in DESIGN.md under a `## Motion` section.

### Energy Level

| Energy | Transition speed | Easing center | Stagger gap | Example use |
|--------|-----------------|---------------|-------------|-------------|
| **Calm** (wellness, brand, luxury) | 0.5-0.8s | `sine.inOut`, `power1` | 0.2-0.3s | Brand film, closing sequence |
| **Medium** (corporate, SaaS, tutorial) | 0.3-0.5s | `power2`, `power3` | 0.1-0.2s | Explainer, product demo |
| **High** (promos, sports, launch) | 0.15-0.3s | `power4`, `expo` | 0.05-0.1s | Sizzle reel, intro bumper |

### Motion Vocabulary (pick primary + accent)

| Vocabulary | Easing | Feel | Use for |
|------------|--------|------|---------|
| Bouncy | `back.out(1.7)`, `elastic.out(1,0.3)` | Playful, light | Social clips, casual content |
| Smooth | `power2.out`, `sine.inOut` | Polished, professional | Corporate, tutorial (default) |
| Urgent | `expo.out`, `power4.out` | Fast, dramatic | Intros, high-energy moments |
| Elegant | `power1.out`, `circ.inOut` | Slow, deliberate | Luxury, closing shots |
| Technical | `steps()`, `none` | Precise, data-driven | Code reveals, instructional |

### Easing Variety Rule

Use ≥3 different eases per scene. Main content entrances use the primary vocabulary. Accent elements (checkmarks, badges, highlights) use a contrasting ease from the accent vocabulary.

### Stagger Patterns

| Pattern | Behavior | Use for |
|---------|----------|---------|
| Waterfall | Top to bottom | Lists, code lines (default) |
| Center-out | From middle outward | Grids, card arrays |
| Randomized | `{each:0.05, from:"random"}` | Organic reveals (GSAP-safe) |
| Burst | Same time, different origins | Reveal moments |

### Transition Character

| Character | Energy fit | Implementation |
|-----------|-----------|---------------|
| Crossfade | Any | Fade opacity 0↔1 over ~0.4s |
| Blur crossfade | Medium | Crossfade + `filter:blur(8px)` on outgoing |
| Push slide | Medium-High | Slide outgoing offscreen -X, incoming in from +X |
| Flash-through | High | Brief overlay at full opacity for 0.1-0.2s |
| Zoom-through | High | Scale outgoing up while fading, scale incoming down |

---

## DESIGN.md Template

```markdown
# <Project> · Design Identity

## Visual
- Colors: Primary #XXXXXX, Background #XXXXXX, Accent #XXXXXX
- Fonts: Display: [...], Body: [...]
- Temperature: <explosive/cinematic/fluid/technical/warm>

## Motion
- Energy: <Calm/Medium/High>
- Primary vocabulary: <bouncy/smooth/urgent/elegant/technical> (<easing>)
- Accent vocabulary: <vocabulary> (<easing>)
- Transition: <crossfade/blur/Push/flash/zoom> at <duration>s
- Transition overlay: <black/white>
- Stagger: <waterfall/center-out/burst> at <gap>s

## Scenes
1. <title> — <role> — <~timing>
2. ...

## What NOT to Do
- <3-5 anti-patterns>
```
