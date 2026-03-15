# Development Setup

This guide explains how to work on the repositories in this workspace without confusing source repo names with published package names.

## Naming First

- `neuraldiff-api` is the source repository for the daemon.
- `@neuraldiff/daemon` is the npm package published from that repository.
- Hosted NeuralDiff services are deployment targets, not a separate source repo called `neuraldiff-api`.

## Prerequisites

- **Node.js** 18+
- **npm**
- **Git**
- Optional service credentials if you are working on `neuraldiff-web` integrations

## Clone The Repositories

```bash
mkdir neuraldiff && cd neuraldiff

git clone git@github.com:neuraldiff/neuraldiff-api.git
git clone git@github.com:neuraldiff/neuraldiff-sdk.git
git clone git@github.com:neuraldiff/neuraldiff-web.git
git clone git@github.com:neuraldiff/neuraldiff-docs.git
git clone git@github.com:neuraldiff/neuraldiff-testbed.git
```

> The old `neuraldiff-mcp` repository is deprecated. MCP now lives in `neuraldiff-api/src/daemon/mcp-server.ts`.

## Workspace Layout

```text
neuraldiff/
  neuraldiff-api/       # daemon source, published as @neuraldiff/daemon
  neuraldiff-sdk/       # SDK source
  neuraldiff-web/       # dashboard and hosted app routes
  neuraldiff-docs/      # documentation
  neuraldiff-testbed/   # sample apps for local testing
```

## Daemon Repo (`neuraldiff-api`)

```bash
cd neuraldiff-api
npm install
npm run dev
```

Useful commands:

```bash
npm run build
npm test
```

What `npm run dev` starts:

- local daemon HTTP API on `http://localhost:7878`
- local daemon UI on `http://localhost:7878`
- built-in MCP transport on stdio

If you want to run the published package instead of source:

```bash
npx @neuraldiff/daemon
```

## SDK Repo (`neuraldiff-sdk`)

```bash
cd neuraldiff-sdk
npm install
```

Useful commands:

```bash
npm run build
npm test
npm run lint
npm run typecheck
```

## Web Repo (`neuraldiff-web`)

```bash
cd neuraldiff-web
npm install
cp .env.example .env.local
npm run dev
```

Useful commands:

```bash
npm run build
npm run lint
```

The web app is a Next.js project. It expects deployment-specific environment variables such as Supabase, Stripe, and backend URLs. Keep those values in `.env.local`.

## Testbed Repo (`neuraldiff-testbed`)

Use the testbed when you want real apps for crawling, auth, or route-discovery work:

```bash
cd neuraldiff-testbed
npm install
npm run dev:all
```

Or start a single sample app:

```bash
npm run dev:nextjs-supabase
npm run dev:react-spa
npm run dev:angular
```

## Running The Local Stack

Open separate terminals:

```bash
# Terminal 1: daemon from source
cd neuraldiff-api && npm run dev

# Terminal 2: dashboard
cd neuraldiff-web && npm run dev

# Terminal 3: optional sample app
cd neuraldiff-testbed && npm run dev:nextjs-supabase
```

## Quick Verification

```bash
curl http://localhost:7878/health
```

You should see a JSON health response from the daemon.

## Hosted Services Note

This workspace does not contain a standalone self-hostable "cloud API repo" under the name `neuraldiff-api`. If you are documenting or changing hosted backends, document the deployment next to the code that owns that surface instead of reusing old Python/FastAPI instructions.

## Cross-Repo Changes

When a change spans repositories:

1. Start with `neuraldiff-api` for daemon and MCP changes.
2. Update `neuraldiff-sdk` if the programmatic client surface changes.
3. Update `neuraldiff-web` if dashboard or hosted app behavior changes.
4. Update `neuraldiff-docs` last so naming and setup instructions stay aligned.
