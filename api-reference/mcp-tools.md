# MCP Tools Reference

The NeuralDiff MCP server exposes the following tools to AI agents. These tools are the primary interface for agents to interact with NeuralDiff.

## Tool Index

| Tool | Description | Common Usage |
|---|---|---|
| `neuraldiff_capture_and_compare` | Capture and check for regressions | After any UI change |
| `neuraldiff_capture` | Capture a screenshot only | Establishing baselines |
| `neuraldiff_compare` | Compare a capture to baseline | When capture was done separately |
| `neuraldiff_update_baseline` | Accept a capture as new baseline | After intentional changes |
| `neuraldiff_list_baselines` | List existing baselines | Understanding current state |
| `neuraldiff_session_status` | Check NeuralDiff status | Debugging connection issues |

---

## `neuraldiff_capture_and_compare`

The primary tool. Captures a screenshot and compares it against the baseline in one call. Use this tool after making any change that could affect the UI.

### Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `url` | string | Yes | The URL to capture |
| `viewport` | string | No | `"desktop"` (default), `"tablet"`, or `"mobile"` |
| `selector` | string | No | CSS selector to capture a specific element |
| `context` | string | No | What changed and why (improves analysis accuracy) |
| `full_page` | boolean | No | Capture the full scrollable page (default: false) |

### Returns

| Field | Type | Description |
|---|---|---|
| `status` | string | `"pass"`, `"fail"`, or `"no_baseline"` |
| `confidence` | number | 0.0 to 1.0 |
| `summary` | string | Human-readable result summary |
| `details` | object | Structured diff data |
| `suggestion` | string | Recommended action (if regression detected) |
| `capture_id` | string | ID for this capture (can be used with `update_baseline`) |

### Examples

**Basic usage after a style change**:

```
Tool: neuraldiff_capture_and_compare
Arguments:
  url: "http://localhost:3000/settings"
  context: "Changed the primary button color from blue to green"
```

Result:
```json
{
  "status": "pass",
  "confidence": 0.92,
  "summary": "Style change detected in button region. Matches declared intent (color change). No regressions.",
  "capture_id": "cap_a1b2c3d4"
}
```

**Checking a specific component**:

```
Tool: neuraldiff_capture_and_compare
Arguments:
  url: "http://localhost:3000/dashboard"
  selector: ".stats-widget"
  context: "Added a new 'Revenue' stat to the stats widget"
```

Result:
```json
{
  "status": "pass",
  "confidence": 0.88,
  "summary": "New element detected in stats-widget region. Matches declared intent (new stat). Layout adjustment is proportional.",
  "capture_id": "cap_e5f6g7h8"
}
```

**Detecting a regression**:

```
Tool: neuraldiff_capture_and_compare
Arguments:
  url: "http://localhost:3000/settings"
  context: "Added dark mode toggle to settings page"
```

Result:
```json
{
  "status": "fail",
  "confidence": 0.87,
  "summary": "Layout shift detected in sidebar region (12px horizontal). This does not match the declared intent of adding a dark mode toggle.",
  "details": {
    "changed_regions": [
      {
        "name": "settings_panel",
        "type": "style_change",
        "status": "expected"
      },
      {
        "name": "sidebar",
        "type": "layout_shift",
        "magnitude": "12px horizontal",
        "status": "unexpected"
      }
    ]
  },
  "suggestion": "Check CSS custom property --sidebar-width inheritance. Dark mode changes may be cascading into the sidebar container.",
  "capture_id": "cap_i9j0k1l2"
}
```

**Mobile viewport check**:

```
Tool: neuraldiff_capture_and_compare
Arguments:
  url: "http://localhost:3000/settings"
  viewport: "mobile"
  context: "Added dark mode toggle -- checking mobile layout"
```

**Full page capture**:

```
Tool: neuraldiff_capture_and_compare
Arguments:
  url: "http://localhost:3000/blog/my-post"
  full_page: true
  context: "Updated blog post layout with new sidebar widget"
```

---

## `neuraldiff_capture`

Capture a screenshot without comparing. Useful for establishing initial baselines or capturing state before making changes.

### Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `url` | string | Yes | The URL to capture |
| `viewport` | string | No | `"desktop"` (default), `"tablet"`, or `"mobile"` |
| `selector` | string | No | CSS selector |
| `full_page` | boolean | No | Capture full page |

### Returns

| Field | Type | Description |
|---|---|---|
| `capture_id` | string | ID for this capture |
| `screenshot_path` | string | Local file path to the screenshot |
| `is_new_baseline` | boolean | True if this became the initial baseline |

### Example

```
Tool: neuraldiff_capture
Arguments:
  url: "http://localhost:3000/settings"
```

Result:
```json
{
  "capture_id": "cap_m3n4o5p6",
  "screenshot_path": "/home/user/.neuraldiff/projects/proj_xyz/captures/sess_123/1705312200_settings_desktop.png",
  "is_new_baseline": true
}
```

---

## `neuraldiff_compare`

Compare a previously captured screenshot against the baseline. Use this when you have already captured a screenshot with `neuraldiff_capture` and want to run comparison separately.

### Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `capture_id` | string | Yes | ID from a previous `neuraldiff_capture` call |
| `threshold` | number | No | Pixel diff sensitivity threshold (0.0 to 1.0, default 0.001) |

### Returns

Same as `neuraldiff_capture_and_compare`.

### Example

```
Tool: neuraldiff_compare
Arguments:
  capture_id: "cap_m3n4o5p6"
```

---

## `neuraldiff_update_baseline`

Accept a capture as the new baseline. Use this after confirming that a visual change is intentional.

### Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `capture_id` | string | Yes | ID of the capture to promote to baseline |

### Returns

| Field | Type | Description |
|---|---|---|
| `status` | string | `"updated"` |
| `baseline_id` | string | ID of the updated baseline |
| `previous_version` | number | Previous baseline version number |
| `new_version` | number | New baseline version number |

### Example

```
Tool: neuraldiff_update_baseline
Arguments:
  capture_id: "cap_i9j0k1l2"
```

Result:
```json
{
  "status": "updated",
  "baseline_id": "bl_abc123",
  "previous_version": 3,
  "new_version": 4
}
```

**When to use this**: After a `capture_and_compare` returns `"fail"` and you have confirmed with the user that the change is intentional. For example, after a redesign or intentional layout change.

---

## `neuraldiff_list_baselines`

List all existing baselines for the current project.

### Parameters

| Parameter | Type | Required | Description |
|---|---|---|---|
| `url_filter` | string | No | Filter baselines by URL (partial match) |

### Returns

| Field | Type | Description |
|---|---|---|
| `baselines` | array | Array of baseline metadata objects |
| `total` | number | Total number of baselines |

### Example

```
Tool: neuraldiff_list_baselines
Arguments:
  url_filter: "/settings"
```

Result:
```json
{
  "baselines": [
    {
      "id": "bl_abc123",
      "url": "http://localhost:3000/settings",
      "viewport": "desktop",
      "branch": "main",
      "version": 3,
      "updated_at": "2025-01-15T10:30:00Z"
    },
    {
      "id": "bl_def456",
      "url": "http://localhost:3000/settings",
      "viewport": "mobile",
      "branch": "main",
      "version": 1,
      "updated_at": "2025-01-10T08:00:00Z"
    }
  ],
  "total": 2
}
```

---

## `neuraldiff_session_status`

Check the current NeuralDiff session status, including daemon health and recent results.

### Parameters

None.

### Returns

| Field | Type | Description |
|---|---|---|
| `daemon_status` | string | `"ok"` or `"error"` |
| `session_id` | string | Current session ID |
| `cloud_connected` | boolean | Whether cloud API is reachable |
| `recent_results` | array | Last 5 check results |

### Example

```
Tool: neuraldiff_session_status
Arguments: {}
```

Result:
```json
{
  "daemon_status": "ok",
  "session_id": "sess_abc123",
  "cloud_connected": true,
  "recent_results": [
    {
      "url": "http://localhost:3000/settings",
      "viewport": "desktop",
      "status": "pass",
      "timestamp": "2025-01-15T10:25:00Z"
    },
    {
      "url": "http://localhost:3000/settings",
      "viewport": "desktop",
      "status": "fail",
      "summary": "Layout shift in sidebar",
      "timestamp": "2025-01-15T10:15:00Z"
    }
  ]
}
```

---

## Tool Result Format

All tool results are returned as MCP tool results with a `content` array containing text blocks:

```json
{
  "content": [
    {
      "type": "text",
      "text": "{\n  \"status\": \"pass\",\n  \"confidence\": 0.95,\n  \"summary\": \"No visual regressions detected.\"\n}"
    }
  ]
}
```

The text content is always JSON-formatted for easy parsing by the agent.

## Error Results

When a tool encounters an error, it returns an error result:

```json
{
  "content": [
    {
      "type": "text",
      "text": "{\n  \"error\": \"daemon_not_running\",\n  \"message\": \"Cannot connect to NeuralDiff daemon at http://localhost:7878. Start it with: npx @neuraldiff/daemon\"\n}"
    }
  ],
  "isError": true
}
```

The `isError` flag helps agents distinguish errors from normal results.
