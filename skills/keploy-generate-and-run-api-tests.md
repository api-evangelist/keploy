---
name: keploy-generate-and-run-api-tests
description: Generate AI-powered API test suites for a Keploy app from its spec, wait for the generation job, run the suites, and read the report.
api: Keploy Cloud API
generated: '2026-09-03'
method: generated
source: openapi/keploy-cloud-api-openapi.yaml + https://keploy.io/docs/running-keploy/public-api/
operations: [listApps, generateTestSuites, getJob, streamJobEvents, listTestSuites, runTestSuites, getTestRun, listTestReports, getTestReport, getTestReportFull]
---

# Generate and run Keploy API tests

Auth: send a scoped key on every call — `Authorization: Bearer kep_...` or `X-API-Key: kep_...`. Generation and runs need `write` scope. Base URL `https://api.keploy.io/client/v1`.

1. Find the app: `listApps` (`GET /apps?offset=0&limit=20`, max limit 100). Take `appId`.
2. Start generation: `generateTestSuites` (`POST /apps/{appId}/test-suites/generate`). Returns a job — capture `job_id` from `data`.
3. Wait: poll `getJob` (`GET /jobs/{jobId}`) or stream `streamJobEvents` (NDJSON). Cancel with `stopJob` if needed.
4. List what was generated: `listTestSuites` (`GET /apps/{appId}/test-suites?page_size=10` — cursor pagination, follow `pagination.next_cursor` via `after`).
5. Run: `runTestSuites` (`POST /apps/{appId}/test-suites/run`). Track the run with `getTestRun`.
6. Read results: `listTestReports` then `getTestReport` (`GET /apps/{appId}/test-reports/{reportId}`) for the cheap rollup; `getTestReportFull` only when you need per-test-case diffs and mock mismatches.

Rules: every response is an envelope `{data, error, meta}` — on error read `error.code` (see errors/keploy-problem-types.yml) and keep `meta.request_id` for support. Respect `X-RateLimit-Remaining` and back off per `Retry-After` on 429 (100 req/min per key). No idempotency keys exist: do not blind-retry `POST` generate/run calls; re-check job state first.
