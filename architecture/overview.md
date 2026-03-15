# System Architecture Overview

NeuralDiff uses a local-first architecture. The daemon runs on the developer machine, exposes MCP plus a local HTTP/UI surface from one process, and can talk to hosted NeuralDiff services when local checks are not enough.

## Naming

- **Source repo**: `neuraldiff-api`
- **Published package**: `@neuraldiff/daemon`
- **Hosted services**: "NeuralDiff Cloud" or deployment-specific hosted endpoints

The most important distinction is that `neuraldiff-api` is a repository name, not the name of a standalone cloud API product.

## Component Map

```text
+---------------------------+
| AI Agent                  |
| Claude Code / Cursor      |
+-------------+-------------+
              |
              | MCP (stdio)
              v
+-------------+-------------+
| @neuraldiff/daemon        |
| source: neuraldiff-api    |
|                           |
| - MCP server              |
| - Local HTTP API          |
| - Local web UI            |
| - Screenshot capture      |
| - Local compare / QA      |
+-------------+-------------+
              |
              | HTTPS
              v
+-------------+-------------+
| Hosted NeuralDiff         |
| services                  |
|                           |
| - Auth and API key verify |
| - Optional cloud analysis |
| - Deployment-specific     |
|   workflow endpoints      |
+---------------------------+

+---------------------------+
| neuraldiff-web            |
| Dashboard / hosted app    |
+---------------------------+
```

## Component Responsibilities

### Daemon (`@neuraldiff/daemon`, source in `neuraldiff-api`)

The daemon is the single local runtime. When started, it:

- registers MCP tools in-process
- serves a local HTTP API and UI on port `7878`
- captures screenshots with Playwright
- stores local artifacts in `./neuraldiff-data` by default
- runs local compare and QA workflows
- connects to hosted services when the user signs in or a workflow needs cloud help

Because MCP is built into the daemon, there is no separate MCP deployment to install.

### Hosted NeuralDiff Services

Hosted services sit behind `NEURALDIFF_CLOUD_URL` and any deployment-specific backend URLs used by the web app. In the current workspace, these services are not represented by a dedicated standalone repository named `neuraldiff-api`.

Hosted responsibilities can include:

- API key verification and account auth
- optional cloud-assisted analysis
- workflow endpoints used by the dashboard
- billing, integrations, and shared project data

### Web Dashboard (`neuraldiff-web`)

The dashboard is a Next.js app that provides:

- projects and runs
- findings and corrections
- integrations and billing
- daemon sign-in flows
- hosted API routes used by the product surface

### SDK (`neuraldiff-sdk`)

The SDK is the programmatic JavaScript/TypeScript client for NeuralDiff integrations.

### Documentation (`neuraldiff-docs`)

Architecture docs, setup guides, API reference, and contributor docs live here.

## Repository Map

| Repository | Role |
|---|---|
| `neuraldiff-api` | Daemon source repo, published as `@neuraldiff/daemon` |
| `neuraldiff-sdk` | SDK package source |
| `neuraldiff-web` | Dashboard and hosted app routes |
| `neuraldiff-docs` | Documentation |
| `neuraldiff-testbed` | Sample applications for local testing |

## Data Boundaries

### Local

- screenshots and QA artifacts
- daemon session state
- local compare results
- MCP traffic between the agent and daemon

### Hosted

- account and organization data
- API key verification
- workflow metadata shared through the dashboard
- any cloud-assisted analysis your deployment enables
