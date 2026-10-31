# GPS Guardian Escolar — Functional requirements and traceability

**Status:** active working specification  
**Version:** 3.0  
**Repository:** `Docs-Guardian-Escolar`  
**Purpose:** provide a versioned source of truth for requirements, user stories, acceptance
criteria and reproducible evidence.

## Product problem

Guardians need to register students, link a student device, monitor school trips, receive
safe-zone notifications and manage account security. The system must protect minors' data,
expose stable contracts to web/mobile clients and remain reproducible in development, QA and
production.

## Users and actors

| Actor | Need | Capabilities |
| --- | --- | --- |
| Guardian | Manage children and monitor trips | Registration, students, routes, zones and notifications |
| Student device | Share the active trip location | Device linking, child token and tracking |
| Administrator | Operate and audit the platform | Dashboard, audit events and error management |
| Reviewer | Verify delivery quality | Tests, CI, PR review and branch protection |

## Product scope

- Authentication: registration, email verification, login, device confirmation, 2FA, recovery,
  sessions and logout.
- Student linking: QR/manual code, device ownership, one active device and child profile.
- Tracking: trip lifecycle, coordinates, WebSocket updates, route deviation and history.
- Safety: safe zones, assigned routes and notifications.
- Administration: dashboard, audit logs and operational errors.
- Delivery: reproducible Docker stack, health checks, CI and reviewed promotion through protected
  branches.

## Active delivery stories

### H-71 — Reconcile the production line

- **User:** team and reviewer.
- **Problem:** delivery branches diverge and do not provide one verifiable promotion line.
- **Acceptance:** approved development work is promoted through a reviewed PR; official branches
  and promotion order are documented; protected branches require approval and green CI.
- **Evidence:** branch comparison, merged PR and branch-protection settings.

### H-72 — Manual student-device linking

- **User:** student or guardian linking without scanning the QR.
- **Problem:** the manual flow may not have `studentId`.
- **Acceptance:** a valid unexpired code resolves the active student; QR linking remains valid;
  invalid, expired, used and over-attempted codes are rejected.
- **Evidence:** backend service tests and frontend payload contract test.

### H-73 — Release artifact and secret policy

- **User:** release manager.
- **Acceptance:** CI rejects tracked APKs and Google Maps keys; APKs are GitHub Release assets;
  keys are supplied through deployment variables.
- **Evidence:** frontend `security:release` check and release asset URL.

### H-74 — Least-privilege database runtime

- **User:** operator and security reviewer.
- **Acceptance:** backend uses `gps_guardian_app`; only Liquibase uses bootstrap credentials;
  `current_user` is not `postgres`.
- **Evidence:** Compose configuration and database session check.

### H-75 — Hash transient security tokens

- **User:** guardian completing verification, recovery, 2FA or device confirmation.
- **Acceptance:** raw transient values are not persisted; lookup hashes the presented value;
  expiration and single-use behavior remain enforced.
- **Evidence:** token-hash unit test and authentication/session test suite.

### H-76 — Version functional requirements

- **User:** new contributor, reviewer or evaluator.
- **Acceptance:** this document is committed in the documentation repository; every active story
  identifies its user, problem, scope, acceptance criteria and evidence; no private Drive link
  is required to understand or verify the work.
- **Evidence:** this file, linked README entry, issue URL, PR URL and merge commit.

### H-79 — Security tests and coverage in CI

- **User:** reviewer.
- **Acceptance:** backend security tests and frontend Jest tests run on PRs; coverage is
  published; a failing security test blocks merging.
- **Evidence:** workflow run and uploaded coverage artifact.

### H-80 — Notification deletion and configuration contract

- **User:** guardian managing notifications.
- **Acceptance:** deletion persists after reload; frontend/backend contracts agree; SMS is
  implemented end to end or explicitly removed; valid public email domains are accepted.
- **Evidence:** API contract test, reload regression test and environment matrix.

### H-81 — Versioned OpenAPI and pagination

- **User:** frontend client and API integrator.
- **Acceptance:** versioned `openapi.json` is accessible; list endpoints accept bounded
  `page`/`size` values with metadata; legacy client aliases are removed.
- **Evidence:** OpenAPI artifact, controller tests and paginated response.

## Definition of done

A story is complete only when the implementation is merged through a reviewed PR, automated
checks pass, acceptance evidence is reproducible and the issue is moved to `Done`. Direct pushes
to protected delivery branches do not count as completion.

## Evidence record

Each implementation issue or PR must record the issue URL, PR URL, merge commit, target branch,
test command and deployment/API evidence. Private documents may supplement the repository but
must not be the only evidence.
