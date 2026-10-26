# Implementation Plan: Dependable local marking workflow

**Branch**: `codex/e2e-review-speckit` | **Date**: 2026-10-26 | **Spec**: [spec.md](spec.md)

**Status**: Target design and remediation plan. Application fixes remain outstanding.

## Summary

Repair the existing teacher workflow from local startup through ingestion, marking,
review, reports, optional Classroom release, and deletion. Retain FastAPI, SQLite,
the subject marker registry, local Ollama, and the current browser interface.
Make ownership, incomplete model results, approval, operation scope, and file
cleanup explicit instead of deriving success from nonempty feedback/annotations.

## Technical Context

**Language/Version**: Python; the review ran Python 3.10.8. Target a documented
Python 3.11+ runtime after clean-install verification; browser JavaScript/HTML/CSS.

**Primary Dependencies**: FastAPI/Pydantic, Uvicorn, requests, PyMuPDF/Pillow,
ReportLab, numpy, python-docx, openpyxl, Google API/auth libraries, local Ollama; optional
pywebview desktop launcher. Declare missing document/spreadsheet/test dependencies.

**Storage**: Existing SQLite and `data/uploads`, `data/processed`, `data/reports`,
`data/google`. Add only bounded migration fields/tables needed for workflow state,
approval revisions, release attempts, and asset references.

**Testing**: pytest/TestClient with temporary databases/assets/credentials and
deterministic model/Google substitutes; browser scenarios with external requests
blocked; rendered English/Chinese PDF inspection. No real records or OAuth calls.

**Target Platform**: Local teacher workstation in a browser; retain desktop launcher.
Validate documented supported platforms without treating macOS review as proof of
Windows packaging.

**Project Type**: Single Python application serving a vanilla JavaScript interface.

**Performance Goals**: Preserve sequential GPU marking and bounded retries. Record
model/hardware duration when measured; no invented latency or OCR accuracy target.

**Constraints**: Core operation requires zero external requests; loopback default;
protected opt-in network access; no cloud inference, telemetry, hosted database,
new identity provider, production deployment, or real grade publication in this pass.

**Scale/Scope**: One teacher/device, five existing interface sections, three subject
marker paths, individual/batch/combined scans, local reports and optional Classroom.

## Constitution Check

The pre-research gate permits this documentation-only remediation design because
it introduces no student-data processing or application activation. Existing code
violations remain release blockers; they are not exceptions granted by this plan.

| Principle | Current-code evidence | Target gate after Phase 1 design |
|---|---|---|
| I. Local processing and explicit sharing | Fails: remote UI libraries, all-interface bind, unprotected routes/assets | Pass by design: local assets, loopback, guarded sessions/assets, explicit sharing |
| II. Teacher control | Fails: invented/incomplete scoring, approval bypass, export mutation | Pass by design: explicit incomplete state, revision-bound approval, pure export, release preflight |
| III. Stable ownership/evidence | Fails: cross-class matching, roster overwrite, lost page/bbox, maximum rewrite | Pass by design: stable IDs, ambiguity resolution, metadata merge, scheme maximum |
| IV. Bounded/recoverable operations | Fails: empty batches widen, stale review, retained deleted files | Pass by design: immutable scope, context guards, retained prior evidence, asset references |
| V. Evidence before readiness | Fails: credential-mutating tests, startup workaround, limited journey checks | Pass by design: isolated fixtures, zero real network, unmodified startup, labelled browser/PDF evidence |

**Post-design result**: No architectural exception or unresolved design choice.
Target gates pass on paper only. Every current-code failure needs implementation
and validation before a readiness claim or network mode with real records.

## Project Structure

```text
specs/001-marking-workflow/
├── spec.md
├── plan.md
├── research.md
├── data-model.md
├── contracts/workflow.md
├── quickstart.md
└── tasks.md
app/
├── main.py                  # startup, access boundary, protected assets
├── api/                     # existing ingestion/review/report/Classroom routes
├── core/                    # SQLite, parsers, Ollama, reports, asset lifecycle
├── markers/                 # general, science, Chinese essay
└── static/                  # existing HTML/JS/CSS and local browser dependencies
tests/                      # isolated fixtures and focused/journey checks
requirements.txt
run.py
tallus_desktop.py
tunnel_launcher.py
```

**Structure Decision**: Keep the current directories. Add focused shared helpers
where necessary; avoid a service-layer rewrite. Back up and migrate SQLite in
place, preserving stable IDs and uncertain legacy ownership for explicit resolution.

## Delivery and Traceability

| Increment | Requirements | Validation |
|---|---|---|
| Foundation + US1 | FR-001, FR-002, FR-014 | Clean startup; five offline sections; denied unrelated origins/devices |
| US2 | FR-003–FR-006 | Duplicate names; omitted/blank questions; malformed/failed/zero scores; bounded empty batches |
| US3 | FR-007–FR-010 | Metadata round trip; stale context; literal text; unchanged approved export |
| US4 | FR-011, FR-012 | Exact approved recipient; failed release/retry; readable English/Chinese reports |
| US5 | FR-013 | Deleted links denied; owned assets removed; shared original retained for survivors |

Design details are in [research.md](research.md), [data-model.md](data-model.md),
and [contracts/workflow.md](contracts/workflow.md). Execute [tasks.md](tasks.md)
incrementally and [quickstart.md](quickstart.md) for acceptance. A reproduction or
design document does not complete its remediation task.

## Complexity Tracking

No constitution exceptions. Workflow/release/asset records address observed data
loss and retry behavior inside the existing SQLite application.
