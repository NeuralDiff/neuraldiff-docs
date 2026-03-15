# Cloud API Reference

The Cloud API refers to hosted NeuralDiff services. It is not the same thing as the `neuraldiff-api` source repository, which contains the daemon source and publishes `@neuraldiff/daemon`.

## Public Status

Hosted API documentation is intentionally minimal in this public repo. Public docs should not expose internal analysis payloads, routing behavior, callback mechanics, scoring details, or knowledge-base internals.

## What To Use Today

- For local development and agent workflows, use `@neuraldiff/daemon`.
- For package-level integration, use the SDK and MCP-facing docs.
- For hosted-service access, document only the customer-facing contract you are prepared to support publicly.

## Future Home

When hosted docs are published, the public contract should live under the main NeuralDiff docs site with customer-safe examples and stable endpoint guarantees.
