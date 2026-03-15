# Daemon API Reference

The daemon exposes a local HTTP API on `http://localhost:7100` by default. This page keeps the public contract focused on the customer-facing basics rather than internal analysis internals.

## Common Endpoints

### `GET /health`

Check whether the daemon is running and ready.

### `POST /capture`

Capture a screenshot for a URL or selector.

### `POST /compare`

Compare a capture against the current baseline.

### `POST /capture-and-compare`

Capture and compare in one step. This is the most common integration path.

### `GET /baselines`

List existing baselines for the current project.

### `PUT /baselines/{baseline_id}`

Promote a capture to a new baseline after confirming an intentional change.

## Public Response Shape

Public integrations should expect result summaries such as:

```json
{
  "status": "pass",
  "summary": "No visual regressions detected."
}
```

```json
{
  "status": "fail",
  "summary": "A visual regression was detected.",
  "suggestion": "Review the changed region and confirm whether the update was intentional."
}
```

```json
{
  "status": "no_baseline",
  "summary": "No baseline exists yet for this target."
}
```

## Public Scope

Internal callback endpoints, scoring details, knowledge-base internals, and analysis routing behavior are intentionally omitted from this public reference.
