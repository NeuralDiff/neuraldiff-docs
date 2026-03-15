# Color Regression Patterns

Color regressions happen when text, backgrounds, borders, icons, or fills change in ways that were not intended. They are often caused by CSS cascade issues, missing variables, or theme-related leakage.

## CSS Specificity Override

### Summary

A new selector unintentionally overrides an older color rule and affects more elements than intended.

### Symptoms

- buttons or links change color outside the area you edited
- text changes in multiple components after a localized style change
- a fix for one screen causes unexpected color changes elsewhere

### Likely Causes

- overly specific selectors
- `!important` overrides
- parent styles cascading more broadly than expected

### Recommended Fixes

1. Inspect the computed style in DevTools to see which rule is winning.
2. Reduce selector specificity where possible.
3. Prefer component-specific classes or modifier classes over deeply nested selectors.

### Code Example

**Before:**

```css
.button {
  background-color: #3b82f6;
}

.settings-panel .button {
  background-color: #10b981;
}
```

**After:**

```css
.settings-panel .save-button {
  background-color: #10b981;
}

.button--success {
  background-color: #10b981;
}
```

## Missing CSS Variable Fallback

### Summary

A color variable is missing in some contexts, so the browser falls back to an undesirable default.

### Symptoms

- text becomes black, transparent, or difficult to read
- a component looks correct in one theme but broken in another
- colors disappear after a refactor

### Recommended Fixes

1. Add fallback values to `var()` expressions.
2. Confirm variables are defined in every context where they are used.
3. Add linting or review checks for required design tokens.

### Code Example

**Before:**

```css
.card {
  background-color: var(--card-bg);
  color: var(--card-text);
}
```

**After:**

```css
.card {
  background-color: var(--card-bg, #ffffff);
  color: var(--card-text, #1a1a1a);
}
```

## Theme Cascade Leak

### Summary

Theme styles affect components that should stay isolated, or fail to reach components that should inherit them.

### Symptoms

- dark-mode styles leak into unexpected regions
- modals or tooltips render in the wrong color scheme
- theme toggles create inconsistent color behavior across screens

### Recommended Fixes

1. Make theme boundaries explicit.
2. Ensure portal roots are inside the intended theme provider.
3. Set explicit colors for components that must remain visually stable.

## Opacity And Background Interaction

### Summary

Transparency changes the perceived color because the background behind the element shows through.

### Symptoms

- text or overlays look different on different backgrounds
- a color appears correct in CSS but wrong on screen
- semi-transparent UI feels inconsistent between pages

### Recommended Fixes

1. Prefer opaque colors where consistency matters.
2. Keep background composition stable behind translucent elements.
3. Compute the final intended color explicitly when needed.
