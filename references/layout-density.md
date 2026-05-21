# Layout Density — Measuring Content vs Dead Space on 1080p Canvases

## Why This Matters

Video storyboard scenes on 1920×1080 canvases are tall. If you center-sparse content vertically, the top 25-35% of the frame is empty black. The viewer's eye lands on empty space instead of your content. Anchoring content higher (eye-line) fills the frame more naturally.

## The Measurement Check

Run this in the browser console on the hero frame or composition HTML:

```javascript
// Returns content height, empty-above space, and percentage
function checkContentDensity(selector = '.scene') {
  const container = document.querySelector(selector);
  if (!container) return 'Container not found';
  const children = Array.from(container.children);
  const totalH = children.reduce((sum, el) => {
    const h = el.getBoundingClientRect().height;
    const mb = parseFloat(getComputedStyle(el).marginBottom) || 0;
    return sum + h + mb;
  }, 0);

  const style = getComputedStyle(container);
  const paddingTop = parseFloat(style.paddingTop) || 0;
  const paddingBottom = parseFloat(style.paddingBottom) || 0;
  const totalHeight = container.getBoundingClientRect().height;
  const available = totalHeight - paddingTop - paddingBottom;

  // If justify-content: center
  const justifyContent = style.justifyContent;
  let emptyAbove;
  if (justifyContent === 'center') {
    emptyAbove = paddingTop + (available - totalH) / 2;
  } else {
    emptyAbove = paddingTop; // flex-start — content starts after padding
  }

  return {
    contentHeight: Math.round(totalH),
    availableSpace: Math.round(available),
    emptyAbove: Math.round(emptyAbove),
    emptyPercent: Math.round((emptyAbove / totalHeight) * 100),
    justifyContent,
    verdict: emptyAbove / totalHeight > 0.25
      ? '⚠️ TOO MUCH DEAD SPACE — switch to flex-start with reduced top padding'
      : '✓ Layout density OK'
  };
}

// Usage
console.table(checkContentDensity('.scene'));
```

## Thresholds

| Empty above | Verdict |
|-------------|---------|
| < 15% | Dense — centering fine |
| 15-25% | Moderate — could go either way |
| > 25% | Sparse — switch to `flex-start` |

## Fix Template

```css
/* Before (creates dead space on sparse scenes) */
.scene {
  display: flex;
  flex-direction: column;
  justify-content: center;    /* ← bad for sparse content */
  padding: 80px 120px;
}

/* After (anchors content at eye-line) */
.scene {
  display: flex;
  flex-direction: column;
  justify-content: flex-start; /* ← start from top */
  padding: 60px 120px 80px;   /* reduced top pad */
}
```

## When Centering IS Appropriate

- Scenes where content fills 80%+ of the available vertical space (dense code blocks, multiple rows of cards, split layouts)
- Title cards with a single centered headline + subtitle (intentional cinematic choice)
- Closing/end cards with minimal content centered for impact

When in doubt, measure. When measured over 25%, switch.
