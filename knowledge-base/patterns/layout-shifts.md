# Layout Shift Patterns

Layout shifts happen when elements move, resize, overlap, or collapse unexpectedly. They are often caused by spacing changes, unstable dimensions, or container behavior that was not accounted for.

## Common Layout Shift Issues

### Unexpected Width Or Height Changes

Symptoms:

- sidebars suddenly grow or shrink
- cards wrap differently after a small style change
- content gets pushed below the fold

Recommended fixes:

1. Check width, min-width, max-width, height, and flex or grid constraints.
2. Verify parent containers did not change sizing rules.
3. Confirm new content still fits the intended layout.

### Collapsing Containers

Symptoms:

- children overflow a parent
- a section loses height after content changes
- positioned elements escape their layout context

Recommended fixes:

1. Verify the container establishes the right formatting context.
2. Check for missing clearfix, overflow, or sizing rules.
3. Confirm absolutely positioned children are anchored intentionally.

### Spacing Reflow

Symptoms:

- adding one nav item shifts an entire header
- margin or padding changes ripple across the layout
- cards or tables no longer align cleanly

Recommended fixes:

1. Audit spacing tokens and shared utility classes.
2. Check whether spacing changes alter available width.
3. Test the same page at multiple viewport sizes.
