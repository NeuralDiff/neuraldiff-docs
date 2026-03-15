# Pattern Library

This directory contains public-safe pattern guides organized by category. Each guide describes a class of visual regression, common symptoms, likely causes, and ways to fix it.

## Public Pattern Format

Public pattern docs should include:

- pattern or issue name
- symptoms a developer might notice
- likely root causes
- step-by-step fixes
- small before/after examples when helpful
- related topics

Public pattern docs should not include:

- confidence scores
- occurrence counts
- detection signatures
- matching thresholds
- benchmark data
- internal scoring or ranking logic

## Categories

### [Layout Shifts](layout-shifts.md)

Unexpected movement, overlap, resizing, or spacing changes in the layout.

### [Color Regressions](color-regressions.md)

Unexpected changes to text color, background color, borders, fills, or contrast.

### [Typography Issues](typography-issues.md)

Unexpected changes in font rendering, text flow, sizing, weight, or truncation.

### [Responsive Breaks](responsive-breaks.md)

Issues that appear only at particular viewport sizes or breakpoints.

## Contributing

Use the public template in [templates/pattern-template.md](../templates/pattern-template.md) when adding or editing pattern guides.
