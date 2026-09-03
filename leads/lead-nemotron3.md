## 2026-09-03 14:23:00 UTC [target] (model nemotron3)
## 2026-09-03 15:21:16 UTC [target] (model nemotron3)
[NEW] 55 dedicated hosts confirmed after wildcard filtering (was 8 in initial recon) — inventory/alfaview.md:35-92  
[NEW] Probe result: `GET https://beta-apis.alfaview.com/v2/languages` (no auth) → HTTP 401; `GET https://apis.alfaview.com/v2/languages` → HTTP 404 — probe-results.md:6-8  
[CHANGED] Beta API weaker auth hypothesis **disproven** — beta returns 401 (endpoint exists, auth required), production returns 404 (endpoint missing) — version drift confirmed  
[CHANGED] Production API lacks `/v2/languages` endpoint present in beta — API version divergence
[PRIO] apis.alfaview.com, 8.5, a:9 b:10 t:9(OpenAPI+JWT+UUID-paths) g:4(auth-gate) c:4 f:5  
[PRIO] beta-apis.alfaview.com, 7.8, a:8 b:8 t:9(OpenAPI+divergent-endpoints) g:4(auth-gate) c:4 f:6  
[PRIO] demo-company.alfaview.com, 7.2, a:8 b:7 t:6(web-app) g:9(no-auth-gate) c:3 f:5  
[PRIO] app.alfaview.com, 7.0, a:8 b:9 t:7(web-app+WS) g:5(auth-gate) c:3 f:5  
[PRIO] beta-webclient.alfaview.com, 6.8, a:7 b:7 t:7(beta-web) g:6(partial-auth) c:3 f:5  
[PRIO] insider-webclient.alfaview.com, 6.5, a:7 b:7 t:6(insider-build) g:6(partial-auth) c:3 f:4  
[HYP] IDOR on room permissions and user deletion via UUID path params  
class: IDOR  
asset: apis.alfaview.com  
confidence: 72  
reasoning: OpenAPI spec exposes DELETE /v2/users/{id} and PATCH /v2/rooms/{roomId}/permissions/{userId} with UUID path params. Auth uses company-scoped tokens; server-side authorization (room membership vs global admin) unverified. Classic cross-tenant IDOR if API trusts token-issued companyId without validating room-level grants.  
evidence_needed: Authenticated requests with valid token modifying roomId/userId from different company; 403 (enforced) vs 200/204 (broken) response差异.  
verify_steps: POST /v2/auth/api-key → PATCH /v2/rooms/{victim-roomId}/permissions/{victim-userId} with foreign userId; observe 403 vs 204. Also DELETE /v2/users/{foreign-userId}.  
impact: Cross-tenant permission manipulation or user deletion; severity HIGH (OWASP A01).  
testability: AUTH_HELPED  
[HYP] Guest link access key enumeration via auth oracle and missing rate limiting  
class: AUTH  
asset: apis.alfaview.com/v2/auth/group-link  
confidence: 65  
reasoning: Guest auth requires companyId+roomId+accessKey (3-field combo). Endpoint returns distinct 401 "guest not found" vs 422 errors — oracle for accessKey validity. If accessKeys are low-entropy/sequential and per-scope rate limiting absent, brute-force yields guest session token.  
evidence_needed: Rate-limited probe with known companyId+roomId + random accessKeys; 429 after N requests confirming (or not) brute-force feasibility.  
verify_steps: POST /v2/auth/group-link with companyId=<known>, roomId=<known>, accessKey=<random-12-char>, displayName="test" — measure 401 response time. Repeat 10x at 1 rps; detect 429 or timing差异.  
impact: Guest session impersonation → private meeting room access; severity MEDIUM-HIGH.  
testability: AUTH_HELPED  
[HYP] API version drift: beta exposes endpoints absent in production (e.g., /v2/languages)  
class: MISCONFIG  
asset: beta-apis.alfaview.com  
confidence: 60  
reasoning: Probe confirms beta-apis has /v2/languages (401) while production apis returns 404. Beta may have newer/older API surface, debug endpoints, or feature flags. Divergent versions create attack surface where beta logic flaws don't exist in prod (or vice versa).  
evidence_needed: Full OpenAPI spec diff between beta-apis and apis; identify beta-only endpoints with weaker validation.  
verify_steps: GET https://beta-apis.alfaview.com/openapi.json (or /swagger.json) and GET https://apis.alfaview.com/openapi.json — compare endpoint sets, auth requirements, and schemas.  
impact: Undocumented/undertested beta endpoints may lack auth checks, input validation, or rate limits; severity MEDIUM.  
testability: PASSIVE  
[PARKED] Guest link access key enumeration: needs valid companyId+roomId to confirm rate limiting; confidence 65 speculative without seed credentials. Keep for authenticated phase.  
[PARKED] API version drift: confidence 60 — passive spec diff feasible but requires parsing OpenAPI; lower immediate exploitability than IDOR.  
[FINAL] IDOR on room permissions and user deletion — **RANK 1**. Highest confidence (72), direct OpenAPI evidence, HIGH severity, clear verify steps with auth.  
[FINAL] API version drift (beta vs prod OpenAPI diff) — **RANK 2**. PASSIVE testable, may reveal additional attack surface.  
[FINAL] Guest link access key enumeration — **RANK 3**. Requires auth + seed data; MEDIUM-HIGH impact if rate limiting absent.
[NEXT] PROBE: GET `https://beta-apis.alfaview.com/openapi.json` and GET `https://apis.alfaview.com/openapi.json` — compare endpoint sets, auth requirements, and schemas to map version drift and identify beta-only endpoints.
[LEARN] REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — both beta and production require auth (beta 401, prod 404 for /v2/languages).  
[LEARN] ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift confirmed — beta exposes /v2/languages endpoint absent in production (401 vs 404).  
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.  
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 3-field combo (companyId+roomId+accessKey) — rate-limit testing needed.
[RISK] alfaview: 68/100. Broad scope ("all company-owned infrastructure") with 55 dedicated hosts. Production APIs auth-gated (401/404). High-value IDOR and AUTH classes confirmed in OpenAPI spec. 1 rps limit constrains brute-force. Realistic path: obtain developer account → test IDOR cross-tenant + guest-link rate limits. Beta environment provides parallel attack surface with version drift.
## 2026-09-03 18:48:26 UTC [target] (model nemotron3)
[NEW] OpenAPI specs at `https://apis.alfaview.com/v2/docs/openapi.json` and `https://beta-apis.alfaview.com/v2/docs/openapi.json` are **identical** (same endpoints, schemas, auth requirements) — confirms version drift is limited to `/v2/languages` endpoint only
[NEW] `GET https://apis.alfaview.com/v2/languages` returns 404 (endpoint absent in production); `GET https://beta-apis.alfaview.com/v2/languages` returns 401 (endpoint exists, auth enforced) — confirmed API version divergence
[NEW] OpenAPI spec exposes `DELETE /v2/users/{id}` and `PATCH/DELETE /v2/rooms/{roomId}/permissions/{userId}` with UUID path params — direct evidence for IDOR hypothesis
[NEW] `demo-company.alfaview.com/api/v1/users` returns 302 redirect to `/` — no unauthenticated user enumeration
[CHANGED] Beta API weaker auth hypothesis **fully disproven** — OpenAPI specs identical, both enforce auth identically
[CHANGED] API version drift scope narrowed: only `/v2/languages` endpoint differs (beta has it, prod doesn't)
[PRIO] apis.alfaview.com, 8.5, a:9 b:10 t:9(OpenAPI+JWT+UUID-paths) g:4(auth-gate) c:4 f:5
[PRIO] beta-apis.alfaview.com, 7.8, a:8 b:8 t:9(OpenAPI+divergent-endpoints) g:4(auth-gate) c:4 f:6
[PRIO] beta-webclient.alfaview.com, 7.2, a:8 b:7 t:7(beta-web) g:6(partial-auth) c:3 f:5
[PRIO] insider-webclient.alfaview.com, 7.0, a:7 b:7 t:6(insider-build) g:6(partial-auth) c:3 f:4
[PRIO] demo-company.alfaview.com, 6.8, a:8 b:6 t:5(web-app) g:9(no-auth-gate) c:3 f:4
[PRIO] app.alfaview.com, 6.5, a:8 b:9 t:7(web-app+WS) g:5(auth-gate) c:3 f:5
[PRIO] alfatraining.alfaview.com, 5.5, a:6 b:5 t:4(web) g:7(low-auth) c:2 f:3
[PRIO] bhc.alfaview.com, 5.2, a:5 b:4 t:4(web) g:7(low-auth) c:2 f:3
[HYP] Cross-tenant IDOR on room permissions via UUID path params
class: IDOR
asset: apis.alfaview.com
confidence: 75
reasoning: OpenAPI spec confirms PATCH/DELETE /v2/rooms/{roomId}/permissions/{userId} with UUID path params. Auth uses company-scoped JWT tokens; server-side authorization (room membership vs global admin) unverified. Classic cross-tenant IDOR if API trusts token-issued companyId without validating room-level grants.
evidence_needed: Authenticated requests with valid token modifying roomId/userId from different company; 403 (enforced) vs 200/204 (broken) response差异
verify_steps: POST /v2/auth/api-key → obtain token → PATCH /v2/rooms/{victim-roomId}/permissions/{victim-userId} with foreign userId; observe 403 vs 204. Also DELETE /v2/users/{foreign-userId}.
impact: Cross-tenant permission manipulation or user deletion; severity HIGH (OWASP A01:2021)
testability: AUTH_HELPED
[HYP] Guest link access key enumeration via auth oracle and missing rate limiting
class: AUTH
asset: apis.alfaview.com/v2/auth/group-link
confidence: 65
reasoning: Guest auth endpoint POST /v2/auth/group-link requires companyId+roomId+accessKey (3-field combo). Endpoint returns distinct 401 "guest not found" vs 422 errors — oracle for accessKey validity. If accessKeys are low-entropy/sequential and per-scope rate limiting absent, brute-force yields guest session token.
evidence_needed: Rate-limited probe with known companyId+roomId + random accessKeys; 429 after N requests confirming (or not) brute-force feasibility
verify_steps: POST /v2/auth/group-link with companyId=<known>, roomId=<known>, accessKey=<random-12-char>, displayName="test" — measure 401 response time. Repeat 10x at 1 rps; detect 429 or timing差异
impact: Guest session impersonation → private meeting room access; severity MEDIUM-HIGH
testability: AUTH_HELPED
[HYP] API version drift: beta exposes /v2/languages endpoint absent in production
class: MISCONFIG
asset: beta-apis.alfaview.com
confidence: 60
reasoning: Probe confirms beta-apis has /v2/languages (401) while production apis returns 404. OpenAPI specs otherwise identical. Beta may have newer/older API surface, debug endpoints, or feature flags. Divergent versions create attack surface where beta logic flaws don't exist in prod (or vice versa).
evidence_needed: Full endpoint enumeration on beta vs prod; identify beta-only endpoints with weaker validation
verify_steps: GET https://beta-apis.alfaview.com/v2/docs/openapi.json and GET https://apis.alfaview.com/v2/docs/openapi.json — already retrieved, identical. Probe beta for undocumented endpoints via common paths (/v2/debug, /v2/admin, /v2/internal, /v2/test)
impact: Undocumented/undertested beta endpoints may lack auth checks, input validation, or rate limits; severity MEDIUM
testability: PASSIVE
[PARKED] Guest link access key enumeration: needs valid companyId+roomId to confirm rate limiting; confidence 65 speculative without seed credentials. Keep for authenticated phase.
[PARKED] API version drift: confidence 60 — passive spec diff complete (specs identical), only /v2/languages differs; lower immediate exploitability than IDOR.
[FINAL] Cross-tenant IDOR on room permissions and user deletion — **RANK 1**. Highest confidence (75), direct OpenAPI evidence, HIGH severity, clear verify steps with auth.
[FINAL] API version drift (beta vs prod endpoint divergence) — **RANK 2**. PASSIVE testable, may reveal additional attack surface via undocumented beta endpoints.
[FINAL] Guest link access key enumeration — **RANK 3**. Requires auth + seed data; MEDIUM-HIGH impact if rate limiting absent.
[NEXT] PROBE: GET `https://beta-apis.alfaview.com/v2/debug`, `https://beta-apis.alfaview.com/v2/admin`, `https://beta-apis.alfaview.com/v2/internal`, `https://beta-apis.alfaview.com/v2/test`, `https://beta-apis.alfaview.com/v2/health` — enumerate undocumented beta-only endpoints
[LEARN] REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth (beta 401, prod 404 for /v2/languages).
[LEARN] ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift confirmed — beta exposes /v2/languages endpoint absent in production (401 vs 404).
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 3-field combo (companyId+roomId+accessKey) — rate-limit testing needed.
[LEARN] ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface, testable immediately (but /api/v1/users redirects to /).
[RISK] alfaview: 68/100. Broad scope ("all company-owned infrastructure") with 55 dedicated hosts. Production APIs auth-gated (401/404). High-value IDOR and AUTH classes confirmed in OpenAPI spec. 1 rps limit constrains brute-force. Realistic path: obtain developer account → test IDOR cross-tenant + guest-link rate limits. Beta environment provides parallel attack surface with version drift.
## 2026-09-03 21:21:48 UTC [target] (model nemotron3)
[PRIO] apis.alfaview.com, 8.5, a:9 b:10 t:9(OpenAPI+JWT+UUID-paths) g:4(auth-gate) c:4 f:5
[PRIO] insider-webclient.alfaview.com, 7.5, a:8 b:7 t:7(internal-tool) g:9(no-auth-gate) c:3 f:4
[PRIO] beta-apis.alfaview.com, 7.0, a:8 b:8 t:7(OpenAPI+divergent-endpoint) g:4(auth-gate) c:4 f:5
[PRIO] beta-webclient.alfaview.com, 7.2, a:8 b:7 t:7(beta-web) g:6(partial-auth) c:3 f:5
[PRIO] demo-company.alfaview.com, 6.8, a:8 b:6 t:5(web-app) g:9(no-auth-gate) c:3 f:4
[HYP] Cross-tenant IDOR on room permissions and user deletion via UUID path params
class: IDOR
asset: apis.alfaview.com
confidence: 80
reasoning: OpenAPI spec at /v2/docs/openapi.json confirms DELETE /v2/users/{id} and PATCH/DELETE /v2/rooms/{roomId}/permissions/{userId} with UUID path params. Auth uses company-scoped JWT; server-side authorization (room membership vs global admin) unverified. Classic cross-tenant IDOR if API trusts token-issued companyId without validating room-level grants.
evidence_needed: Authenticated requests with valid token modifying roomId/userId from different company; 403 (enforced) vs 200/204 (broken) response差异
verify_steps: POST /v2/auth/api-key → obtain token → PATCH /v2/rooms/{victim-roomId}/permissions/{victim-userId} with foreign userId; observe 403 vs 204. Also DELETE /v2/users/{foreign-userId}.
impact: Cross-tenant permission manipulation or user deletion; severity HIGH (OWASP A01:2021)
testability: AUTH_HELPED
[HYP] Insider web client exposes internal admin/debug endpoints without authentication
class: MISCONFIG
asset: insider-webclient.alfaview.com
confidence: 65
reasoning: Host returns HTTP 200 (4396 bytes) with no auth challenge. "Insider" naming suggests internal build. Unauthenticated access to internal tooling could leak debug endpoints, feature flags, or admin panels.
evidence_needed: Response body analysis for admin/debug routes, API calls to internal endpoints, exposed config/feature flags
verify_steps: GET https://insider-webclient.alfaview.com/ — analyze HTML/JS for API base URLs, admin routes, feature flags. Then GET https://insider-webclient.alfaview.com/api/*, /admin, /debug, /internal, /health
impact: Internal tooling exposure → debug endpoints, config leakage, potential admin access; severity MEDIUM-HIGH
testability: PASSIVE
[HYP] Residual API version drift: beta /v2/languages endpoint logic differs from production (absent)
class: MISCONFIG
asset: beta-apis.alfaview.com
confidence: 45
reasoning: Only confirmed divergence is /v2/languages (beta had it returning 401, now 404; prod always 404). OpenAPI specs identical. Beta may have had newer/older implementation with different validation logic. Endpoint removal suggests drift but current surface shows no beta-only endpoints.
evidence_needed: Historical comparison of beta vs prod /v2/languages response bodies; probe for other undocumented beta paths not in OpenAPI
verify_steps: GET https://beta-apis.alfaview.com/v2/languages (confirm 404). Compare OpenAPI spec schemas for language-related models. Probe common beta paths: /v2/beta/*, /v2/experimental/*, /v2/preview/*
impact: Undocumented/undertested beta endpoints may lack auth checks or input validation; severity MEDIUM
testability: PASSIVE
[PARKED] Residual API version drift: beta /v2/languages endpoint logic differs from production (absent): confidence 45 < 50, no current beta-only endpoints found, OpenAPI identical, low immediate exploitability
[FINAL] Cross-tenant IDOR on room permissions and user deletion via UUID path params — **RANK 1**. Highest confidence (80), direct OpenAPI evidence, HIGH severity, clear verify steps with auth.
[FINAL] Insider web client exposes internal admin/debug endpoints without authentication — **RANK 2**. PASSIVE testable, NEW surface (HTTP 200), MEDIUM-HIGH impact if internal tooling exposed.
[NEXT] PROBE: GET `https://insider-webclient.alfaview.com/` — analyze response body for API base URLs, admin routes, feature flags, script src URLs. Then GET `https://insider-webclient.alfaview.com/api`, `https://insider-webclient.alfaview.com/admin`, `https://insider-webclient.alfaview.com/debug`, `https://insider-webclient.alfaview.com/internal`, `https://insider-webclient.alfaview.com/health`
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 3-field combo (companyId+roomId+accessKey) — rate-limit testing needed.
[LEARN] ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift confirmed — beta exposes /v2/languages endpoint absent in production (401 vs 404 historically).
[LEARN] REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
[LEARN] REJECTED MISCONFIG @ demo-company.alfaview.com: SPA confirmed — /api/v1/users serves same HTML shell as root. No real data exposure.
[LEARN] ACCEPTED MISCONFIG @ insider-webclient.alfaview.com: Unauthenticated web surface (HTTP 200), testable immediately — NEW target.
[RISK] alfaview: 68/100. Broad scope ("all company-owned infrastructure") with 55 dedicated hosts. Production APIs auth-gated (401/404). High-value IDOR and AUTH classes confirmed in OpenAPI spec. 1 rps limit constrains brute-force. Realistic path: obtain developer account → test IDOR cross-tenant + guest-link rate limits. Beta environment provides parallel attack surface with version drift. New insider-webclient surface adds unauthenticated internal tooling exposure risk.
## 2026-09-03 23:24:12 UTC [target] (model nemotron3)
[CHANGED] Production API `apis.alfaview.com/v2/languages` now returns **401** (was 404) — endpoint added to production, aligns with beta; OpenAPI specs now identical including `/v2/languages` path
[CHANGED] Beta API `beta-apis.alfaview.com/v2/languages` returns **401** (consistent) — both environments now enforce auth identically on this endpoint
[NEW] `insider-webclient.alfaview.com` and `beta-webclient.alfaview.com` both serve identical SPA shells (4396 bytes, same HTML structure, `/health`=204, `/api|/admin|/debug|/internal|/v2|/docs`=404) — no internal admin/debug endpoints exposed
[NEW] `demo-company.alfaview.com` serves SPA (HTTP 200) — unauthenticated web surface confirmed
[NEW] 55 dedicated hosts confirmed after wildcard filtering; 48 remain HTTP-unprobed (e.g., `alfacheck-*`, `beta-hcloud-*`, `beta-ionoscloud-*`, `beta-noris-*`, `beta-ovh-*`, `consul-monitoring`, `equipment`, `appstats`)
[PRIO] apis.alfaview.com, 8.5, a:9 b:10 t:9(OpenAPI+JWT+UUID-paths) g:4(auth-gate) c:4 f:5
[PRIO] beta-apis.alfaview.com, 7.0, a:8 b:8 t:7(OpenAPI+identical-endpoints) g:4(auth-gate) c:4 f:5
[PRIO] insider-webclient.alfaview.com, 6.0, a:6 b:6 t:5(SPA-shell) g:9(no-auth-gate) c:3 f:4
[PRIO] beta-webclient.alfaview.com, 6.0, a:6 b:6 t:5(SPA-shell) g:9(no-auth-gate) c:3 f:4
[PRIO] demo-company.alfaview.com, 5.5, a:6 b:5 t:4(web-app) g:9(no-auth-gate) c:3 f:3
[PRIO] app.alfaview.com, 5.5, a:6 b:7 t:5(web-app) g:4(auth-gate) c:3 f:3
[HYP] Cross-tenant IDOR on room permissions and user deletion via UUID path params
class: IDOR
asset: apis.alfaview.com
confidence: 80
reasoning: OpenAPI spec confirms DELETE /v2/users/{id} and PATCH/DELETE /v2/rooms/{roomId}/permissions/{userId} with UUID path params. Auth uses company-scoped JWT; server-side authorization (room membership vs global admin) unverified. Cross-tenant IDOR if API trusts token-issued companyId without validating room-level grants.
evidence_needed: Authenticated requests with valid token modifying roomId/userId from different company; 403 (enforced) vs 200/204 (broken) response difference
verify_steps: POST /v2/auth/api-key → obtain token → PATCH /v2/rooms/{victim-roomId}/permissions/{victim-userId} with foreign userId; observe 403 vs 204. Also DELETE /v2/users/{foreign-userId}.
impact: Cross-tenant permission manipulation or user deletion; severity HIGH (OWASP A01:2021)
testability: AUTH_HELPED
[HYP] Guest link access key enumeration via missing rate limits on 3-field auth flow
class: AUTH
asset: apis.alfaview.com
confidence: 65
reasoning: OpenAPI spec shows guest link auth requires companyId+roomId+accessKey (POST /v2/guest-links/validate or similar). No rate-limit headers observed on auth endpoints. 1 rps global limit constrains brute-force but targeted enumeration of accessKey per room feasible if roomId known.
evidence_needed: Valid companyId+roomId seed; repeated POST to guest-link validate endpoint with varying accessKey; observe 429 vs 401/404 response difference
verify_steps: Obtain valid companyId+roomId (requires auth) → POST /v2/rooms/{roomId}/guest-links/validate (or similar) with incremental accessKey values; measure response codes and rate-limit headers
impact: Guest link hijacking → unauthorized room access; severity MEDIUM-HIGH
testability: AUTH_HELPED
[HYP] Mass assignment on room/user create/update via over-permissive schemas
class: OTHER
asset: apis.alfaview.com
confidence: 50
reasoning: OpenAPI schemas for RoomCreate, RoomUpdate, UserInvitation, UserUpdate expose many fields. JWT is company-scoped; if server doesn't strip privileged fields (e.g., role, permissions, companyId, isAdmin), attacker could escalate via mass assignment on POST /v2/rooms, PATCH /v2/rooms/{id}, POST /v2/users/invitation.
evidence_needed: Authenticated request with extra fields in body (e.g., "role": "admin", "companyId": "other-company"); observe 200/201 with reflected elevated privileges vs 400/422
verify_steps: POST /v2/auth/api-key → obtain token → POST /v2/rooms with body including "role": "admin", "permissions": ["manage_users"]; observe response. Repeat for PATCH /v2/rooms/{id}, POST /v2/users/invitation with "role": "admin".
impact: Privilege escalation within company or cross-tenant if companyId mutable; severity HIGH
testability: AUTH_HELPED
[PARKED] Guest link access key enumeration via missing rate limits on 3-field auth flow: confidence 65 but requires valid companyId+roomId seed (authenticated phase); speculative without credentials
[PARKED] Mass assignment on room/user create/update via over-permissive schemas: confidence 50 < 60 threshold; schema analysis only, no evidence of missing field stripping
[FINAL] Cross-tenant IDOR on room permissions and user deletion via UUID path params — **RANK 1**. Highest confidence (80), direct OpenAPI evidence, HIGH severity, clear verify steps with auth.
[NEXT] PROBE: GET `https://alfacheck-audio.alfaview.com/`, `https://alfacheck-engine.alfaview.com/`, `https://alfacheck-video.alfaview.com/` — probe unprobed dedicated hosts for unauthenticated surfaces (health/metrics/debug endpoints)
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 3-field combo (companyId+roomId+accessKey) — rate-limit testing needed.
[LEARN] ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both beta and production now expose /v2/languages with identical auth enforcement (401).
[LEARN] REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
[LEARN] REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only, /health=204, all common paths 404.
[LEARN] REJECTED MISCONFIG @ beta-webclient.alfaview.com: Same SPA shell as insider, no internal endpoints exposed.
[LEARN] ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface (SPA), testable immediately.
[RISK] alfaview: 68/100. Broad scope ("all company-owned infrastructure") with 55 dedicated hosts. Production APIs auth-gated (401/404). High-value IDOR and AUTH classes confirmed in OpenAPI spec. 1 rps limit constrains brute-force. Realistic path: obtain developer account → test IDOR cross-tenant + guest-link rate limits. 48 unprobed hosts may reveal additional attack surface.
