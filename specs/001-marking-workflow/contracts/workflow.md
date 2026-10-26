# Workflow Contracts

Target behavior for existing routes/browser actions; implementation outstanding.
Keep route shapes where possible without silently changing ownership/score meaning.

## Local access and literal text — US1/US3

Teacher sessions protect `/api/*`, reports, and student assets. Unrelated
origins/hosts and missing/invalid sessions receive 401/403 without data or mutation.
Local launcher/bootstrap works offline; network/tunnel startup fails closed without
protected access. Bundle core UI dependencies. Logs/health contain no identities or
credentials.

Use DOM text/value assignment and escape HTML report text before newline formatting.
Quotes, `<`, `>`, and `</textarea>` remain literal through save/reload. No imported,
model-generated, or URL parameter text becomes executable interface code.

## Ingestion and bounded batches — US2

- `/api/submissions/upload`, `/batch-upload`, `/split-combined-scan`: validate
  assignment/supplied student IDs before committing; constrain matching by class
  and stable identity; expose unresolved candidates without changing trusted names.
- `/api/submissions/batch-pipeline`: distinguish omitted global scope, explicit
  assignment scope, and explicit submission scope. An explicit empty list is zero
  work. Validate IDs/membership before starting. Empty eligibility returns
  `total: 0, results: []` without global fallback.
- `/api/assignments/{id}/rerun-pipeline`: supplied IDs must belong to the selected
  assignment; approved inclusion must preserve the original scope.

## Marking and review evidence — US2/US3

All markers retain expected question coverage and explicit step outcomes.
Failures/malformed responses expose diagnostics, never passing defaults or complete
flags. Valid zero remains zero. Per-script writes never alter the scheme maximum.

`POST /api/grading/{id}/save-draft` and `/approve` preserve full question evidence.
Merge omitted page/bbox/criteria/annotations from storage rather than replacing
them with defaults. Reject stale revision/context with 409, missing IDs with 404,
invalid scores/state with 400/422, without partial save. Approval requires complete
evidence and records the current revision.

The review UI clears its active ID, fields, assets, and mutation controls when
assignment selection is empty, loading, failed, or stale. Save/approve confirms the
active context still matches the displayed script.

## Reports and release — US3/US4

Report GET/download is read-only. Annotation generation that changes assessment
evidence is an explicit mutation with visible approval transition. English/Chinese
fonts render legible text and a consistent denominator.

Single/bulk Google release use the same preflight: complete current approval,
linked course/work, exact student mapping/verified destination, readable report.
Pending/incomplete/ambiguous work causes no upload/share/grade/return. Fetch all
relevant pages, never select the first student as fallback, and import external
rosters by Google ID.

Persist external operation IDs/outcomes for the approved revision. Permission,
grade, attachment, or return failures remain visible with completed steps; do not
claim successful release or hide missing student access. Retry reuses completed
uploads and records release only after required operations succeed. Mocks do not
prove permissions in a real school tenant.

## Deletion — US5

Student/assignment/submission delete revokes access, removes owned originals/pages/
thumbnails/reports, and retains a shared original only for surviving references.
Old URLs return 404/410 without deleted content. Cleanup failures remain visible
and retryable while access stays denied. Delete only paths inside managed data
directories, never arbitrary client-supplied paths.

## Validation boundary — all stories

Redirect DB, upload, processed, report, OAuth secret/token paths, and services to
temporary substitutes before app creation. Deny outbound network unless a test
explicitly installs a local mock. Credential/disconnect tests never touch teacher
files. Report clean startup, diagnostic probes, browser/PDF checks, and live
integration/accuracy validation separately.
