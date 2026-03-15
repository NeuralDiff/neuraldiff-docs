# Architecture Decision Records

This page keeps only public-safe architecture decisions. Internal implementation details, scoring logic, routing rules, and private research notes belong in private internal docs.

---

## ADR-001: Local-First Product Design

**Status**: Accepted

NeuralDiff starts on the local machine and adds hosted assistance only when needed. This keeps setup simple, preserves privacy by default, and lets the product stay useful even when hosted features are unavailable.

---

## ADR-002: Thin Interfaces, Capable Daemon

**Status**: Accepted

The daemon is the primary local runtime. SDK, CLI, and MCP-facing integrations should stay thin and focus on translating requests into daemon operations rather than duplicating product logic.

---

## ADR-003: Public vs. Private Documentation

**Status**: Accepted

Public documentation should cover product behavior, setup, and customer-safe concepts. Internal analysis design, benchmark notes, algorithm tuning, knowledge-base matching logic, and grant materials must stay in private internal docs.

---

## ADR-004: Repository Boundaries

**Status**: Accepted

NeuralDiff uses separate repositories for the daemon source, SDK, web surfaces, and docs. The `neuraldiff-api` repository is the source home for the daemon package published as `@neuraldiff/daemon`.
