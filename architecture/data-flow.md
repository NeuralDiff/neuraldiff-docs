# Data Flow

This page describes the public, high-level flow of a NeuralDiff check without exposing internal routing or scoring details.

## End-to-End Flow

1. An agent, script, or developer asks NeuralDiff to check a page or component.
2. The local daemon captures the current UI state and loads the relevant baseline.
3. NeuralDiff compares the current view to prior expected output.
4. The daemon returns a result locally, or optionally uses hosted assistance when richer review is needed.
5. The caller receives a summary and can keep the current baseline or intentionally update it.

## What Moves Through The System

- target URL or selector
- viewport and capture settings
- current screenshot and baseline
- a result summary suitable for a person or agent to act on

## Privacy Model

- captures and baselines start local
- hosted assistance is optional
- privacy controls determine what additional context can leave the machine

## Public Scope

This page intentionally omits internal scoring, routing, retention internals, callback transport, and benchmark data. Those belong in private internal docs.
