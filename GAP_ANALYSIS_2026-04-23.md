# GAP Analysis BDCP - 2026-04-23

## Scope
- Requirements baseline: `docs-bdcp/REQUIREMENTS.md`
- Backend: `api-bdcp/src`
- Frontend: `app-bdcp/src`

## Executive Summary
- Functional coverage is good for authentication, invite-based onboarding, component lifecycle with draft/approval, export, and crawler import.
- Main gaps are authorization governance and requirement mismatches around profile/teacher management.
- Highest-risk issues are related to missing admin guard in sensitive routes.

## Update 2026-05-02 (Implemented Slice)
- Fixed public discipline details flow to align with E03US03 public access behavior:
  - `GET /api/components/:code` is now public (no auth middleware).
  - Component lookup by code now uses exact case-insensitive comparison (no partial `LIKE %code%` match).
  - `ementas-app` detail page no longer requests protected draft listing for non-authenticated users.
- Hardened SIAC crawler import for E03US08:
  - importer now uses received `cdCurso` and `nuPerCursoInicial` values instead of hardcoded course/semester;
  - imports all extracted components from SIAC detail pages;
  - skips duplicate components safely while preserving error propagation for unexpected failures;
  - captures prerequisite information in simplified mode (text/codes only), without automatic linkage workflow.
- Functional modeling update for official lifecycle (E03US04/E03US06):
  - approval logs now persist `versionCode` (`ddMMyyyy + ata`), `officialProgram`, and `officialSyllabus`;
  - official version is explicitly displayed in `ementas-app` details, separated from draft visualization.
- Docker Compose end-to-end SIAC validation (real course/semester):
  - stack: `docker compose up -d --build` in `api-bdcp`;
  - import request payload: `{ "cdCurso": 112140, "nuPerCursoInicial": 20132 }`;
  - observed result in API after authenticated import: `beforeTotal=1`, `afterTotal=37`, sample imported codes `MATA67,MATA66,MATE11,MATA88,...`;
  - sample detail check: `GET /api/components/MATA67` returned `prerequeriments="NAO_SE_APLICA"`, proving simplified prerequisite persistence.
- Validation evidence:
  - `api-bdcp`: `jest ComponentDocumentFlow.spec.ts --runInBand` passed.
  - `ementas-app`: `vitest run src/pages/DisciplineDetailsPage.test.tsx` passed.

## Traceability Matrix

| Requirement | Status | Evidence | Risk | Recommended slice |
| --- | --- | --- | --- | --- |
| E01US01 Generate invite link | Partial | `api-bdcp/src/routers/UserInviteRouter.ts` route `/generate` has no auth/admin guard; `app-bdcp/src/pages/UserListPage/index.tsx` uses this action | Any user (or anonymous) may issue invite links | Add `ensureAuthenticated` + role guard for invite generation; add forbidden tests |
| E01US02 Register from invite | Implemented | `api-bdcp/src/routers/UserRouter.ts` POST `/:inviteToken`; `app-bdcp/src/routes.tsx` `/cadastrar/:inviteToken` | Token misuse risk if validation rules are weak | Add integration tests for expired/invalid/reused token |
| E01US03 Edit teacher info | Partial | API has `PUT /users/update/email` and `PUT /users/update/password`; no admin endpoint to edit another teacher name/email | Requirement asks teacher info editing in management flow | Add admin endpoint `PUT /users/:id` with explicit role checks and audit log |
| E01US04 List teachers | Implemented | `api-bdcp/src/routers/UserRouter.ts` GET `/` guarded by admin middleware | Frontend route is only authenticated, not explicitly admin-gated | Add UI guard by role to hide/deny user management page for non-admin |
| E02US01 Login | Implemented | `api-bdcp/src/routers/AuthRouter.ts` POST `/login`; auth context in `app-bdcp/src/contexts/auth/index.tsx` | Low | Keep as is; add regression tests after dependency upgrades |
| E02US02 Reset password | Implemented | `api-bdcp/src/routers/AuthRouter.ts` POST `/reset-password`; `app-bdcp/src/pages/FogotPasswordPage/index.tsx` | Naming typo in page folder can hurt maintainability | Rename `FogotPasswordPage` -> `ForgotPasswordPage` in a safe refactor slice |
| E03US01 Create discipline/content | Implemented | `api-bdcp/src/routers/ComponentRouter.ts` POST `/`; DTO validation and workload model present | Medium data consistency risk in workload copy/update | Add integration test for create + draft mirror consistency |
| E03US02 Search/list disciplines | Implemented | `api-bdcp/src/controllers/ComponentController.ts` getComponents supports search/pagination; list UI in `app-bdcp/src/pages/ComponentListPage/index.tsx` | Autocomplete behavior may differ from requirement wording | Validate UX acceptance and add tests for search by code/name |
| E03US03 Discipline details (professor/public variants) | Partial | Public list allows viewing published; draft details require auth in `/component-drafts/:code` | Visibility rules for unpublished edits are implicit, not explicit by role | Formalize visibility policy and enforce by role in service layer/tests |
| E03US04 Official change history | Implemented | `GET /components/:id/logs` in `api-bdcp/src/routers/ComponentRouter.ts`; API client in `app-bdcp/src/api/component/index.ts` | Log filtering semantics need contract tests | Add tests for chronological order and type filtering |
| E03US05 Update discipline (draft + publish options) | Implemented | Draft update `PUT /component-drafts/:id` and approval publish `POST /component-drafts/:id/approve` | Medium if update bypasses intended workflow | Add invariant tests to ensure publish only via approval route |
| E03US06 Approve discipline change with date/minutes | Implemented | `ApproveDraftRequestDto` requires `agreementDate` and `agreementNumber`; approve service writes approval log | Unauthorized approver risk | Require admin/specific role for approve action |
| E03US07 Export discipline | Implemented | `GET /components/:id/export` returns PDF in `api-bdcp/src/controllers/ComponentController.ts` | Endpoint is public by design, review policy | Decide if public export is expected; if not, add auth |
| E03US08 Crawler import | Implemented | `POST /components/import` in `api-bdcp/src/routers/ComponentRouter.ts` with auth | High cost/abuse risk if not role-restricted | Restrict import to admin and add rate/timeout safeguards |

## Critical Risks (Priority Order)
1. Invite generation endpoint without authentication/authorization.
2. User deletion endpoint appears to require only authentication, not admin role (`api-bdcp/src/routers/UserRouter.ts` DELETE `/:id`).
3. Requirement target role is SUPER_ADMIN, but current role model is only `admin` and `teacher`.
4. Admin functionality is exposed in frontend route structure without explicit role guard.

## Recommended Sprint Slices

### Slice 1 - Authorization Hardening (highest priority)
- Goals:
  - Protect invite generation, user deletion, draft approval, and crawler import with explicit admin-level authorization.
  - Introduce SUPER_ADMIN in role model or map policy explicitly.
- Acceptance criteria:
  - Unauthorized users receive 401/403 consistently.
  - Existing admin happy-path remains green.
- Tests:
  - API integration tests for allowed/forbidden cases per endpoint.

### Slice 2 - User Management Requirement Completion
- Goals:
  - Add endpoint and UI flow for admin to edit teacher name/email.
  - Restrict `/usuarios` page to authorized role.
- Acceptance criteria:
  - Admin edits another user successfully.
  - Non-admin cannot access user management operations.
- Tests:
  - Backend controller/service tests and frontend route guard tests.

### Slice 3 - Discipline Lifecycle Invariants
- Goals:
  - Guarantee publication path only through formal approval.
  - Strengthen draft/published consistency checks.
- Acceptance criteria:
  - Published data always linked to approval log with date/number.
  - Draft updates never bypass approval invariants.
- Tests:
  - End-to-end API scenarios for create -> draft edit -> approve -> history.

## Validation Plan
- Backend:
  - `npm run lint:check`
  - `npm run typecheck`
  - `npm test`
- Frontend:
  - `npm run lint`
  - `npm run check-types`
  - `npm test`

## Monograph Notes
- Problem statement candidate:
  - "The current BDCP implementation has broad functional coverage, but authorization boundaries in critical governance operations are under-specified."
- Technical decision candidate:
  - "Prioritize authorization hardening and requirement traceability before broad dependency modernization to reduce regression and security risk."
- Expected impact:
  - "Improved robustness, explicit governance, and stronger alignment between documented requirements and implemented behavior."
