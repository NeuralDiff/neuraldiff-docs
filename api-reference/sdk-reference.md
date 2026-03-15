# SDK Reference

The NeuralDiff SDK (`@neuraldiff/sdk`) provides programmatic access to NeuralDiff capabilities. It can be used independently of the MCP server and daemon for custom integrations, CI/CD pipelines, and testing frameworks.

## Installation

```bash
npm install @neuraldiff/sdk
```

## Quick Start

```typescript
import { NeuralDiff } from '@neuraldiff/sdk';

const nd = new NeuralDiff({
  baseUrl: 'http://localhost:3000',
  daemonUrl: 'http://localhost:7100',
});

// Capture and compare
const result = await nd.captureAndCompare('/settings', {
  viewport: 'desktop',
  context: 'Changed button color',
});

if (result.status === 'fail') {
  console.log('Regression detected:', result.summary);
  console.log('Suggestion:', result.suggestion);
}
```

## Classes

### `NeuralDiff`

The main SDK class. Manages configuration and provides high-level methods for capture and comparison.

#### Constructor

```typescript
new NeuralDiff(options: NeuralDiffOptions)
```

**Options**:

```typescript
interface NeuralDiffOptions {
  // Required
  baseUrl: string;           // Base URL of the web application
  daemonUrl?: string;        // Daemon URL (default: http://localhost:7100)

  // Optional
  apiKey?: string;           // Cloud API key
  apiEndpoint?: string;      // Cloud API URL
  projectName?: string;      // Project name

  // Capture defaults
  defaultViewport?: Viewport;  // Default viewport (default: desktop 1280x720)
  defaultWaitFor?: WaitStrategy; // Default wait strategy (default: networkidle)
  defaultDelay?: number;       // Default delay in ms (default: 500)

  // Escalation
  confidenceThreshold?: number; // Min confidence (default: 0.85)
  maxStage?: number;            // Max escalation stage (default: 4)
}
```

#### Methods

##### `captureAndCompare(path, options?)`

Capture a screenshot and compare against the baseline.

```typescript
async captureAndCompare(
  path: string,
  options?: CaptureCompareOptions
): Promise<CompareResult>
```

**Parameters**:

```typescript
interface CaptureCompareOptions {
  viewport?: ViewportPreset | Viewport;  // "desktop", "tablet", "mobile", or {width, height}
  selector?: string;                      // CSS selector
  context?: string;                       // Change description
  fullPage?: boolean;                     // Capture full page
  waitFor?: WaitStrategy;                // "load", "domcontentloaded", "networkidle"
  delay?: number;                         // Additional delay in ms
  threshold?: number;                     // Pixel diff threshold
}
```

**Returns**:

```typescript
interface CompareResult {
  status: 'pass' | 'fail' | 'no_baseline';
  confidence: number;
  summary: string;
  captureId: string;
  pipelineStage: number;

  pixelDiff?: {
    percentage: number;
    changedPixels: number;
    totalPixels: number;
  };

  structuralDiff?: {
    changedRegions: ChangedRegion[];
  };

  patternMatches?: PatternMatch[];
  suggestion?: string;

  screenshots: {
    current: string;    // file path
    baseline?: string;  // file path
    diff?: string;      // file path
  };
}
```

**Example**:

```typescript
const result = await nd.captureAndCompare('/settings', {
  viewport: 'desktop',
  context: 'Added dark mode toggle',
});

console.log(result.status);     // "pass" or "fail"
console.log(result.confidence); // 0.87
console.log(result.summary);   // "Layout shift detected..."
```

---

##### `capture(path, options?)`

Capture a screenshot without comparing.

```typescript
async capture(
  path: string,
  options?: CaptureOptions
): Promise<CaptureResult>
```

**Returns**:

```typescript
interface CaptureResult {
  captureId: string;
  screenshotPath: string;
  metadata: {
    url: string;
    viewport: Viewport;
    capturedAt: string;
    pageTitle: string;
    contentHash: string;
    fileSizeBytes: number;
  };
  isNewBaseline: boolean;
}
```

---

##### `compare(captureId, options?)`

Compare a previously captured screenshot against the baseline.

```typescript
async compare(
  captureId: string,
  options?: CompareOptions
): Promise<CompareResult>
```

---

##### `updateBaseline(captureId)`

Accept a capture as the new baseline.

```typescript
async updateBaseline(captureId: string): Promise<BaselineUpdateResult>
```

**Returns**:

```typescript
interface BaselineUpdateResult {
  status: 'updated';
  baselineId: string;
  previousVersion: number;
  newVersion: number;
}
```

---

##### `listBaselines(filter?)`

List existing baselines.

```typescript
async listBaselines(filter?: BaselineFilter): Promise<BaselineListResult>
```

**Parameters**:

```typescript
interface BaselineFilter {
  url?: string;      // URL pattern filter
  branch?: string;   // Git branch filter
}
```

---

##### `getSessionStatus()`

Get the current session status.

```typescript
async getSessionStatus(): Promise<SessionStatus>
```

---

##### `checkHealth()`

Check daemon health.

```typescript
async checkHealth(): Promise<HealthStatus>
```

**Returns**:

```typescript
interface HealthStatus {
  status: 'ok' | 'error';
  version: string;
  uptimeSeconds: number;
  browserStatus: string;
  cloudConnected: boolean;
}
```

---

### `NeuralDiffBatch`

Utility class for running multiple captures and comparisons efficiently.

```typescript
import { NeuralDiff, NeuralDiffBatch } from '@neuraldiff/sdk';

const nd = new NeuralDiff({ baseUrl: 'http://localhost:3000' });
const batch = new NeuralDiffBatch(nd);

// Add pages to check
batch.add('/settings', { viewport: 'desktop' });
batch.add('/settings', { viewport: 'mobile' });
batch.add('/dashboard', { viewport: 'desktop' });
batch.add('/profile', { viewport: 'desktop' });

// Run all checks
const results = await batch.run({
  concurrency: 2,         // Max parallel captures
  context: 'Updated global styles',
  stopOnFailure: false,   // Continue checking even after a failure
});

console.log(`Passed: ${results.passed}`);
console.log(`Failed: ${results.failed}`);
console.log(`Total: ${results.total}`);

for (const failure of results.failures) {
  console.log(`${failure.url} (${failure.viewport}): ${failure.summary}`);
}
```

#### Constructor

```typescript
new NeuralDiffBatch(client: NeuralDiff)
```

#### Methods

##### `add(path, options?)`

Add a page to the batch.

```typescript
add(path: string, options?: CaptureCompareOptions): void
```

##### `run(options?)`

Run all checks in the batch.

```typescript
async run(options?: BatchOptions): Promise<BatchResult>
```

**Parameters**:

```typescript
interface BatchOptions {
  concurrency?: number;    // Max parallel captures (default: 2)
  context?: string;        // Shared context for all checks
  stopOnFailure?: boolean; // Stop on first failure (default: false)
}
```

**Returns**:

```typescript
interface BatchResult {
  passed: number;
  failed: number;
  noBaseline: number;
  total: number;
  results: CompareResult[];
  failures: CompareResult[];
  duration: number;        // Total duration in ms
}
```

---

### `NeuralDiffCI`

Helper class for CI/CD integration.

```typescript
import { NeuralDiffCI } from '@neuraldiff/sdk';

const ci = new NeuralDiffCI({
  baseUrl: process.env.APP_URL || 'http://localhost:3000',
  apiKey: process.env.NEURALDIFF_API_KEY,
  failOnRegression: true,
});

// Run visual regression suite
const result = await ci.run([
  { path: '/', name: 'Homepage' },
  { path: '/settings', name: 'Settings' },
  { path: '/dashboard', name: 'Dashboard' },
  { path: '/profile', name: 'Profile' },
]);

// Outputs a summary report
ci.printReport(result);

// Exit with non-zero if regressions found
process.exit(result.failed > 0 ? 1 : 0);
```

#### Constructor

```typescript
new NeuralDiffCI(options: CIOptions)
```

**Options**:

```typescript
interface CIOptions extends NeuralDiffOptions {
  failOnRegression?: boolean;  // Exit non-zero on regression (default: true)
  viewports?: ViewportPreset[]; // Viewports to check (default: ["desktop"])
  reportFormat?: 'text' | 'json' | 'junit'; // Report format (default: "text")
  artifactDir?: string;        // Directory to save screenshots (default: ./neuraldiff-artifacts)
}
```

#### Methods

##### `run(pages)`

Run visual regression checks on a list of pages.

```typescript
async run(pages: PageSpec[]): Promise<CIResult>
```

##### `printReport(result)`

Print a formatted report to stdout.

```typescript
printReport(result: CIResult): void
```

## Type Definitions

### Viewport

```typescript
interface Viewport {
  width: number;
  height: number;
}

type ViewportPreset = 'desktop' | 'desktop-hd' | 'tablet' | 'mobile' | 'mobile-landscape';
```

### WaitStrategy

```typescript
type WaitStrategy = 'load' | 'domcontentloaded' | 'networkidle';
```

### ChangedRegion

```typescript
interface ChangedRegion {
  name: string;
  type: 'layout_shift' | 'content_change' | 'style_change' | 'new_element' | 'removed_element';
  magnitude?: string;
  description?: string;
  boundingBox?: { x: number; y: number; width: number; height: number };
  confidence: number;
  status?: 'expected' | 'unexpected';
}
```

### PatternMatch

```typescript
interface PatternMatch {
  patternId: string;
  name: string;
  confidence: number;
  solution?: string;
}
```

## Integration Examples

### Jest Integration

```typescript
import { NeuralDiff } from '@neuraldiff/sdk';

describe('Visual Regression Tests', () => {
  let nd: NeuralDiff;

  beforeAll(() => {
    nd = new NeuralDiff({
      baseUrl: 'http://localhost:3000',
    });
  });

  it('settings page has no regressions', async () => {
    const result = await nd.captureAndCompare('/settings', {
      viewport: 'desktop',
    });
    expect(result.status).toBe('pass');
  });

  it('dashboard renders correctly on mobile', async () => {
    const result = await nd.captureAndCompare('/dashboard', {
      viewport: 'mobile',
    });
    expect(result.status).toBe('pass');
  });
});
```

### Playwright Integration

```typescript
import { test, expect } from '@playwright/test';
import { NeuralDiff } from '@neuraldiff/sdk';

const nd = new NeuralDiff({ baseUrl: 'http://localhost:3000' });

test('visual regression check after interaction', async ({ page }) => {
  await page.goto('http://localhost:3000/settings');
  await page.click('#dark-mode-toggle');
  await page.waitForTimeout(500);

  const result = await nd.captureAndCompare('/settings', {
    context: 'After toggling dark mode',
  });

  expect(result.status).toBe('pass');
});
```

### GitHub Actions

```yaml
# .github/workflows/visual-regression.yml
name: Visual Regression
on: [pull_request]

jobs:
  visual-check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'

      - run: npm ci
      - run: npx playwright install chromium

      - name: Start app
        run: npm start &
        env:
          PORT: 3000

      - name: Start NeuralDiff daemon
        run: npx @neuraldiff/daemon > /tmp/neuraldiff.log 2>&1 &

      - name: Run visual regression checks
        run: npx neuraldiff-ci
        env:
          NEURALDIFF_API_KEY: ${{ secrets.NEURALDIFF_API_KEY }}
          APP_URL: http://localhost:3000

      - name: Upload artifacts
        if: failure()
        uses: actions/upload-artifact@v4
        with:
          name: neuraldiff-artifacts
          path: ./neuraldiff-artifacts/
```
