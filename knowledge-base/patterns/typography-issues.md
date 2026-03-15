# Typography Issue Patterns

Typography regressions happen when fonts, weights, spacing, wrapping, or truncation change unexpectedly. These bugs are often subtle but can affect readability, hierarchy, and brand consistency.

## Common Typography Issues

### Font Loading Or Fallback Mismatch

Symptoms:

- text looks heavier or wider after deploy
- layout shifts when the font finishes loading
- one screen uses a fallback font unexpectedly

Recommended fixes:

1. Verify the intended font loads in every environment.
2. Check fallback stacks and font-display behavior.
3. Compare computed font-family, weight, and line-height values.

### Line Height And Wrapping Changes

Symptoms:

- text spills into extra lines
- headings or labels push neighboring content
- cards become different heights for the same content

Recommended fixes:

1. Check line-height, letter-spacing, and width constraints together.
2. Confirm font-size changes did not require spacing updates elsewhere.
3. Test representative long content, not only ideal examples.

### Weight Or Size Drift

Symptoms:

- text hierarchy feels wrong without an obvious break
- buttons or labels look subtly off-brand
- the UI feels denser or lighter after refactors

Recommended fixes:

1. Compare against design tokens instead of eyeballing the result.
2. Audit inherited typography styles from parent containers.
3. Lock shared text styles into reusable components or utilities.
