# Review evidence, 2026-10-26

Source snapshot: `cca32d23d06a1e57ad1d193ae07b3418cfe95660`.
All records and documents are synthetic. No live model or Google account was used.

| File | Method and limits |
| --- | --- |
| `api-review-results.json` | Temporary SQLite/files, real DAO and route calls, mocked Google API. Process-only missing-`Optional` workaround. |
| `marking-review-results.json` | Actual marker classes with deterministic Ollama responses, synthetic PDF text extraction. Process-only missing-`Optional` workaround. |
| `ui-review-results.json` | Chromium using real HTML/JS, external script tags removed, Lucide stubbed, API responses mocked. Exercises review field rendering and empty-assignment mutation. |
| `offline-review-result.json` | Chromium using untouched HTML/JS, external requests blocked, local assets fulfilled from source and API responses synthetic. |
| `http-workflow-review-results.json` | In-process HTTP via TestClient, temporary SQLite/assets, real PDF rasterisation/reports and orchestrator; extraction/scoring/annotation stages mocked. Process-only missing-`Optional` workaround. |

`success` or `all_probe_assertions_passed` in these records means a probe observed
the documented response. In several cases that response is the defect under review,
not a passing product acceptance check. Diagnostic workarounds never changed app
source and do not repair the unmodified startup/test-collection failure.

Unmodified pytest: 24 collection errors. Exploratory suite additionally supplied
the missing `List`, initialised the fresh database, blocked unmocked HTTP requests,
and used the additional packages listed in the review: 107 passed, 5 failed,
2 skipped. Four failures concern science scheme alignment; one requires an absent
private Windows sample. Full review-host logs and runnable diagnostic probes are
packaged separately for the requesting user.
