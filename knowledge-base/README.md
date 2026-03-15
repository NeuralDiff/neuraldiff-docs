# Knowledge Base

The NeuralDiff knowledge base is a public-facing library of common visual regression categories, likely causes, and practical fixes.

This public version is intentionally educational. It explains the kinds of issues NeuralDiff can help identify, but it does not document internal matching logic, scoring systems, benchmark data, or proprietary research details.

## What You Will Find Here

- category guides for common visual regression problems
- plain-language explanations of symptoms and likely causes
- example fixes and debugging approaches
- public-safe contribution templates

## Pattern Categories

| Category | Description | Directory |
|---|---|---|
| Layout Shifts | Elements move or resize unexpectedly | [patterns/layout-shifts.md](patterns/layout-shifts.md) |
| Color Regressions | Colors, backgrounds, or contrast change unexpectedly | [patterns/color-regressions.md](patterns/color-regressions.md) |
| Typography Issues | Fonts, weights, spacing, or text flow regress | [patterns/typography-issues.md](patterns/typography-issues.md) |
| Responsive Breaks | Layouts fail at specific viewport sizes | [patterns/responsive-breaks.md](patterns/responsive-breaks.md) |

## How To Use These Docs

Use the pattern guides to:

1. recognize the kind of visual issue you are seeing
2. narrow down likely root causes
3. try the recommended fixes
4. document reusable learnings in public-safe language

## Contributing

Public contributions are welcome when they stay focused on:

- clear problem descriptions
- reproducible symptoms
- safe technical explanations
- actionable fixes

Do not include proprietary detection rules, confidence values, internal thresholds, private datasets, or benchmark/calibration details in this public repo.

## Internal Material

Internal KB matching logic, scoring, datasets, and research notes belong in private internal docs under the daemon/api project.
