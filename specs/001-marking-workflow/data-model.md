# Data Model and Invariants

Target evolution of SQLite, implemented through backed-up additive migration.
This document is not a migration or a claim that these fields already exist.

| Entity | Existing basis and target fields | Relationships |
|---|---|---|
| Student | Existing local ID/code, name, class, email, subject, optional Google ID; separate unresolved scan candidates | Owns many submissions |
| Assignment | Existing title, class, subject, scheme/rubric, marker, maximum; scheme revision | Has many submissions |
| Submission | Existing owner/source/pages/scores/feedback; assessment revision; step outcomes/diagnostics; approved revision/time | Belongs to student/assignment; owns assessments and asset references |
| Question Assessment | Existing question ID/title, max/awarded marks, answer, criteria, feedback, page, bbox, annotations; extraction/evaluation outcome/error | Belongs to submission and expected scheme question |
| Asset Reference | Local file identity/path/kind, submission references, deletion/cleanup outcome | Shared originals have multiple references; derived assets belong to submission/revision |
| Release Attempt | Submission/revision, exact course/work/student/submission IDs, Drive file ID, upload/share/grade/return outcomes, error | References an approved revision and retains completed steps |

Teacher sessions are ephemeral process state. OAuth credentials do not belong in
assessment tables, reports, fixtures, or Git.

## Validation rules

- **D1**: "A scan candidate must not overwrite a trusted student name or class."
- **D2**: "An explicit batch scope must never expand when its eligible set is empty."
- **D3**: "The assignment maximum comes from its marking scheme, not one script's extracted questions."
- **D4**: "A failed or malformed model result cannot be complete; an explicit valid zero remains zero."
- **D5**: "Review saves preserve page, bbox, criteria, annotations, and extracted text unless explicitly changed."
- **D6**: "Only the current complete approved revision can be released to an exact verified recipient."
- **D7**: "Unchanged export cannot change assessment revision, approval, or scores."
- **D8**: "Deleted assets cannot be retrieved; shared originals survive while a live submission references them."

Apply existing SQLite relationships consistently, checking foreign-key integrity
during migration. Preserve IDs and uncertain legacy rows; do not silently delete
orphans or reassign historical owners. Surface records requiring resolution.

## State transitions

Each step starts pending, becomes running, and ends complete, incomplete, or failed.
Keep diagnostics separate from assessment text. Readiness uses step outcomes and
scheme coverage, including explicitly blank answers, rather than nonempty text.

Submission flow: pending → marking → review_ready → approved. Incomplete/failed
work remains flagged and cannot be approved/released as complete. Teacher edits or
successful reruns increment the revision and return approval to review_ready;
failed attempts preserve usable evidence and record errors. Export has no state
transition. Approval records the current revision.

Release preflight rejects without side effects or creates a durable attempt for
the approved revision. Advance upload → share → grade → return, retaining IDs and
outcomes. Failure exposes the last completed step; retry only unfinished work.

Deletion makes records/assets inaccessible, removes dependent grades/references,
and removes unreferenced files. Failed filesystem removal remains retryable and
inaccessible; distinguish access revocation from completed physical cleanup.
