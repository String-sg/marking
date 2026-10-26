# Feature Specification: Dependable local marking workflow

**Feature Branch**: `codex/e2e-review-speckit`

**Created**: 2026-10-26

**Status**: Draft baseline and remediation scope; implementation outstanding

**Input**: User description: "review this code e2e, introduce speckit too"

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Open a private local workstation (Priority: P1)

A teacher installs Tallus, opens it without internet access, and manages local
assessment records without exposing them to other devices or websites.

**Why this priority**: Installation, offline access, and student privacy underpin
every assessment journey.

**Independent Test**: Start from a clean installation with external requests blocked;
open the workstation and navigate all five sections using synthetic records.

**Acceptance Scenarios**:

1. **Given** a clean installation, **When** the teacher starts Tallus, **Then** it
   opens successfully with all required runtime dependencies declared.
2. **Given** no internet access, **When** the teacher opens each section, **Then**
   navigation, forms, icons, visibility controls, and local charts remain usable.
3. **Given** local mode, **When** an unrelated device or website attempts to access
   records or mutate assessments, **Then** access is denied.

### User Story 2 - Ingest and mark the correct student scripts (Priority: P1)

A teacher imports a roster, creates an assignment and marking scheme, then uploads
individual scans or splits a combined class scan into correctly owned scripts.
General, science, and Chinese composition markers produce reviewable drafts.

**Why this priority**: Wrong identity, missing questions, and invented scores can
misrepresent student performance before the teacher begins review.

**Independent Test**: Use two classes with duplicate names and a synthetic multipage
paper; mark with deterministic successful, failed, and malformed model responses.

**Acceptance Scenarios**:

1. **Given** duplicate student names in different classes, **When** a script is
   ingested, **Then** its class and stable identity constrain matching and ambiguity
   requires teacher resolution without changing trusted roster details.
2. **Given** a complete assignment scheme, **When** a question is blank or omitted
   during extraction, **Then** it remains accounted for or visibly incomplete;
   other students' maximum marks remain unchanged.
3. **Given** a failed or malformed AI response, **When** marking completes,
   **Then** the affected work is visibly incomplete and receives no fabricated mark.
4. **Given** an explicitly selected assignment with no eligible scripts, **When**
   the teacher starts a batch, **Then** no other assignment is processed.

### User Story 3 - Review and approve without losing evidence (Priority: P1)

A teacher compares the original script with extracted answers and marking
criteria, corrects scores and feedback, and approves the reviewed result.

**Why this priority**: The review station is the final human control over AI output.

**Independent Test**: Review a three-page script, save and reload it, switch to an
empty assignment, and export an approved result without editing it.

**Acceptance Scenarios**:

1. **Given** a question grounded on page three, **When** its draft is saved or
   approved, **Then** page, location, criteria, and annotation evidence persist.
2. **Given** a previously open script, **When** the teacher selects an empty
   assignment, **Then** no previous script remains actionable and approval is disabled.
3. **Given** quoted text or HTML-like handwriting and feedback, **When** review
   fields render and are saved, **Then** their text is preserved exactly and cannot
   execute as interface code.
4. **Given** an approved script, **When** an unchanged report is downloaded,
   **Then** the approval and inclusion in approved analytics remain intact.

### User Story 4 - Publish the approved result to its intended student (Priority: P1)

A teacher produces a legible report and optionally releases the approved result
through a connected Google Classroom account to the intended student.

**Why this priority**: Publication changes what students receive and must preserve
both assessment accuracy and recipient identity.

**Independent Test**: Generate English and Chinese reports; use a mocked class with
multiple students to verify exact recipient selection and the approval gate.

**Acceptance Scenarios**:

1. **Given** pending, flagged, or incomplete work, **When** release is requested,
   **Then** no student-facing upload, grade update, or return occurs.
2. **Given** a student without a linked Classroom identity, **When** release is
   requested, **Then** an exact verified association is required; the first class
   submission is never selected as a default.
3. **Given** Chinese names, assignment titles, and feedback, **When** a report is
   generated, **Then** the text is legible and the score denominator is consistent.
4. **Given** a failed external release operation, **When** the teacher retries,
   **Then** the failure is reported and earlier local approval/evidence remain intact.

### User Story 5 - Delete assessment data predictably (Priority: P2)

A teacher removes an assignment, student, or submission and can trust that its
owned scans, derived images, and reports no longer remain accessible.

**Why this priority**: Record cleanup must match what the interface and README promise.

**Independent Test**: Delete a synthetic script and a combined-scan assignment;
check both records and prior asset links while preserving surviving shared sources.

**Acceptance Scenarios**:

1. **Given** a deleted submission, **When** an old script or report link is opened,
   **Then** it no longer returns the deleted student's content.
2. **Given** multiple scripts from one combined scan, **When** one script is
   deleted, **Then** remaining scripts retain their valid source evidence.

### Edge Cases

- Zero eligible scripts; nonexistent assignment; explicitly empty submission sets.
- Same-name students across classes, unknown identifiers, and ambiguous OCR names.
- Blank answers, missing question extraction, partial rubrics, and explicit zero marks.
- Model unavailable, invalid JSON, structurally invalid results, and partial failure.
- Navigation during review, empty assignment selection, and repeated save/export.
- Quotation marks, angle brackets, Chinese text, and long multipage feedback.
- Missing or paginated Classroom recipients, expired credentials, and release retries.
- Shared scan files, missing assets, and cleanup failure during deletion.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The workstation MUST start from documented dependencies and support
  its core workflow with external network requests blocked.
- **FR-002**: Local mode MUST restrict record and script access to the intended
  teacher; network/tunnel access MUST require an explicit protected access boundary.
- **FR-003**: Roster import and script matching MUST preserve stable student identity,
  constrain matching by class, and surface ambiguous ownership.
- **FR-004**: Marking MUST preserve all scheme questions or identify incomplete
  coverage and MUST NOT silently change the assignment's authoritative maximum.
- **FR-005**: Failed or malformed AI output MUST remain incomplete; explicit zero
  scores MUST remain zero unless changed by the teacher.
- **FR-006**: Explicit assignment/submission selections MUST bound batch processing
  even when the selection has no eligible work.
- **FR-007**: Save and approval MUST preserve the question's page, location,
  annotations, criteria, and extracted text unless explicitly edited.
- **FR-008**: An empty or stale review context MUST prevent changes to a previously
  active script.
- **FR-009**: Imported and generated text MUST display literally in review fields
  and HTML reports, including quotation marks and markup-like content.
- **FR-010**: Viewing and exporting unchanged approved work MUST preserve approval.
- **FR-011**: Release MUST require approval and an exact verified student/assignment
  association before any student-facing side effect.
- **FR-012**: English and Chinese reports MUST retain legible names, feedback, and
  consistent marks and maximums.
- **FR-013**: Deletion MUST remove owned assessment files and revoke previous asset
  access while preserving sources still referenced by surviving scripts.
- **FR-014**: Automated validation MUST use isolated records, files, credentials,
  and network substitutes and MUST report live integration limits explicitly.

### Key Entities *(include if feature involves data)*

- **Student**: Stable local identity, class membership, optional external association.
- **Assignment**: Subject, class, scheme, authoritative maximum, selected marker.
- **Submission**: Owned student script, source pages, marking state, approval, release.
- **Question Assessment**: Extracted answer, criterion scores, feedback, page/location evidence.
- **Report**: Rendered approved or draft assessment with a consistent score and language.
- **Release Record**: Intended external recipient/work, approval basis, outcome and retry state.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A fresh workstation starts and all five sections remain usable with
  zero external requests during the core workflow.
- **SC-002**: Every synthetic identity and empty-selection scenario changes only
  its intended students and assessments, with zero cross-class reassignment.
- **SC-003**: Every injected model failure is visibly incomplete; no test case
  receives an invented passing mark or silently omits an assessed question.
- **SC-004**: Save, approval, reload, and unchanged export preserve all tested
  evidence fields and approval state exactly.
- **SC-005**: All release scenarios send only approved work to the exact intended
  student; ambiguous or missing associations produce zero external side effects.
- **SC-006**: English and Chinese report samples retain readable text; every
  deleted owned asset becomes inaccessible while surviving shared work stays readable.

## Assumptions

- The actor is a teacher operating a local workstation; this pass does not create
  a hosted service or mandate String ID integration.
- This document defines acceptance behaviour for the existing product. It does
  not claim the reviewed code satisfies it; the review and tasks record known gaps.
- This pass introduces Spec Kit and records remediation tasks. Application fixes,
  production deployment, and real student-data processing are separate work.
- Live handwriting accuracy requires an agreed labelled sample set and local model;
  mocked marking does not validate educational scoring quality.
- Live Classroom verification requires a teacher-approved test account/course;
  this review uses synthetic recipients and no real grade publication.
- Focused regression and browser checks are required for the consequential fixes
  described here; cosmetic-only edits do not need mirrored implementation tests.
