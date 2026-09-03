---
name: keploy-run-load-tests
description: Start a Keploy load test against an app, stream progress, stop it, and read the load test report.
api: Keploy Cloud API
generated: '2026-09-03'
method: generated
source: openapi/keploy-cloud-api-openapi.yaml
operations: [startLoadTest, listLoadTestRuns, streamLoadTestEvents, stopLoadTest, getLoadTestReport]
---

# Run Keploy load tests

Auth: `write` scope key. Base URL `https://api.keploy.io/client/v1`.

1. Start: `startLoadTest` (`POST /apps/{appId}/load-tests`). Capture `runId`.
2. Watch: `streamLoadTestEvents` (`GET /apps/{appId}/load-tests/{runId}/events`, NDJSON — use a streaming client; a 426 means upgrade the connection).
3. Stop early if needed: `stopLoadTest` (`POST /apps/{appId}/load-tests/{runId}/stop`) — this is the documented reversal for a running load test.
4. Results: `getLoadTestReport` (`GET /apps/{appId}/load-tests/{runId}`); history via `listLoadTestRuns`.

Rules: envelope `{data, error, meta}`; 429 back-off per `Retry-After`; a load test hits YOUR application under test — confirm the target app/cluster with `getApp` before starting.
