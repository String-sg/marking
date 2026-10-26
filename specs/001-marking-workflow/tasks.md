# Tasks: Dependable local marking workflow

**Input**: [spec.md](spec.md), [plan.md](plan.md), [research.md](research.md),
[data-model.md](data-model.md), [contracts/workflow.md](contracts/workflow.md).

**Status**: All 25 remediation tasks are outstanding. Documentation and successful
bug reproductions do not constitute application fixes. Focused tests are required
by the specification/constitution for consequential changes; write them first and
observe the relevant failure before implementing the fix. Paths are repository-relative.

## Phase 1: Setup

- [ ] T001 Declare tested Python/runtime and development dependencies, including numpy, python-docx, openpyxl, pytest, browser test tooling, and TestClient transport, in `requirements.txt`, a new `requirements-dev.txt`, and `README.md`; verify clean install without extra manual packages (FR-001, FR-014; R01).
- [ ] T002 Repair the missing `Optional` import in `app/core/pdf_processor.py` and `List` import in `app/core/doc_parser.py` and add an unmodified fresh-process application import check in `tests/test_startup.py`; no builtins injection in production/tests (FR-001; R01).

## Phase 2: Foundation

**Goal**: Safe tests and explicit persistence semantics before story implementation.

- [ ] T003 Add temporary DB/upload/processed/report/OAuth path fixtures and outbound-network denial in `tests/conftest.py`; isolate `tests/test_google_classroom.py` credential/disconnect operations and make absent external samples explicit skips (FR-014).
- [ ] T004 Add backed-up additive workflow/revision/asset/release migrations and integrity checks in `app/core/db.py` with migration coverage in `tests/test_workflow_migrations.py`; preserve legacy IDs/rows and flag uncertain owners instead of silent deletion/reassignment (FR-003, FR-005, FR-007, FR-011, FR-013).

**Checkpoint**: No real database, assets, client secret, token, or remote service is
touched by a test; migrated data retains stable identities and earlier evidence.

## Phase 3: US1 — Open a private local workstation (P1)

**Independent test**: Fresh startup; five sections under blocked external network;
unrelated origin/device cannot read records or mutate assessments.

- [ ] T005 [P] [US1] Add access/origin/host and protected-asset regressions in `tests/test_local_access.py` plus an offline five-section browser scenario in `tests/browser/test_offline_workstation.py` (FR-001, FR-002; R13, R14).
- [ ] T006 [US1] Default to loopback and implement launcher-bootstrapped teacher sessions, host/origin checks, protected APIs/assets, and fail-closed network/tunnel startup in `app/core/config.py`, new `app/core/local_access.py`, `app/main.py`, `run.py`, `tallus_desktop.py`, and `tunnel_launcher.py`; wire the session bootstrap in `app/static/js/app.js` (FR-002; R13).
- [ ] T007 [P] [US1] Bundle pinned browser dependencies/styles under `app/static/vendor/`, replace remote script/style dependencies in `app/static/index.html`, and verify offline icons/charts/visibility/navigation controls (FR-001; R14).

## Phase 4: US2 — Ingest and mark the correct student scripts (P1)

**Independent test**: Duplicate names across classes, combined scans, omitted/blank
questions, malformed/failed/zero model results, and empty explicit batch selections.

- [ ] T008 [P] [US2] Add synthetic ownership, coverage/maximum, model-failure/zero, empty-scope, and retry regressions in `tests/test_ingestion_boundaries.py` and `tests/test_marking_state.py`; include general/science/Chinese paths and no real model calls (FR-003–FR-006; R02–R04, R06, R07, R15, R17).
- [ ] T009 [US2] Constrain stable-ID/class matching, require ambiguous-owner resolution, validate supplied IDs, replace short temporary IDs, and preserve roster fields in `app/core/db.py`, `app/api/submissions.py`, `app/core/marker_engine.py`, and `app/core/google_classroom.py`; enforce "A scan candidate must not overwrite a trusted student name or class." (FR-003; R07, R17).
- [ ] T010 [US2] Reconcile extraction with the canonical scheme, including blank/missing and Roman-subpart questions; preserve all rubric pages in `app/core/doc_parser.py` with synthetic mixed/scanned PDF coverage in `tests/test_rubric_parser.py`; stop per-submission maximum rewrites in `app/core/marker_engine.py`, `app/markers/base.py`, and `app/core/db.py`; enforce "The assignment maximum comes from its marking scheme, not one script's extracted questions." (FR-004; R03, R15).
- [ ] T011 [US2] Reject malformed/failed/partial evaluation as incomplete, remove fabricated essay defaults/score compression, and preserve explicit zero in `app/markers/base.py`, `app/markers/general.py`, `app/markers/chinese_essay.py`, and `app/core/essay_marker.py`; enforce "A failed or malformed model result cannot be complete; an explicit valid zero remains zero." (FR-005; R02, R04).
- [ ] T012 [US2] Preserve omitted versus explicit-empty scope, validate assignment membership, and return zero work for empty eligibility in `app/api/submissions.py` and `app/api/assignments.py`; enforce "An explicit batch scope must never expand when its eligible set is empty." (FR-006; R06).
- [ ] T013 [US2] Persist/use step outcomes and coverage diagnostics instead of nonempty-content completion heuristics in `app/core/db.py` and `app/api/submissions.py`; preserve prior usable extraction/remarks/annotations on failed retries and between Step 2/Step 3 in `app/core/direct_marker.py` (FR-004, FR-005; R04).

## Phase 5: US3 — Review and approve without losing evidence (P1)

**Independent test**: Page-three evidence round trip; empty assignment selection;
literal quotes/markup; unchanged approved export and analytics inclusion.

- [ ] T014 [P] [US3] Add save/approve metadata and revision tests in `tests/test_review_persistence.py`, and stale-context/literal-text/export browser checks in `tests/browser/test_review_workflow.py` (FR-007–FR-010; R05, R08–R10).
- [ ] T015 [US3] Merge full stored question metadata in review schemas/DAO saves, validate score/state/stale revision, and bind approval to the current complete revision in `app/api/grading.py` and `app/core/db.py`; enforce "Review saves preserve page, bbox, criteria, annotations, and extracted text unless explicitly changed." (FR-007; R08).
- [ ] T016 [US3] Clear active IDs/fields/assets and disable mutations for empty/loading/failed/stale assignment contexts in `app/static/js/app.js`; verify the displayed script/context before save or approval (FR-008; R05).
- [ ] T017 [US3] Replace unsafe imported/generated-text interpolation with literal DOM values/text in `app/static/js/app.js` and escape report/OAuth response text in `app/api/reports.py` and `app/api/google_classroom.py`; preserve exact quotes and markup through save/reload (FR-009; R10).
- [ ] T018 [US3] Remove implicit save-draft from unchanged PDF download and keep report GET/generation paths read-only in `app/static/js/app.js`, `app/api/reports.py`, and `app/core/direct_marker.py`; expose explicit regeneration as a separate mutation when needed and enforce "Unchanged export cannot change assessment revision, approval, or scores." (FR-010; R09).

## Phase 6: US4 — Publish the approved result to its intended student (P1)

**Independent test**: Intended mocked student is second/paginated; pending or
ambiguous work has zero side effects; failed release retries truthfully; rendered
English/Chinese names/feedback and maximums remain readable/consistent.

- [ ] T019 [P] [US4] Add exact-recipient/approval/pagination/permission/retry mocks to `tests/test_google_classroom.py` and synthetic multilingual render assertions/inspection fixtures in `tests/test_report_generation.py` (FR-011, FR-012; R11, R12, R18).
- [ ] T020 [US4] Implement shared single/bulk preflight, exact Google-ID/verified-email association, pagination, durable step outcomes/file reuse, and propagated student-access failures in `app/core/google_classroom.py`, `app/api/google_classroom.py`, and `app/core/db.py`; enforce "Only the current complete approved revision can be released to an exact verified recipient." before upload/share/grade/return (FR-011; R11, R12).
- [ ] T021 [US4] Select/bundle a distributable CJK-capable PDF font and apply it to names, titles, tables, answers, and feedback in `app/core/report_generator.py` and `app/core/direct_marker.py`; verify long/multipage English/Chinese reports against authoritative marks (FR-012; R18).

## Phase 7: US5 — Delete assessment data predictably (P2)

**Independent test**: Submission/student/assignment deletion denies old links and
removes owned assets; one deleted combined-scan script does not break survivors.

- [ ] T022 [P] [US5] Extend `tests/test_bulk_delete.py` and add `tests/test_asset_lifecycle.py` for old URL denial, original/page/thumbnail/report removal, surviving shared references, managed-path checks, and cleanup failure/retry (FR-013; R16).
- [ ] T023 [US5] Implement owned/shared asset references and retryable cleanup in `app/core/db.py` and new `app/core/asset_lifecycle.py`, integrate delete routes in `app/api/students.py`, `app/api/assignments.py`, `app/api/submissions.py`, and protected delivery in `app/main.py`; enforce "Deleted assets cannot be retrieved; shared originals survive while a live submission references them." (FR-013; R16).

## Phase 8: Cross-cutting acceptance

- [ ] T024 Add/run a synthetic PDF → raster → SQLite → deterministic pipeline → review/approve → actual PDF/HTML/analytics → mocked release → deletion scenario in `tests/test_workflow_e2e.py`, then execute `specs/001-marking-workflow/quickstart.md` without startup workarounds (FR-001–FR-014).
- [ ] T025 Update `README.md`, `docs/reviews/2026-10-26-e2e.md`, and `specs/001-marking-workflow/quickstart.md` with actual validation counts/evidence, corrected offline/sharing/deletion claims, supported install instructions, and remaining live OCR/Classroom/platform limits; verify no runtime data or credentials entered Git (FR-014).

## Requirement-to-task mapping

| Requirement | Tasks |
|---|---|
| FR-001 | T001, T002, T005, T007, T024 |
| FR-002 | T005, T006, T024 |
| FR-003 | T004, T008, T009, T024 |
| FR-004 | T008, T010, T013, T024 |
| FR-005 | T004, T008, T011, T013, T024 |
| FR-006 | T008, T012, T024 |
| FR-007 | T004, T014, T015, T024 |
| FR-008 | T014, T016, T024 |
| FR-009 | T014, T017, T024 |
| FR-010 | T014, T018, T024 |
| FR-011 | T004, T019, T020, T024 |
| FR-012 | T019, T021, T024 |
| FR-013 | T004, T022, T023, T024 |
| FR-014 | T001, T003, T024, T025 |

## Dependencies and parallel execution

T001–T002 → T003–T004 → story work → T024 → T025. US1 is the first safe local
increment. US2, US3, US4, and US5 can write their independent tests after foundation
using seeded synthetic states. Final US3 approval integration depends on US2's
explicit completion state; final US4 release depends on US3 approval/revision
semantics; US5 access checks integrate with US1 protected assets. Do not edit the
same DAO, orchestrator, or frontend file concurrently.

Parallel examples, after their prerequisites:

- US1: T006 access helper/launchers and T007 local browser assets are separate file sets.
- US2: T008 ingestion fixtures and its marking-state tests can be split between owners; serialize T009–T013 edits where files overlap.
- US3: T014 API persistence tests and browser scenarios can be authored separately; serialize T016–T018 frontend edits.
- US4: T020 Classroom release and T021 report fonts use separate core modules after T019 tests exist.
- US5: T022 filesystem fixture cases and URL-access cases can be split within test ownership; run T023 after the failing cases are established.

## Implementation strategy

Deliver foundation + US1 as the minimal private local workstation, then US2/US3
assessment correctness and evidence preservation. Add US4 only after approval is
trustworthy, then finish US5 cleanup and full acceptance. Validate each story using
its seeded independent test before integrating. Every checkbox remains unchecked
until its implementation and relevant verification are complete; no real student
data, model quality claim, or live Classroom publication is needed for these tasks.
