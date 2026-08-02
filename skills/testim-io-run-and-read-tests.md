---
name: testim-io-run-and-read-tests
description: Trigger a Testim test, suite, label, or test plan remotely and read back the execution results.
api: Testim.io Public API
base_url: https://api.testim.io
auth: 'Authorization: Bearer PAK-<key>  (Pro-tier project; keys from Settings > API)'
operations:
  - POST /tests/run/{testId}
  - POST /suites/run/{suiteId}
  - POST /labels/run/{label}
  - POST /test-plans/run/{planId}
  - GET /runs/executions
  - GET /v2/runs/executions/{executionId}
  - GET /runs/tests/{testResultId}
---

# Run a Testim test and read its results

Use this skill to kick off a remote Testim run and poll for results.

## Steps

1. **Authenticate.** Send every request with `Authorization: Bearer PAK-<key>`.
   The public REST API requires a Professional-tier project.
2. **Pick what to run.** Choose one entry point:
   - a single test: `POST /tests/run/{testId}`
   - a suite: `POST /suites/run/{suiteId}`
   - all tests with a label: `POST /labels/run/{label}`
   - a test plan: `POST /test-plans/run/{planId}`
   In the JSON body set `branch` (default `master`), `grid`, `parallel`,
   optional `baseUrl`, `params`, `resultLabels`, `retries`, and `timeout`.
   You must supply a Grid or Grid ID.
3. **Capture the executionId.** Each run responds with `{ executionId, metaData }`.
4. **Poll the execution.** Call `GET /v2/runs/executions/{executionId}`
   (paginated with `page`/`pageSize`) to read per-test status
   (passedCount/failedCount/tests[]), or `GET /runs/executions` with
   `fromDate`/`toDate`/`status`/`branch` filters to find executions.
5. **Drill into a failure.** For a single failing test, call
   `GET /runs/tests/{testResultId}?stepsResults=true&runParams=true` to get
   step-level results, error messages, and screenshots.

## Rules
- Correlate every call by `metaData.requestId` when contacting support.
- On `429 TooManyRequests`, back off — limits apply per project and per key.
- Errors return `{ requestId, error: { message, status } }` (not RFC 9457).
