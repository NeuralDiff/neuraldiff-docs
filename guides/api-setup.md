# Cloud API Setup

This guide is about connecting NeuralDiff to hosted services. It is **not** a guide for the `neuraldiff-api` repository, because that repository contains the daemon source and publishes `@neuraldiff/daemon`.

## Naming

- `neuraldiff-api`: daemon source repo
- `@neuraldiff/daemon`: published npm package
- "Cloud API": hosted NeuralDiff endpoints used for auth, verification, and any cloud-assisted workflows

## Daemon Connection

The daemon reads `NEURALDIFF_CLOUD_URL` and expects hosted endpoints under `${NEURALDIFF_CLOUD_URL}/api/v1`.

Example:

```bash
NEURALDIFF_CLOUD_URL=https://neuraldiff.com npx @neuraldiff/daemon
```

From the local UI at `http://localhost:7878`, users can connect their account and verify API keys against the hosted app.

## Web App Connection

`neuraldiff-web` uses deployment-specific environment variables for backend access. In this workspace, the important ones are:

- `NEXT_PUBLIC_API_URL`
- `NEURALDIFF_API_URL`
- `NEURALDIFF_API_KEY`

Set those in `neuraldiff-web/.env.local` for your environment.

## Self-Hosting Note

The current workspace does not contain a standalone self-hostable cloud API implementation or the older Python/FastAPI service described in previous drafts of the docs. If you are introducing or documenting a self-hosted backend, document the actual service and deployment path next to the code that owns it.
