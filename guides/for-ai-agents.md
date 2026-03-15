# Guide for AI Agents

This document is written for AI agents that use NeuralDiff during development. It explains when to run visual checks, how to describe intent clearly, and how to respond to results without exposing internal analysis details.

## Overview

You can use NeuralDiff through the daemon and MCP-facing tools to capture screenshots and check for regressions while working on UI changes.

## Main Workflow

1. Make the UI change.
2. Run a capture-and-compare check against the affected page or component.
3. Read the returned summary and suggestion.
4. If the change is intentional and correct, update the baseline.
5. If the change is unintended, fix it and check again.

## When To Capture

Always capture after:

1. CSS or style changes
2. component structure changes
3. layout and spacing changes
4. image or icon updates
5. typography changes
6. responsive behavior changes

## Context Best Practices

Give a short explanation of what changed and why. Good context helps NeuralDiff distinguish intended updates from likely regressions.

Good example:

```text
Changed the header background color and added an Export button to the toolbar.
```

Poor example:

```text
Made changes.
```

## Interpreting Results

### `pass`

No regression was reported. Continue with your work.

### `fail`

A regression may have been found. Read the summary, investigate the affected area, and re-run the check after fixing it.

### `no_baseline`

No prior baseline exists yet for that target. This is normal on first capture.

## Multi-Viewport Checks

If a change could affect responsive behavior, run checks for desktop, tablet, and mobile rather than only one viewport.

## Error Handling

If the daemon is not running, start it with:

```text
npx @neuraldiff/daemon
```

If hosted assistance is unavailable, local checks can still provide useful results. Tell the user when a result indicates reduced coverage or manual review is needed.
