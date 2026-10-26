# Working on Tallus

Tallus is a local teacher workstation: FastAPI, SQLite, plain JavaScript, local
Ollama, and optional Google Classroom integration. Read `README.md` and
`.specify/memory/constitution.md` before consequential changes.

## Scope and source of truth

- Start with `specs/001-marking-workflow/spec.md`, `plan.md`, and `tasks.md` for
  the baseline workflow and known remediation. The baseline defines acceptance;
  it does not certify existing behaviour.
- Use the installed `.agents/skills/speckit-*` workflows for new specifications,
  planning, task breakdown, consistency checks, and convergence. Keep specs close
  to the actual app and use focused fixes instead of unnecessary replatforming.
- Reference the affected requirement and a concrete reproduction in fixes.
- The review at `docs/reviews/2026-10-26-e2e.md` is a snapshot at the recorded
  commit. Revalidate before treating its findings as current.

## Student data and validation

- Keep raw scans, identities, OAuth credentials, runtime databases, and reports
  out of Git and diagnostics. Use synthetic data and temporary directories.
- Before running existing tests in an established installation, isolate all
  runtime data paths. Some tests write OAuth client credentials and disconnect
  the configured account; never point them at a teacher's live data directory.
- Do not contact Google Classroom, publish grades, or operate on real student
  work as part of automated validation. Stub external boundaries unless a live
  test account/course has been explicitly authorised.
- Preserve student ownership, evidence coordinates, approved work, and shared
  source references. Empty selections must remain empty.
- Treat OCR, rubric, roster, and model text as untrusted data when rendering.

## Checks and reporting

- A clean import/startup check precedes the automated suite. Dependencies belong
  in documented runtime/development manifests, not only a local environment.
- Run focused regression checks and the affected full user journey. Marking and
  release fixes need model-failure and recipient/approval checks respectively.
- Verify core UI with external network requests blocked. A warm CDN cache does
  not prove offline readiness.
- Report unmodified results separately from any diagnostic import workaround.
  Mocks prove orchestration, not real handwriting accuracy or live grade release.

## Spec Kit provenance

Initial scaffolding was generated with Specify CLI `1.2.3.dev0`, Codex skills
integration, and shell scripts on 2026-10-26. It includes shared scripts/templates,
a constitution, and a filled workflow baseline. `.specify/feature.json` is local
selection state and is ignored; use `SPECIFY_FEATURE_DIRECTORY=specs/001-marking-workflow` when
selecting the baseline in a fresh checkout. Review upstream changes before
refreshing generated assets. Track `.agents/skills/`; do not commit agent caches,
session state, or credentials.
