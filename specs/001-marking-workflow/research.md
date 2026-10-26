# Design Decisions

**Date**: 2026-10-26 | **Evidence**: Repository review and isolated probes recorded
in [the review](../../docs/reviews/2026-10-26-e2e.md). These are target decisions,
not claims that the application already satisfies them.

## 1. Retain the local architecture

**Decision**: Keep FastAPI, SQLite/files, vanilla JavaScript, subject markers, and
Ollama. Declare all runtime dependencies, fix import failures, bundle browser
libraries/styles locally, and verify a documented Python 3.11+ installation.

**Rationale**: Missing `Optional` in `pdf_processor.py`, undeclared document/Excel
dependencies, and remote scripts in `index.html` block startup/offline use.

**Alternatives considered**: Replatforming would expand scope and change data
boundaries without resolving the existing failure modes.

## 2. Protect the existing teacher surface

**Decision**: Loopback default, allowed host/origin checks, teacher sessions for
APIs and student assets. Bootstrap through the local launcher with a per-run
unpredictable credential; use an HttpOnly SameSite session cookie for browser/PDF
access. Refuse network/tunnel mode without an explicitly configured protected
access boundary. No external identity provider is required.

**Rationale**: All-interface binding, unauthenticated routes/static files, and the
quick tunnel expose the whole teacher workstation. Origin checks alone do not
authenticate another device; loopback alone does not stop hostile websites.

**Alternatives considered**: Tunnel URL secrecy and permissive CORS do not provide
an access boundary. A hosted identity service is outside this local scope.

## 3. Separate trusted roster identity from scan candidates

**Decision**: Stable IDs and class constrain matching; ambiguity requires teacher
resolution. OCR candidates do not rewrite shared roster fields. Use the existing
random unique-ID helper instead of short timestamp IDs. Import Google students by
external identity, with email/name used only for verified disambiguation.

**Rationale**: Exact-name lookup ignores class; ingestion omits class; identity
updates rename shared records; external imports match back to IDs by name alone.

**Alternatives considered**: Picking the first or closest name retains the wrong
owner failure mode.

## 4. Record step outcomes and preserve evidence

**Decision**: Explicit pending/running/complete/incomplete/failed outcomes and
coverage diagnostics replace completion guesses from text presence. Preserve prior
usable evidence on failed retries. Account for expected scheme questions, preserve
valid zero marks, retain grounding on teacher saves, and keep the scheme maximum.

**Rationale**: Partial timeouts and malformed responses can become completed marks;
Chinese essay fallbacks can fabricate passing scores; omitted questions inflate
percentages; replace-all review saves lose grounding and alter the shared maximum.

**Alternatives considered**: More feedback-string heuristics or synthetic default
scores conceal uncertainty rather than resolving it.

## 5. Bind approval, export, and release to one revision

**Decision**: Mutations visibly invalidate approval; unchanged export is read-only.
Preflight release requires complete current approval and exact verified
course/work/student destination before side effects. Persist completed external
steps/file IDs for retry and propagate sharing failures.

**Rationale**: Missing Google IDs currently select the first student; single release
has no approval gate; retries can duplicate uploads; export can demote approval.

**Alternatives considered**: Blind retries or accepting a first recipient violates
the assessment/recipient contract.

## 6. Track local asset references

**Decision**: Use SQLite references for originals, pages, thumbnails, and reports.
Revoke access on deletion, remove unreferenced owned files, retain shared combined
originals for surviving scripts, and report retryable cleanup failures.

**Rationale**: SQL-only deletion leaves images readable; combined scans deliberately
share a source path.

**Alternatives considered**: Unconditional original deletion damages survivors;
retaining everything indefinitely defeats deletion.

## 7. Isolate and label validation

**Decision**: Temporary DB/asset/OAuth paths and denied network by default.
Deterministic mocks cover success/error contracts; browser and rendered PDF checks
cover presentation. Report unavailable real samples and live integrations honestly.

**Rationale**: Existing Google tests overwrite secrets/delete tokens. A mocked pass
does not prove handwriting accuracy or school-tenant permissions.

**Alternatives considered**: A real teacher database, token, or class makes tests
destructive and less reproducible.

All design choices are resolved within the existing architecture. Live educational
accuracy and actual Classroom publication remain separate acceptance exercises
requiring agreed synthetic/test destinations and explicit authorization.
