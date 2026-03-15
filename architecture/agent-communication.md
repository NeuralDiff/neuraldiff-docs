# Agent Communication

NeuralDiff can support a collaborative review flow between the local daemon and hosted assistance. Publicly, the important concept is simple: local checks stay local first, and richer hosted review can ask for additional context only when needed and allowed.

## Public Principles

- the local daemon remains the source of truth for the current workspace state
- hosted review is optional and privacy-aware
- additional context should be requested narrowly rather than sent by default
- customer-facing docs should focus on behavior, not transport internals

## What Customers Need To Know

- most routine checks can stay local
- some advanced review flows may benefit from additional screenshots or context
- privacy controls determine what extra information can be shared

## What This Doc Leaves Out

This public page does not document callback protocols, tunnel details, session limits, retry behavior, or internal security mechanics. Those belong in private internal docs.
