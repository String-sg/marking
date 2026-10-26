# Validation Quickstart

Acceptance guide for planned remediation, not a claim of current passes. Use a
fresh disposable checkout with synthetic records and no OAuth credentials. Existing
Google tests overwrite client secrets and delete tokens; do not run them in a
configured teacher installation.

## Setup

Use Python 3.11+, a browser, and synthetic PDFs/images/rosters. Install packages
before offline acceptance. Deterministic tests require no real Google/local-model
connection. After T001 declares development requirements, install those too.

```bash
python3 -m venv .venv
.venv/bin/python -m pip install -r requirements.txt
.venv/bin/python -c 'from app.main import app; print(app.title)'
export SPECIFY_FEATURE_DIRECTORY=specs/001-marking-workflow
bash .specify/scripts/bash/check-prerequisites.sh --json --require-tasks --include-tasks
```

The import check must run unmodified. An injected `Optional` or manually added
undeclared dependency is diagnostic evidence, not a clean-install pass. Spec Kit
CLI used for this baseline: `1.2.3.dev0`; the actual Git branch remains
`codex/e2e-review-speckit` regardless of a setup script's virtual feature label.

Before pytest, implement T003 temporary-path/network fixtures and confirm there
are no configured teacher credential files in this disposable checkout. Then run:

```bash
.venv/bin/python -m pytest -q
HOST=127.0.0.1 .venv/bin/python run.py
```

The launch command is for the synthetic workstation after access/startup fixes.
Do not pass `--tunnel`, upload credentials, or use a school database. Confirm only
loopback listening. Deterministic tests deny/mock external Ollama/Google calls.

## Synthetic acceptance scenarios

| Story | Action | Expected evidence |
|---|---|---|
| US1 | Block external requests; navigate Dashboard, Assignments, Processing, Review, Students; try another origin/device | Five usable sections, zero external requests; unrelated access/mutations denied |
| US2 | Two Jordan Lees in different classes; upload/split three pages; inject blank/omitted answers and malformed/failed/zero responses | Correct owner or visible ambiguity; roster unchanged; coverage complete or visibly incomplete; zero stays zero |
| US2 | Empty explicit assignment/submission selection while another assignment has pending work | Zero processed; other assignment unchanged |
| US3 | Save/approve/reload page-three question with bbox/criteria/annotations; choose empty assignment | Evidence unchanged; no stale script or enabled mutations |
| US3 | Save quotes and `</textarea><script>window.syntheticProbe=1</script>` as answer/name/feedback; view HTML | Exact literal text, no execution or unintended mutation |
| US3 | Export unchanged approved work; refresh history/cohort analytics | Revision, approval, scores, and inclusion unchanged |
| US4 | Intended mocked Classroom student second; pending/incomplete/missing-ID/paginated/permission-failed/retry cases | Approved exact recipient only; blocked preflight has zero side effects; truthful retry state |
| US4 | Render English/Chinese reports with long feedback and multiple pages | Visually readable names/feedback, consistent maximum, no missing glyphs/clipping |
| US5 | Delete one combined-scan script, then the remaining assignment; revisit old links | Deleted content inaccessible; surviving source works; final unreferenced assets removed |

See [contracts/workflow.md](contracts/workflow.md) for rejection/state behavior and
[data-model.md](data-model.md) for persistence invariants. Test files named in
[tasks.md](tasks.md) are implementation targets, not existing passed checks.

## Evidence and live limits

Record commands/environment, synthetic fixture IDs, counts, network observations,
browser screenshots, and rendered report inspection. Label missing/skipped samples,
mocks, and diagnostic workarounds. Verify runtime files/credentials stayed out of Git.

Real scoring accuracy requires agreed labelled samples, rubric, model, and hardware.
A live Classroom round trip requires an explicitly approved test account/course
and recipient. Neither is authorized or established by this guide.
