# Escalation Pipeline

NeuralDiff uses a local-first escalation model. Every visual check starts with the simplest available analysis and only reaches for richer context when the result is still ambiguous.

## Public Overview

At a high level, the pipeline works like this:

1. **Local visual comparison**
   The daemon captures the current UI state and compares it to what was seen before.

2. **Context-aware structural analysis**
   NeuralDiff looks at where change happened and whether it lines up with the user or agent's stated intent.

3. **Known-issue and historical guidance**
   The system can use known issue categories and prior learnings to recognize common regressions and common safe changes.

4. **Optional hosted assistance**
   When local signals are not enough, hosted services can help with deeper analysis, account-aware workflows, and richer review experiences.

## Design Goals

- keep most checks local
- preserve privacy by default
- avoid unnecessary hosted analysis when a simple answer is enough
- surface actionable explanations rather than raw diffs alone

## What This Public Doc Intentionally Leaves Out

This page does not publish internal thresholds, scoring models, routing logic, or benchmark distributions. Those belong in private internal docs.
