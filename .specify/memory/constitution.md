# Tallus Constitution

## Core Principles

### I. Local processing and explicit sharing

The core roster, scan, marking, review, and report workflow MUST operate without
external network requests. Student records, scans, extracted text, and grades MUST
stay on the teacher's device unless the teacher explicitly invokes a documented
sharing feature. Google Classroom and tunnel access are opt-in network features;
they MUST NOT be described as offline. Local mode MUST bind to loopback by default.
Any reachable teacher API or script resource MUST enforce an access boundary before
network or tunnel mode can be recommended for real student data.

### II. Teacher control over assessment

AI output is a draft. A failed, malformed, or incomplete model response MUST remain
visibly incomplete and MUST NOT acquire an invented score or silently pass as a
completed assessment. Only a teacher-approved result may be released to students.
Viewing or exporting unchanged work MUST preserve its approval state. Edits or
reruns that invalidate approval MUST expose that transition to the teacher.

### III. Stable ownership and assessment evidence

Submission ownership MUST resolve to an unambiguous student identity. OCR guesses
MUST NOT silently overwrite trusted roster records or unrelated history. Duplicate
names across classes MUST require disambiguation. Saving a review MUST preserve
page, bounding-box, criteria, annotation, and extraction evidence unless explicitly
changed. The assignment marking scheme determines the assessment denominator;
one student's partial extraction MUST NOT rewrite it for the class.

### IV. Bounded and recoverable operations

An explicitly selected assignment or submission set MUST never expand when empty.
Retries MUST preserve earlier usable evidence and report partial failure. Invalid
or stale review state MUST disable mutations. Deletion MUST remove owned student
assets and prevent their continued retrieval; shared source files require explicit
reference handling. Backup and migration steps MUST preserve existing records.

### V. Evidence before readiness claims

Consequential marking, identity, approval, release, and deletion changes MUST have
focused regression coverage and an end-to-end validation scenario using synthetic
data. Tests MUST isolate databases, assets, OAuth credentials, and network traffic.
Review reports MUST distinguish unmodified checks, diagnostic workarounds, mocked
integrations, skipped samples, and live validation. A successful mock run MUST NOT
be represented as proof of real OCR quality or a live Classroom round trip.

## Product and Data Constraints

Tallus remains a local teacher workstation with optional Classroom integration.
The existing subject marker registry and general/science/Chinese essay workflows
remain the baseline. Human review and printable reports are core outcomes.
No new cloud inference, telemetry, identity provider, or shared hosting dependency
is implied by adopting Spec Kit. Text imported from scans, files, rosters, and model
responses MUST be rendered as data in the interface and reports.

## Development Workflow and Quality Gates

Each consequential change MUST identify the affected user journey, acceptance
criteria, evidence, and data implications in `specs/`. Use the installed Spec Kit
constitution, specify, plan, tasks, analyze, and convergence skills as appropriate.
Small fixes may reference the baseline requirement and a focused reproduction.

Before a release, verify clean installation/startup, isolated automated checks,
browser review and export, offline operation, and applicable integration gates.
Known failures MUST be recorded with ownership and scope. No real student records
or OAuth credentials belong in Git, fixtures, screenshots, or review evidence.

## Governance

This is the first reviewable project constitution, adopted alongside Spec Kit on
2026-10-26. It defines acceptance constraints; it does not certify that the existing
application already satisfies them. Known violations are tracked in
`specs/001-marking-workflow/` and the end-to-end review.

Amendments MUST state their reason, affected requirements, validation, and any data
migration. Version with major changes for incompatible principles, minor changes
for new principles, and patch changes for clarification. Reviewers MUST check
relevant principles before merging. Exceptions require a documented bounded reason
and follow-up action; they MUST NOT be hidden by marking incomplete tasks complete.

**Version**: 1.0.0 | **Ratified**: 2026-10-26 | **Last Amended**: 2026-10-26
