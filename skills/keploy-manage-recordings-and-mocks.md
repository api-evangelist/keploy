---
name: keploy-manage-recordings-and-mocks
description: Inspect recorded test sets, test cases, and mocks for a Keploy app; upload, export, or clean up recordings and fix mock mappings.
api: Keploy Cloud API
generated: '2026-09-03'
method: generated
source: openapi/keploy-cloud-api-openapi.yaml + https://keploy.io/docs/running-keploy/public-api/
operations: [listAppsWithRecordings, listRecordings, getRecording, listMocks, getMock, createMock, deleteMock, getTestCase, updateTestCase, getMockMapping, editMockMapping, uploadRecordingBundle, importRecording, exportRecording, getGeneratedSchema]
---

# Manage Keploy recordings and mocks

Auth as in the generate skill; reads need `read`, writes need `write` scope.

1. Which apps have traffic: `listAppsWithRecordings` (`GET /apps/with-recordings`).
2. Enumerate test sets: `listRecordings` (`GET /apps/{appId}/recordings`), then `getRecording` for one set.
3. Inspect content: `listMocks` / `getMock`; `getTestCase` for a case; `getMockMapping` shows which mocks a test case depends on, `editMockMapping` rebinds them.
4. Repair flaky expectations: `updateTestCase` (`PUT .../test-cases/{testCaseId}`) to correct expected responses.
5. Move recordings: `exportRecording` to pull a set, `importRecording` or `uploadRecordingBundle` (atomic set+cases+mocks+mappings; 413 is declared on uploads — keep bundles small) to push.
6. Side effect worth using: `getGeneratedSchema` returns the OpenAPI 3.0.3 schema Keploy inferred from captured traffic.

Rules: deletes (`deleteMock`, and recording deletion) have no documented restore path — treat as permanent (see conventions reversibility). Envelope + rate-limit rules as in the generate skill.
