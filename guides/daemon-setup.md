# Daemon Setup

The daemon source lives in the `neuraldiff-api` repository and is published to npm as `@neuraldiff/daemon`.

## Run It

### No Install

```bash
npx @neuraldiff/daemon
```

### Global Install

```bash
npm install -g @neuraldiff/daemon
neuraldiff
```

### Project-Local Install

```bash
npm install --save-dev @neuraldiff/daemon
npx neuraldiff
```

## What Starts

One process starts all local daemon surfaces:

- MCP server on stdio
- local HTTP API on `http://localhost:7878`
- local web UI on `http://localhost:7878`

## Run From Source

If you are developing the daemon itself:

```bash
cd neuraldiff-api
npm install
npm run dev
```

## Environment Variables

The current daemon reads configuration from environment variables:

| Variable | Default | Purpose |
|---|---|---|
| `PORT` | `7878` | HTTP/UI port |
| `HOST` | `0.0.0.0` | Bind address |
| `OUTPUT_DIR` | `./neuraldiff-data` | Local artifact directory |
| `CORS_ORIGINS` | local dev origins | Allowed browser origins |
| `API_KEY` | unset | Optional bearer auth for `/api/*` |
| `NEURALDIFF_CLOUD_URL` | `https://neuraldiff.com` | Hosted app / cloud base URL |
| `ANTHROPIC_API_KEY` | unset | Enables AI-assisted analysis paths |

Example:

```bash
PORT=7878 OUTPUT_DIR=./neuraldiff-data npx @neuraldiff/daemon
```

## Health Check

```bash
curl http://localhost:7878/health
```

Expected fields include:

- `status`
- `version`
- `browserStatus`
- `memoryMB`
- `cloud.connected`

## Data Directory

By default, artifacts are written under `./neuraldiff-data`, including screenshots and QA session output.

## Troubleshooting

### Cannot reach the daemon

Start it directly:

```bash
npx @neuraldiff/daemon
```

### Browser missing

Install Chromium for Playwright:

```bash
npx playwright install chromium
```

### Need the repo/package distinction

Remember:

- change daemon source in `neuraldiff-api`
- run or install the daemon as `@neuraldiff/daemon`
