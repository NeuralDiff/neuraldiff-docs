# NeuralDiff

Visual regression detection for AI-assisted development.

NeuralDiff is built around a local-first daemon that AI agents can call through MCP. The daemon captures screenshots, compares UI states, manages local QA sessions, and can call hosted NeuralDiff services when local checks are not enough.

## Naming

- `neuraldiff-api` is the source repository for the daemon.
- `@neuraldiff/daemon` is the npm package published from that repository.
- "NeuralDiff Cloud" refers to hosted services, not the `neuraldiff-api` repository.

## How It Works

```text
AI agent
   |
   | MCP (stdio)
   v
@neuraldiff/daemon
(source: neuraldiff-api)
   |
   | local capture, compare, QA, UI
   v
Hosted NeuralDiff services
```

1. **Daemon** (`@neuraldiff/daemon`, source in `neuraldiff-api`) runs locally as one process with MCP, HTTP API, web UI, screenshot capture, and local analysis.
2. **Hosted NeuralDiff services** provide auth, API key verification, and any cloud-assisted analysis flows used by your deployment.
3. **Web app** (`neuraldiff-web`) provides the dashboard, billing, integrations, and workflow surfaces.

## Getting Started In 60 Seconds

```bash
claude mcp add neuraldiff -- npx @neuraldiff/daemon
```

The daemon starts automatically when your MCP client launches it and serves the local UI at `http://localhost:7878`.

## Documentation

### Architecture
- [System Overview](architecture/overview.md)
- [Escalation Pipeline](architecture/escalation-pipeline.md)
- [Agent Communication](architecture/agent-communication.md)
- [Data Flow](architecture/data-flow.md)

### Guides
- [Getting Started](guides/getting-started.md)
- [MCP Setup](guides/mcp-setup.md)
- [Daemon Setup](guides/daemon-setup.md)
- [Cloud API Setup](guides/api-setup.md)
- [For AI Agents](guides/for-ai-agents.md)

### API Reference
- [Daemon API](api-reference/daemon-api.md)
- [Cloud API](api-reference/cloud-api.md)
- [MCP Tools](api-reference/mcp-tools.md)
- [SDK Reference](api-reference/sdk-reference.md)

## Repository Map

| Repository | Purpose | Notes |
|---|---|---|
| `neuraldiff-api` | Daemon source | Published as `@neuraldiff/daemon` |
| `neuraldiff-sdk` | JavaScript/TypeScript SDK | Programmatic client package |
| `neuraldiff-web` | Dashboard and hosted app routes | Next.js application |
| `neuraldiff-docs` | Documentation | This repository |
| `neuraldiff-testbed` | Sample apps for testing | Local development harness |

> `neuraldiff-mcp` is deprecated. MCP now ships inside the daemon.
