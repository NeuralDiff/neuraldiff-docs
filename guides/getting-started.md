# Getting Started

This guide walks you through setting up NeuralDiff end-to-end: the daemon (which includes the MCP server) and, optionally, a hosted cloud connection.

> Naming note: `neuraldiff-api` is the daemon source repository. The package users run is `@neuraldiff/daemon`.

## Prerequisites

- **Node.js** 18 or later
- **An AI coding agent** that supports MCP (e.g., Claude Code)
- **A web application** running locally that you want to monitor
- **An API key** (optional, required for cloud analysis features)

## Quick Setup

If you just want to get running fast:

```bash
# Add NeuralDiff to Claude Code
claude mcp add neuraldiff -- npx @neuraldiff/daemon

# Done -- start coding
```

The daemon starts automatically when your AI agent launches. It serves the HTTP API + UI on port 7878 and MCP on stdio -- all from a single process.

The rest of this guide covers configuration, verification, and advanced setup.

## Step 1: Add NeuralDiff to Your AI Agent

The daemon is a single package that includes the MCP server, HTTP API, and web UI. There is no separate MCP package to install.

### Claude Code

```bash
claude mcp add neuraldiff -- npx @neuraldiff/daemon
```

This adds NeuralDiff to your Claude Code MCP configuration. The daemon will start automatically when Claude Code launches, serving both MCP (on stdio) and the HTTP API + UI (on port 7878).

**Verify it is working**:

```bash
claude mcp list
```

You should see `neuraldiff` in the list of registered MCP servers.

### Other MCP-Compatible Agents

Add the following to your agent's MCP configuration file:

```json
{
  "mcpServers": {
    "neuraldiff": {
      "command": "npx",
      "args": ["@neuraldiff/daemon"]
    }
  }
}
```

See [MCP Setup](mcp-setup.md) for detailed configuration options.

## Step 2: Verify the Daemon

When started via MCP, the daemon also launches an HTTP server on port 7878 for the web UI and REST API.

**Verify it is working**:

```bash
curl http://localhost:7878/health
```

Expected response:

```json
{
  "status": "healthy",
  "version": "1.0.0"
}
```

See [Daemon Setup](daemon-setup.md) for configuration, auto-start, and troubleshooting.

## Step 3: Configure Your Project (Optional)

Create a `.neuraldiff.json` file in your project root:

```json
{
  "project": {
    "name": "my-app",
    "baseUrl": "http://localhost:3000"
  },
  "capture": {
    "viewports": [
      { "name": "desktop", "width": 1280, "height": 720 },
      { "name": "mobile", "width": 375, "height": 812 }
    ],
    "waitFor": "networkidle",
    "defaultDelay": 500
  },
  "escalation": {
    "confidenceThreshold": 0.85,
    "maxStage": 4,
    "pixelDiffThreshold": 0.001
  }
}
```

This is optional. Without it, NeuralDiff uses sensible defaults.

## Step 4: Connect To Hosted Cloud Services (Optional)

Cloud features (deep analysis, knowledge base, agent analysis) require an API key.

### Get an API Key

1. Sign up at [https://app.neuraldiff.dev](https://app.neuraldiff.dev)
2. Go to Settings > API Keys
3. Generate a new key

### Configure the Key

Set the environment variable:

```bash
export NEURALDIFF_API_KEY=nd_sk_your_key_here
```

Or add it to your `.neuraldiff.json`:

```json
{
  "cloud": {
    "apiKey": "nd_sk_your_key_here",
    "endpoint": "https://api.neuraldiff.dev"
  }
}
```

Or create a `.env` file in your project root:

```
NEURALDIFF_API_KEY=nd_sk_your_key_here
```

**Verify cloud connectivity**:

```bash
curl -H "Authorization: Bearer nd_sk_your_key_here" \
     https://api.neuraldiff.dev/v1/health
```

See [API Setup](api-setup.md) for the current hosted-service setup boundary.

## Step 5: Verify Everything Works

### Manual Test

Start your web application, then run:

```bash
# Capture a screenshot
curl -X POST http://localhost:7878/api/screenshots \
  -H "Content-Type: application/json" \
  -d '{"id": "homepage-desktop", "url": "http://localhost:3000", "viewport": {"width": 1280, "height": 720}}'

# Check the result
curl http://localhost:7878/api/screenshots
```

### Agent Test

Start Claude Code in your project directory and ask it to check the UI:

```
You: Can you capture a screenshot of the homepage and check for any visual regressions?
```

Claude should call the `neuraldiff_capture_and_compare` tool and report the results.

## What Happens Next

Once set up, NeuralDiff works automatically during AI-assisted development:

1. **The AI agent makes UI changes** -- editing CSS, HTML, React components, etc.
2. **The agent calls NeuralDiff tools** -- capturing screenshots and checking for regressions.
3. **NeuralDiff runs the escalation pipeline** -- fast local checks first, cloud analysis only when needed.
4. **Results are returned to the agent** -- the agent can fix issues immediately or report them to you.

The agent learns to call NeuralDiff tools at the right moments. See [For AI Agents](for-ai-agents.md) for guidance on optimal tool usage patterns.

## Troubleshooting

### "Connection refused" on port 7878

The daemon is not running. Start it with:

```bash
npx @neuraldiff/daemon
```

### "No baseline found"

This is normal on the first capture. The first screenshot becomes the baseline. Subsequent captures will be compared against it.

### MCP server not showing up

Restart your AI agent after adding the MCP server. Check the MCP configuration with:

```bash
claude mcp list
```

### Screenshots are blank or incorrect

Make sure your web application is running and accessible at the configured URL. Test it in a regular browser first.

Check that the daemon can reach it:

```bash
curl http://localhost:3000  # or whatever your app URL is
```

### Cloud analysis not working

Verify your API key:

```bash
curl -H "Authorization: Bearer $NEURALDIFF_API_KEY" \
     https://api.neuraldiff.dev/v1/health
```

Check that `sendScreenshotsToCloud` is not disabled in your privacy settings.

## Next Steps

- [MCP Setup](mcp-setup.md) -- Detailed MCP server configuration
- [Daemon Setup](daemon-setup.md) -- Daemon configuration and management
- [For AI Agents](for-ai-agents.md) -- How agents should use NeuralDiff
- [Architecture Overview](../architecture/overview.md) -- Understand the system design
