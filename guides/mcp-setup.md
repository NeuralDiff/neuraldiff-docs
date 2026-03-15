# MCP Server Setup

MCP support is built directly into the NeuralDiff daemon (`@neuraldiff/daemon`). There is no separate MCP package to install. When you run `npx @neuraldiff/daemon`, a single process starts both the MCP server (on stdio) and the HTTP API + web UI (on port 7878).

## Installation

### Claude Code

```bash
claude mcp add neuraldiff -- npx @neuraldiff/daemon
```

To remove:

```bash
claude mcp remove neuraldiff
```

### Manual MCP Configuration

For other MCP-compatible agents, add to your MCP configuration file:

```json
{
  "mcpServers": {
    "neuraldiff": {
      "command": "npx",
      "args": ["@neuraldiff/daemon"],
      "env": {
        "NEURALDIFF_LOG_LEVEL": "info"
      }
    }
  }
}
```

### Using a Specific Version

Pin to a specific version to avoid unexpected updates:

```json
{
  "mcpServers": {
    "neuraldiff": {
      "command": "npx",
      "args": ["@neuraldiff/daemon@0.1.0"]
    }
  }
}
```

## Configuration

### Environment Variables

| Variable | Default | Description |
|---|---|---|
| `NEURALDIFF_PORT` | `7878` | HTTP server port for the web UI and REST API |
| `NEURALDIFF_LOG_LEVEL` | `info` | Log level: `debug`, `info`, `warn`, `error` |
| `NEURALDIFF_TIMEOUT_MS` | `30000` | Timeout for capture operations |
| `NEURALDIFF_PROJECT_DIR` | Current working directory | Project root for config file discovery |

### Project Configuration

The daemon looks for a `.neuraldiff.json` file in the project directory. See [Daemon Setup](daemon-setup.md) for the full configuration reference.

## Available Tools

The MCP server registers the following tools. See [MCP Tools Reference](../api-reference/mcp-tools.md) for complete documentation.

### `neuraldiff_capture`

Capture a screenshot of a URL.

```
Parameters:
  url: string (required) -- The URL to capture
  viewport: string (optional) -- "desktop", "tablet", or "mobile"
  selector: string (optional) -- CSS selector to capture a specific element
  full_page: boolean (optional) -- Capture the full scrollable page
```

### `neuraldiff_compare`

Compare a captured screenshot against the baseline.

```
Parameters:
  capture_id: string (required) -- ID from a previous capture
  threshold: number (optional) -- Pixel diff threshold (0.0 to 1.0)
```

### `neuraldiff_capture_and_compare`

Capture and compare in a single call (most common usage).

```
Parameters:
  url: string (required) -- The URL to capture
  viewport: string (optional) -- "desktop", "tablet", or "mobile"
  selector: string (optional) -- CSS selector
  context: string (optional) -- Description of what changed and why
```

### `neuraldiff_update_baseline`

Accept the current capture as the new baseline.

```
Parameters:
  capture_id: string (required) -- ID of the capture to promote
```

### `neuraldiff_list_baselines`

List all baselines for the current project.

```
Parameters:
  url_filter: string (optional) -- Filter by URL pattern
```

### `neuraldiff_session_status`

Get the current NeuralDiff session status.

```
Parameters: none
```

## How It Works

The MCP server is integrated into the daemon as a single process. When an AI agent starts the daemon via MCP, it communicates over stdio while also serving the HTTP API and web UI on port 7878.

```
Agent                    Daemon (single process)
  |                          |
  | tool_call (stdio):       | HTTP API (:7878)
  |   capture_and_compare    | + Web UI
  | -----------------------> |
  |                          | (internal call)
  |                          | capture, analyze,
  |                          | escalate if needed
  |                          |
  | tool_result (stdio):     |
  |   { content: [          |
  |     { type: "text",     |
  |       text: "..." }     |
  |   ] }                    |
  | <----------------------- |
```

All logic -- MCP tool handling, screenshot capture, analysis, and escalation -- lives in the daemon. There is no separate MCP process.

## Logging

The MCP server logs to stderr (as required by the MCP protocol, since stdout is used for protocol messages).

Set log level with the `NEURALDIFF_LOG_LEVEL` environment variable:

```json
{
  "env": {
    "NEURALDIFF_LOG_LEVEL": "debug"
  }
}
```

Logs include:
- Tool calls received from the agent
- Capture and analysis operations
- Response times and status codes
- Errors and retry attempts

## Lifecycle

Since MCP and the daemon run in the same process, there are no connection management concerns. When the AI agent starts the daemon via MCP, everything is available immediately.

The HTTP server on port 7878 starts alongside the MCP server and remains available as long as the process is running. You can open the web UI at `http://localhost:7878` to view captures, sessions, and settings.

## Troubleshooting

### Tools not appearing in the agent

1. Verify the MCP server is registered:
   ```bash
   claude mcp list
   ```

2. Restart the agent (MCP servers are loaded at startup).

3. Check for startup errors:
   ```bash
   # Run the daemon directly to see error output
   npx @neuraldiff/daemon 2>&1
   ```

### Tool calls timing out

The default timeout is 30 seconds. Increase it if you have slow screenshots:

```json
{
  "env": {
    "NEURALDIFF_TIMEOUT_MS": "60000"
  }
}
```

### Getting debug information

Enable debug logging to see full request/response details:

```json
{
  "env": {
    "NEURALDIFF_LOG_LEVEL": "debug"
  }
}
```
