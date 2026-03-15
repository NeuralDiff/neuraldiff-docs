# Responsive Break Patterns

Responsive breaks happen when a layout works at one viewport size but fails at another. These issues usually come from missing breakpoints, rigid sizing, or interactions between content length and available space.

## Common Responsive Issues

### Overflow At Narrow Widths

Symptoms:

- horizontal scrolling appears unexpectedly
- cards or panels exceed the viewport width
- tables or nav bars push outside the layout

Recommended fixes:

1. Check fixed widths and min-width rules.
2. Make sure long content can wrap or truncate gracefully.
3. Test the smallest supported viewport first.

### Navigation Collapse Failure

Symptoms:

- desktop nav items no longer fit on mobile
- menus overlap branding or actions
- toggles appear but content still renders full-width

Recommended fixes:

1. Revisit the breakpoint where navigation changes form.
2. Confirm hidden and visible states are both styled correctly.
3. Test with real translated or long labels, not just short placeholders.

### Touch Target Problems

Symptoms:

- buttons become hard to tap on small screens
- controls are visually present but unusable
- dense toolbars break mobile usability

Recommended fixes:

1. Increase hit areas and spacing.
2. Re-group or collapse secondary actions.
3. Validate real interactions on a small viewport.
