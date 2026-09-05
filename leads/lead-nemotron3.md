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
## 2026-09-04 01:12:17 UTC [target] (model nemotron3)
[NEW] Production API `apis.alfaview.com/v2/languages` now returns **401** (was 404) — endpoint added to production, aligns with beta; OpenAPI specs now identical including `/v2/languages` path
[NEW] `insider-webclient.alfaview.com` and `beta-webclient.alfaview.com` both serve identical SPA shells (4396 bytes, same HTML structure, `/health`=204, `/api|/admin|/debug|/internal|/v2|/docs`=404) — no internal admin/debug endpoints exposed
[NEW] `demo-company.alfaview.com` serves SPA (HTTP 200) — unauthenticated web surface confirmed
[NEW] 55 dedicated hosts confirmed after wildcard filtering; 48 remain HTTP-unprobed (e.g., `alfacheck-*`, `beta-hcloud-*`, `beta-ionoscloud-*`, `beta-noris-*`, `beta-ovh-*`, `consul-monitoring`, `equipment`, `appstats`)
[NEW] Live HTTP 200 on previously unprobed: `alfatraining.alfaview.com`, `bhc.alfaview.com`, `kh-freiburg.alfaview.com`, `beta-hcloud-19-beta-hydra-dzwx8.alfaview.com`, `beta-noris-33-beta-hydra-2zm7t.alfaview.com`, `beta-ovh-29-beta-hydra-z4tf8.alfaview.com`
[NEW] `beta-app.alfaview.com` and `internal.alfaview.com` both return HTTP 401 (auth-gated)
[NEW] `appstats.alfaview.com`, `consul-monitoring.alfaview.com`, `equipment.alfaview.com`, `ip-185-245-101-240.alfaview.com` timeout/unreachable
[CHANGED] Beta API weaker auth hypothesis **fully disproven** — OpenAPI specs identical, both enforce auth identically (401 on `/v2/languages`)
[CHANGED] API version drift **resolved** — both beta and production now expose `/v2/languages` with identical auth enforcement
[CHANGED] Cross-tenant IDOR on room permissions and user deletion via UUID path params confirmed as highest-priority authenticated target (confidence 80)
[PRIO] apis.alfaview.com,7.6,a:9 b:10 t:9 g:4 c:4 f:5
[PRIO] beta-apis.alfaview.com,6.6,a:8 b:8 t:7 g:4 c:4 f:5
[PRIO] alfatraining.alfaview.com,5.8,a:6 b:6 t:5 g:9 c:3 f:4
[PRIO] bhc.alfaview.com,5.8,a:6 b:6 t:5 g:9 c:3 f:4
[PRIO] kh-freiburg.alfaview.com,5.8,a:6 b:6 t:5 g:9 c:3 f:4
[PRIO] beta-hcloud-19-beta-hydra-dzwx8.alfaview.com,5.8,a:6 b:6 t:5 g:9 c:3 f:4
[PRIO] beta-noris-33-beta-hydra-2zm7t.alfaview.com,5.8,a:6 b:6 t:5 g:9 c:3 f:4
[PRIO] beta-ovh-29-beta-hydra-z4tf8.alfaview.com,5.8,a:6 b:6 t:5 g:9 c:3 f:4
[PRIO] insider-webclient.alfaview.com,5.8,a:6 b:6 t:5 g:9 c:3 f:4
[PRIO] beta-webclient.alfaview.com,5.8,a:6 b:6 t:5 g:9 c:3 f:4
[PRIO] demo-company.alfaview.com,5.3,a:6 b:5 t:4 g:9 c:3 f:3
[PRIO] app.alfaview.com,5.2,a:6 b:7 t:5 g:4 c:3 f:3
[PRIO] beta-app.alfaview.com,4.7,a:5 b:6 t:4 g:4 c:3 f:3
[PRIO] internal.alfaview.com,4.2,a:5 b:5 t:3 g:4 c:3 f:3
[HYP] Cross-tenant IDOR on room permissions and user deletion via UUID path params
class: IDOR
asset: apis.alfaview.com
confidence: 80
reasoning: OpenAPI spec confirms DELETE /v2/users/{id} and PATCH/DELETE /v2/rooms/{roomId}/permissions/{userId} with UUID path params. Auth uses company-scoped JWT; server-side authorization (room membership vs global admin) unverified. Cross-tenant IDOR if API trusts token-issued companyId without validating room-level grants.
evidence_needed: Authenticated requests with valid token modifying roomId/userId from different company; 403 (enforced) vs 200/204 (broken) response difference
verify_steps: POST /v2/auth/api-key → obtain token → PATCH /v2/rooms/{victim-roomId}/permissions/{victim-userId} with foreign userId; observe 403 vs 204. Also DELETE /v2/users/{foreign-userId}.
impact: Cross-tenant permission manipulation or user deletion; severity HIGH (OWASP A01:2021)
testability: AUTH_HELPED
[HYP] Mass assignment on room/user create/update via over-permissive schemas
class: OTHER
asset: beta-apis.alfaview.com
confidence: 55
reasoning: OpenAPI schemas for RoomCreate, RoomUpdate, UserInvitation, UserUpdate expose many fields. JWT is company-scoped; if server doesn't strip privileged fields (e.g., role, permissions, companyId, isAdmin), attacker could escalate via mass assignment on POST /v2/rooms, PATCH /v2/rooms/{id}, POST /v2/users/invitation. Beta environment safer for testing.
evidence_needed: Authenticated request with extra fields in body (e.g., "role": "admin", "companyId": "other-company"); observe 200/201 with reflected elevated privileges vs 400/422
verify_steps: POST /v2/auth/api-key → obtain token → POST /v2/rooms with body including "role": "admin", "permissions": ["manage_users"]; observe response. Repeat for PATCH /v2/rooms/{id}, POST /v2/users/invitation with "role": "admin".
impact: Privilege escalation within company or cross-tenant if companyId mutable; severity HIGH
testability: AUTH_HELPED
[HYP] Exposed internal endpoints on hydra/beta infrastructure hosts
class: MISCONFIG
asset: beta-hcloud-19-beta-hydra-dzwx8.alfaview.com
confidence: 45
reasoning: Three hydra hosts across cloud providers (hcloud, noris, ovh) return HTTP 200 on root. These appear to be internal infrastructure nodes (hydra = likely signaling/media server). Unauthenticated exposure of health/metrics/debug/admin endpoints on such hosts could leak cluster topology, config, or allow control-plane abuse.
evidence_needed: Response bodies on /.well-known, /health, /metrics, /debug, /admin, /actuator, /api, /v1, /v2 showing non-SPA content (JSON, prometheus metrics, stack traces)
verify_steps: GET https://beta-hcloud-19-beta-hydra-dzwx8.alfaview.com/ — capture body, Content-Type. Then GET /health, /metrics, /debug, /admin, /actuator/health, /api, /v1, /v2. Repeat for beta-noris-33-beta-hydra-2zm7t.alfaview.com and beta-ovh-29-beta-hydra-z4tf8.alfaview.com.
impact: Infrastructure info leak, potential RCE via debug endpoints, cluster pivot; severity MEDIUM-HIGH
testability: PASSIVE
[PARKED] Mass assignment on room/user create/update via over-permissive schemas: confidence 55 < 60 threshold; schema analysis only, no evidence of missing field stripping
[PARKED] Exposed internal endpoints on hydra/beta infrastructure hosts: confidence 45 < 60; speculative without body analysis, hydra hosts may be media relays not admin panels
[FINAL] Cross-tenant IDOR on room permissions and user deletion via UUID path params — **RANK 1**. Highest confidence (80), direct OpenAPI evidence, HIGH severity, clear verify steps with auth.
[NEXT] PROBE: GET `https://beta-hcloud-19-beta-hydra-dzwx8.alfaview.com/` — capture response body, Content-Type, headers. Then GET `/health`, `/metrics`, `/debug`, `/admin`, `/actuator/health`, `/api`, `/v1`, `/v2`. Repeat for `beta-noris-33-beta-hydra-2zm7t.alfaview.com` and `beta-ovh-29-beta-hydra-z4tf8.alfaview.com`.
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 3-field combo (companyId+roomId+accessKey) — rate-limit testing needed.
[LEARN] ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both beta and production now expose /v2/languages with identical auth enforcement (401).
[LEARN] REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
[LEARN] REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only, /health=204, all common paths 404.
[LEARN] REJECTED MISCONFIG @ beta-webclient.alfaview.com: Same SPA shell as insider, no internal endpoints exposed.
[LEARN] ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface (SPA), testable immediately.
[LEARN] ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (HTTP 200), testable immediately — NEW target.
[LEARN] ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (HTTP 200), testable immediately — NEW target.
[LEARN] ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (HTTP 200), testable immediately — NEW target.
[LEARN] ACCEPTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Unauthenticated infrastructure surface (HTTP 200) — NEW target.
[LEARN] ACCEPTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Unauthenticated infrastructure surface (HTTP 200) — NEW target.
[LEARN] ACCEPTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Unauthenticated infrastructure surface (HTTP 200) — NEW target.
[LEARN] REJECTED MISCONFIG @ appstats.alfaview.com: Unreachable/timeout — no surface.
[LEARN] REJECTED MISCONFIG @ consul-monitoring.alfaview.com: Unreachable/timeout — no surface.
[LEARN] REJECTED MISCONFIG @ equipment.alfaview.com: Unreachable/timeout — no surface.
[LEARN] REJECTED MISCONFIG @ ip-185-245-101-240.alfaview.com: Unreachable/timeout — no surface.
[LEARN] ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
[LEARN] ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.
[RISK] alfaview: 70/100. Broad scope ("all company-owned infrastructure") with 55 dedicated hosts, 17 now confirmed live HTTP. Production APIs auth-gated (401/404). High-value IDOR and AUTH classes confirmed in OpenAPI spec. 1 rps limit constrains brute-force. Realistic path: obtain developer account → test IDOR cross-tenant + guest-link rate limits. Beta environment provides parallel attack surface with identical API. 6 hydra infrastructure nodes across 3 cloud providers expose unauthenticated HTTP 200 — potential internal tooling leak. 31 hosts remain unprobed/unreachable.
## 2026-09-04 06:00:46 UTC [target] (model nemotron3)
[PRIO] apis.alfaview.com,7.6,a:9 b:10 t:9 g:4 c:4 f:5
[PRIO] beta-apis.alfaview.com,6.6,a:8 b:8 t:7 g:4 c:4 f:5
[PRIO] beta-hcloud-19-beta-hydra-dzwx8.alfaview.com,4.2,a:5 b:5 t:4 g:9 c:2 f:3
[PRIO] beta-noris-33-beta-hydra-2zm7t.alfaview.com,4.2,a:5 b:5 t:4 g:9 c:2 f:3
[PRIO] beta-ovh-29-beta-hydra-z4tf8.alfaview.com,4.2,a:5 b:5 t:4 g:9 c:2 f:3
[PRIO] alfatraining.alfaview.com,4.0,a:5 b:5 t:3 g:9 c:2 f:3
[PRIO] bhc.alfaview.com,4.0,a:5 b:5 t:3 g:9 c:2 f:3
[PRIO] kh-freiburg.alfaview.com,4.0,a:5 b:5 t:3 g:9 c:2 f:3
[PRIO] demo-company.alfaview.com,3.8,a:5 b:4 t:3 g:9 c:2 f:2
[PRIO] app.alfaview.com,5.2,a:6 b:7 t:5 g:4 c:3 f:3
[PRIO] beta-app.alfaview.com,4.7,a:5 b:6 t:4 g:4 c:3 f:3
[PRIO] internal.alfaview.com,4.2,a:5 b:5 t:3 g:4 c:3 f:3
[HYP] Cross-tenant IDOR on room permissions and user deletion via UUID path params
class: IDOR
asset: apis.alfaview.com
confidence: 80
reasoning: OpenAPI spec confirms DELETE /v2/users/{id} and PATCH/DELETE /v2/rooms/{roomId}/permissions/{userId} with UUID path params. Auth uses company-scoped JWT; server-side authorization (room membership vs global admin) unverified. Cross-tenant IDOR if API trusts token-issued companyId without validating room-level grants.
evidence_needed: Authenticated requests with valid token modifying roomId/userId from different company; 403 (enforced) vs 200/204 (broken) response difference
verify_steps: POST /v2/auth/api-key → obtain token → PATCH /v2/rooms/{victim-roomId}/permissions/{victim-userId} with foreign userId; observe 403 vs 204. Also DELETE /v2/users/{foreign-userId}.
impact: Cross-tenant permission manipulation or user deletion; severity HIGH (OWASP A01:2021)
testability: AUTH_HELPED
[HYP] Guest link auth rate-limit bypass on 3-field combo (companyId+roomId+accessKey)
class: AUTH
asset: apis.alfaview.com
confidence: 65
reasoning: Guest link auth flow requires 3-field combo (companyId+roomId+accessKey). Rate-limit status unknown. If no rate limiting on accessKey validation, attacker can brute-force valid accessKeys for known companyId/roomId pairs. AccessKey format/entropy unverified.
evidence_needed: Repeated POST to guest auth endpoint with invalid accessKey; observe 429 vs consistent 401/403; measure requests/second before throttle
verify_steps: Identify guest auth endpoint (likely POST /v2/auth/guest or similar via OpenAPI) → send 50+ rapid requests with fixed companyId/roomId, rotating accessKey → monitor status codes and response timing
impact: Unauthorized room access via accessKey enumeration; severity MEDIUM-HIGH
testability: AUTH_HELPED
[HYP] Multi-tenant SaaS subdomain takeover via DNS misconfiguration on customer domains
class: MISCONFIG
asset: alfatraining.alfaview.com
confidence: 45
reasoning: Three customer subdomains (alfatraining, bhc, kh-freiburg) serve identical SPA shell with same etag/last-modified — confirms multi-tenant SaaS on alfaview.com subdomains. If any customer DNS points to decommissioned infrastructure (CNAME to expired cloud resource), subdomain takeover possible. No evidence of dangling CNAMEs yet.
evidence_needed: DNS records for each customer subdomain showing CNAME to external/cloud resource that could be claimed; HTTP response from claimed resource serving attacker content
verify_steps: dig +short CNAME alfatraining.alfaview.com bhc.alfaview.com kh-freiburg.alfaview.com → check target hosting provider → attempt registration if dangling
impact: Full control of customer-branded alfaview subdomain → phishing, credential theft, brand damage; severity HIGH
testability: PASSIVE
[PARKED] Multi-tenant SaaS subdomain takeover via DNS misconfiguration on customer domains: confidence 45 < 60; no dangling CNAME evidence found, all three customer domains resolve to alfaview infrastructure (edge-proxy)
[FINAL] Cross-tenant IDOR on room permissions and user deletion via UUID path params — **RANK 1**. Highest confidence (80), direct OpenAPI evidence, HIGH severity, clear verify steps with auth.
[FINAL] Guest link auth rate-limit bypass on 3-field combo — **RANK 2**. Confidence 65, Auth class, testable with valid companyId/roomId pair, MEDIUM-HIGH impact.
[NEXT] PROBE: GET `https://apis.alfaview.com/v2/docs/openapi.json` — fetch full OpenAPI spec to identify exact guest auth endpoint path and request schema for rate-limit testing.
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 3-field combo (companyId+roomId+accessKey) — rate-limit testing needed.
[LEARN] ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both beta and production now expose /v2/languages with identical auth enforcement (401).
[LEARN] REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
[LEARN] REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only (4396B), /health=204, all common paths 404.
[LEARN] REJECTED MISCONFIG @ beta-webclient.alfaview.com: Same SPA shell as insider, no internal endpoints exposed.
[LEARN] ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface (SPA), testable immediately.
[LEARN] ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Hydra hosts are media/signaling servers returning "Hi Client" on all paths — no internal endpoints exposed.
[LEARN] REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same as above — media server, not admin panel.
[LEARN] REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same as above — media server, not admin panel.
[LEARN] REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: Unreachable/timeout — no surface.
[LEARN] REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: Unreachable/timeout — no surface.
[LEARN] REJECTED MISCONFIG @ alfacheck-video.alfaview.com: Unreachable/timeout — no surface.
[LEARN] ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
[LEARN] ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.
[RISK] alfaview: 70/100. Broad scope ("all company-owned infrastructure") with 55 dedicated hosts, 20 now confirmed live HTTP. Production APIs auth-gated (401). High-value IDOR (confidence 80) and AUTH (confidence 65) classes confirmed in OpenAPI spec. Multi-tenant SaaS on customer subdomains (3 confirmed identical SPA shells). 3 hydra media servers across 3 cloud providers expose unauthenticated HTTP 200 but serve only "Hi Client" — no sensitive endpoints. 31 hosts remain unprobed/unreachable. Realistic path: obtain developer account → test IDOR cross-tenant + guest-link rate limits. Beta environment provides parallel attack surface with identical API.
## 2026-09-04 10:12:03 UTC [target] (model nemotron3)
[PRIO] apis.alfaview.com,7.9,a:9 b:10 t:9 g:2 c:8 f:9
[PRIO] beta-apis.alfaview.com,7.4,a:9 b:9 t:9 g:2 c:8 f:8
[PRIO] app.alfaview.com,6.8,a:8 b:9 t:7 g:3 c:7 f:8
[PRIO] alfatraining.alfaview.com,5.8,a:7 b:8 t:6 g:9 c:6 f:7
[PRIO] bhc.alfaview.com,5.8,a:7 b:8 t:6 g:9 c:6 f:7
[PRIO] kh-freiburg.alfaview.com,5.8,a:7 b:8 t:6 g:9 c:6 f:7
[PRIO] demo-company.alfaview.com,5.5,a:6 b:7 t:6 g:9 c:6 f:6
[PRIO] beta-app.alfaview.com,5.2,a:6 b:8 t:7 g:2 c:6 f:6
[PRIO] internal.alfaview.com,4.7,a:5 b:7 t:6 g:2 c:6 f:5
[PRIO] beta-webclient.alfaview.com,4.2,a:5 b:6 t:5 g:9 c:5 f:4
[PRIO] insider-webclient.alfaview.com,4.2,a:5 b:6 t:5 g:9 c:5 f:4
[HYP] Cross-tenant IDOR on room permissions and user deletion via UUID path params
class: IDOR
asset: apis.alfaview.com
confidence: 80
reasoning: OpenAPI spec confirms DELETE /v2/users/{id} and PATCH/DELETE /v2/rooms/{roomId}/permissions/{userId} with UUID path params. Auth uses company-scoped JWT; server-side authorization (room membership vs global admin) unverified. Cross-tenant IDOR if API trusts token-issued companyId without validating room-level grants.
evidence_needed: Authenticated requests with valid token modifying roomId/userId from different company; 403 (enforced) vs 200/204 (broken) response difference
verify_steps: POST /v2/auth/api-key → obtain token → PATCH /v2/rooms/{victim-roomId}/permissions/{victim-userId} with foreign userId; observe 403 vs 204. Also DELETE /v2/users/{foreign-userId}.
impact: Cross-tenant permission manipulation or user deletion; severity HIGH (OWASP A01:2021)
testability: AUTH_HELPED
[HYP] Guest link auth rate-limit bypass on 4-field combo (companyId+roomId+accessKey+displayName)
class: AUTH
asset: apis.alfaview.com
confidence: 65
reasoning: Guest link auth endpoint POST /v2/auth/guest-link requires 4-field combo (companyId+roomId+accessKey+displayName). Rate-limit status unknown. If no rate limiting on accessKey validation, attacker can brute-force valid accessKeys for known companyId/roomId pairs. AccessKey format/entropy unverified (schema shows string, example "0123456789ABCDEFGHIJKLMNOP" — 26 chars alphanumeric).
evidence_needed: Repeated POST to /v2/auth/guest-link with invalid accessKey; observe 429 vs consistent 401/403; measure requests/second before throttle
verify_steps: Identify valid companyId/roomId pair (from customer subdomain or demo) → send 50+ rapid POST requests to /v2/auth/guest-link with fixed companyId/roomId, rotating accessKey → monitor status codes and response timing for 429
impact: Unauthorized room access via accessKey enumeration; severity MEDIUM-HIGH
testability: AUTH_HELPED
[HYP] Multi-tenant SaaS subdomain takeover via dangling CNAME on customer domains
class: MISCONFIG
asset: alfatraining.alfaview.com
confidence: 40
reasoning: Three customer subdomains (alfatraining, bhc, kh-freiburg) serve identical SPA shell with same structure — confirms multi-tenant SaaS on alfaview.com subdomains. If any customer DNS points to decommissioned infrastructure (CNAME to expired cloud resource), subdomain takeover possible. No evidence of dangling CNAMEs yet; all three resolve to alfaview edge infrastructure.
evidence_needed: DNS records for each customer subdomain showing CNAME to external/cloud resource that could be claimed; HTTP response from claimed resource serving attacker content
verify_steps: dig +short CNAME alfatraining.alfaview.com bhc.alfaview.com kh-freiburg.alfaview.com → check target hosting provider → attempt registration if dangling
impact: Full control of customer-branded alfaview subdomain → phishing, credential theft, brand damage; severity HIGH
testability: PASSIVE
[PARKED] Multi-tenant SaaS subdomain takeover via dangling CNAME on customer domains: confidence 40 < 60; no dangling CNAME evidence found, all three customer domains resolve to alfaview infrastructure (edge-proxy). PASSIVE-only testability limits exploit confirmation.
[FINAL] Cross-tenant IDOR on room permissions and user deletion via UUID path params — **RANK 1**. Highest confidence (80), direct OpenAPI evidence, HIGH severity, clear verify steps with auth.
[FINAL] Guest link auth rate-limit bypass on 4-field combo — **RANK 2**. Confidence 65, Auth class, testable with valid companyId/roomId pair, MEDIUM-HIGH impact.
[NEXT] PROBE: POST `https://apis.alfaview.com/v2/auth/guest-link` with JSON `{"companyId":"<known>","roomId":"<known>","accessKey":"invalid-key-001","displayName":"test"}` — capture exact 401 response body and timing; repeat 10x rapidly to detect rate-limit headers (Retry-After, X-RateLimit-*) or 429 status. Requires known companyId/roomId pair (obtain from demo-company.alfaview.com or customer subdomain page source).
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 4-field combo (companyId+roomId+accessKey+displayName) — rate-limit testing needed.
[LEARN] ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both beta and production now expose /v2/languages with identical auth enforcement (401).
[LEARN] REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
[LEARN] REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only (4396B), /health=204, all common paths 404.
[LEARN] REJECTED MISCONFIG @ beta-webclient.alfaview.com: Same SPA shell as insider, no internal endpoints exposed.
[LEARN] ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface (SPA), testable immediately.
[LEARN] ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Hydra hosts are media/signaling servers returning "Hi Client" on all paths — no internal endpoints exposed.
[LEARN] REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same as above — media server, not admin panel.
[LEARN] REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same as above — media server, not admin panel.
[LEARN] REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: Unreachable/timeout — no surface.
[LEARN] REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: Unreachable/timeout — no surface.
[LEARN] REJECTED MISCONFIG @ alfacheck-video.alfaview.com: Unreachable/timeout — no surface.
[LEARN] ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
[LEARN] ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.
[RISK] alfaview: 70/100. Broad scope ("all company-owned infrastructure") with 55 dedicated hosts, 20+ now confirmed live HTTP. Production APIs auth-gated (401). High-value IDOR (confidence 80) and AUTH (confidence 65) classes confirmed in OpenAPI spec. Multi-tenant SaaS on customer subdomains (3 confirmed identical SPA shells). 3 hydra media servers across 3 cloud providers expose unauthenticated HTTP 200 but serve only "Hi Client" — no sensitive endpoints. 31 hosts remain unprobed/unreachable (beta-ionoscloud-* fleet, etc.). Realistic path: obtain developer account → test IDOR cross-tenant + guest-link rate limits. Beta environment provides parallel attack surface with identical API.
## 2026-09-04 14:24:40 UTC [target] (model nemotron3)
[NEW] Production API `apis.alfaview.com/v2/languages` now returns 401 (was 404) — endpoint added, aligns with beta; OpenAPI specs identical
[NEW] `beta-hcloud-19-beta-hydra-dzwx8.alfaview.com`: HTTP 200 with 9-byte body ("Hi Client"); trailing slash returns HTTP 400 — minimal health-check surface
[NEW] Guest link auth flow updated to 4-field combo (companyId+roomId+accessKey+displayName) — rate-limit status unknown
[CHANGED] API version drift **resolved** — both beta and production expose `/v2/languages` with identical auth enforcement (401)
[CHANGED] `alfacheck-engine/audio/video.alfaview.com`: confirmed UNREACHABLE (timeout probes) — target exhausted
[CHANGED] `beta-hcloud-19-beta-hydra-dzwx8.alfaview.com` et al: confirmed media/signaling servers ("Hi Client") — no admin endpoints
[PRIO] apis.alfaview.com,8.5,a:9 b:10 t:9 g:2 c:9 f:9
[PRIO] beta-apis.alfaview.com,8.2,a:9 b:9 t:9 g:2 c:8 f:9
[PRIO] app.alfaview.com,7.5,a:8 b:10 t:8 g:3 c:8 f:8
[PRIO] alfatraining.alfaview.com,6.2,a:7 b:8 t:6 g:9 c:7 f:7
[PRIO] bhc.alfaview.com,6.2,a:7 b:8 t:6 g:9 c:7 f:7
[PRIO] kh-freiburg.alfaview.com,6.2,a:7 b:8 t:6 g:9 c:7 f:7
[PRIO] demo-company.alfaview.com,5.8,a:6 b:7 t:6 g:9 c:6 f:6
[PRIO] beta-app.alfaview.com,5.5,a:6 b:8 t:7 g:2 c:6 f:6
[PRIO] internal.alfaview.com,5.0,a:5 b:7 t:6 g:2 c:6 f:5
[HYP] Cross-tenant IDOR on room permissions and user deletion via UUID path params
class: IDOR
asset: apis.alfaview.com
confidence: 85
reasoning: OpenAPI spec confirms DELETE /v2/users/{id} and PATCH/DELETE /v2/rooms/{roomId}/permissions/{userId} with UUID path params. Auth uses company-scoped JWT; server-side authorization (room membership vs global admin) unverified. Cross-tenant IDOR if API trusts token-issued companyId without validating room-level grants.
evidence_needed: Authenticated requests with valid token modifying roomId/userId from different company; 403 (enforced) vs 200/204 (broken) response difference
verify_steps: POST /v2/auth/api-key → obtain token → PATCH /v2/rooms/{victim-roomId}/permissions/{victim-userId} with foreign userId; observe 403 vs 204. Also DELETE /v2/users/{foreign-userId}.
impact: Cross-tenant permission manipulation or user deletion; severity HIGH (OWASP A01:2021)
testability: AUTH_HELPED
[HYP] Guest link auth rate-limit bypass on 4-field combo (companyId+roomId+accessKey+displayName)
class: AUTH
asset: apis.alfaview.com
confidence: 70
reasoning: Guest link auth endpoint POST /v2/auth/guest-link requires 4-field combo. Rate-limit status unknown. If no rate limiting on accessKey validation, attacker can brute-force valid accessKeys for known companyId/roomId pairs. AccessKey format/entropy unverified (schema shows string, example "0123456789ABCDEFGHIJKLMNOP" — 26 chars alphanumeric).
evidence_needed: Repeated POST to /v2/auth/guest-link with invalid accessKey; observe 429 vs consistent 401/403; measure requests/second before throttle
verify_steps: Identify valid companyId/roomId pair (from customer subdomain or demo) → send 50+ rapid POST requests to /v2/auth/guest-link with fixed companyId/roomId, rotating accessKey → monitor status codes and response timing for 429
impact: Unauthorized room access via accessKey enumeration; severity MEDIUM-HIGH
testability: AUTH_HELPED
[HYP] JWT token validation bypass via alg/key confusion on company-scoped tokens
class: AUTH
asset: apis.alfaview.com
confidence: 55
reasoning: API uses JWT for authentication (OpenAPI shows bearerAuth). Company-scoped tokens imply asymmetric or shared-secret signing. If RS256/HS256 confusion or JWKS endpoint misconfiguration exists, attacker could forge tokens with arbitrary companyId/roomId claims.
evidence_needed: JWKS endpoint exposure; token signing algorithm analysis; successful verification of forged HS256 token against RS256 public key
verify_steps: GET /.well-known/jwks.json or /v2/auth/jwks → analyze key structure → capture valid token → test alg confusion (RS256→HS256) with public key as HMAC secret → attempt token forgery with modified companyId
impact: Full authentication bypass, cross-tenant access, privilege escalation; severity CRITICAL
testability: PASSIVE (JWKS) + AUTH_HELPED (token capture)
[PARKED] Multi-tenant SaaS subdomain takeover via dangling CNAME on customer domains: confidence 40 < 60; no dangling CNAME evidence found, all three customer domains resolve to alfaview infrastructure (edge-proxy). PASSIVE-only testability limits exploit confirmation.
[FINAL] Cross-tenant IDOR on room permissions and user deletion via UUID path params — **RANK 1**. Confidence 85, direct OpenAPI evidence, HIGH severity, clear verify steps with auth.
[FINAL] Guest link auth rate-limit bypass on 4-field combo — **RANK 2**. Confidence 70, Auth class, testable with valid companyId/roomId pair, MEDIUM-HIGH impact.
[FINAL] JWT token validation bypass via alg/key confusion — **RANK 3**. Confidence 55, Auth class, PASSIVE JWKS probe first, CRITICAL impact if confirmed.
[NEXT] PROBE: POST `https://apis.alfaview.com/v2/auth/guest-link` with JSON `{"companyId":"<known>","roomId":"<known>","accessKey":"invalid-key-001","displayName":"test"}` — capture exact 401 response body, headers (Retry-After, X-RateLimit-*), and timing; repeat 10x rapidly (≤1 rps) to detect rate-limit enforcement. Requires known companyId/roomId pair (obtain from demo-company.alfaview.com page source or customer subdomain).
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 4-field combo (companyId+roomId+accessKey+displayName) — rate-limit testing needed.
[LEARN] ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both beta and production now expose /v2/languages with identical auth enforcement (401).
[LEARN] REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
[LEARN] REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only (4396B), /health=204, all common paths 404.
[LEARN] REJECTED MISCONFIG @ beta-webclient.alfaview.com: Same SPA shell as insider, no internal endpoints exposed.
[LEARN] ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface (SPA), testable immediately.
[LEARN] ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Hydra hosts are media/signaling servers returning "Hi Client" on all paths — no internal endpoints exposed.
[LEARN] REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same as above — media server, not admin panel.
[LEARN] REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same as above — media server, not admin panel.
[LEARN] REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: Unreachable/timeout — no surface.
[LEARN] REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: Unreachable/timeout — no surface.
[LEARN] REJECTED MISCONFIG @ alfacheck-video.alfaview.com: Unreachable/timeout — no surface.
[LEARN] ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
[LEARN] ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.
[RISK] alfaview: 72/100. Broad scope ("all company-owned infrastructure") with 55 dedicated hosts, 20+ confirmed live HTTP. Production APIs auth-gated (401). High-value IDOR (confidence 85) and AUTH (confidence 70) classes confirmed in OpenAPI spec. Multi-tenant SaaS on customer subdomains (3 confirmed identical SPA shells). 3 hydra media servers across 3 cloud providers expose unauthenticated HTTP 200 but serve only "Hi Client" — no sensitive endpoints. 31 hosts remain unprobed/unreachable (beta-ionoscloud-* fleet, etc.). Realistic path: obtain developer account → test IDOR cross-tenant + guest-link rate limits. Beta environment provides parallel attack surface with identical API. JWT alg/key confusion (confidence 55) adds CRITICAL-severity vector if JWKS misconfigured.
## 2026-09-04 17:50:54 UTC [target] (model nemotron3)
[PRIO] apis.alfaview.com,8.5,a:9 b:10 t:9 g:2 c:9 f:9
[PRIO] beta-apis.alfaview.com,8.2,a:9 b:9 t:9 g:2 c:8 f:9
[PRIO] app.alfaview.com,7.5,a:8 b:10 t:8 g:3 c:8 f:8
[PRIO] alfatraining.alfaview.com,6.2,a:7 b:8 t:6 g:9 c:7 f:7
[PRIO] bhc.alfaview.com,6.2,a:7 b:8 t:6 g:9 c:7 f:7
[PRIO] kh-freiburg.alfaview.com,6.2,a:7 b:8 t:6 g:9 c:7 f:7
[PRIO] demo-company.alfaview.com,5.8,a:6 b:7 t:6 g:9 c:6 f:6
[PRIO] beta-app.alfaview.com,5.5,a:6 b:8 t:7 g:2 c:6 f:6
[PRIO] internal.alfaview.com,5.0,a:5 b:7 t:6 g:2 c:6 f:5
[PRIO] beta-ionoscloud-21-beta-hydra-7x5d5.alfaview.com,4.5,a:5 b:6 t:5 g:9 c:5 f:5
[PRIO] beta-ionoscloud-21-beta-engine-gw4qw.alfaview.com,4.5,a:5 b:6 t:5 g:9 c:5 f:5
[PRIO] consul-monitoring.alfaview.com,3.0,a:3 b:5 t:3 g:9 c:3 f:3
[HYP] Cross-tenant IDOR on room permissions and user deletion via UUID path params
class: IDOR
asset: apis.alfaview.com
confidence: 85
reasoning: OpenAPI spec confirms DELETE /v2/users/{id} and PATCH/DELETE /v2/rooms/{roomId}/permissions/{userId} with UUID path params. Auth uses company-scoped JWT; server-side authorization (room membership vs global admin) unverified. Cross-tenant IDOR if API trusts token-issued companyId without validating room-level grants.
evidence_needed: Authenticated requests with valid token modifying roomId/userId from different company; 403 (enforced) vs 200/204 (broken) response difference
verify_steps: POST /v2/auth/api-key → obtain token → PATCH /v2/rooms/{victim-roomId}/permissions/{victim-userId} with foreign userId; observe 403 vs 204. Also DELETE /v2/users/{foreign-userId}.
impact: Cross-tenant permission manipulation or user deletion; severity HIGH (OWASP A01:2021)
testability: AUTH_HELPED
[HYP] Guest link auth rate-limit bypass on 4-field combo (companyId+roomId+accessKey+displayName)
class: AUTH
asset: apis.alfaview.com
confidence: 70
reasoning: Guest link auth endpoint POST /v2/auth/guest-link requires 4-field combo. Rate-limit status unknown. If no rate limiting on accessKey validation, attacker can brute-force valid accessKeys for known companyId/roomId pairs. AccessKey format/entropy unverified (schema shows string, example "0123456789ABCDEFGHIJKLMNOP" — 26 chars alphanumeric).
evidence_needed: Repeated POST to /v2/auth/guest-link with invalid accessKey; observe 429 vs consistent 401/403; measure requests/second before throttle
verify_steps: Identify valid companyId/roomId pair (from customer subdomain or demo) → send 50+ rapid POST requests to /v2/auth/guest-link with fixed companyId/roomId, rotating accessKey → monitor status codes and response timing for 429
impact: Unauthorized room access via accessKey enumeration; severity MEDIUM-HIGH
testability: AUTH_HELPED
[HYP] JWT token validation bypass via alg/key confusion on company-scoped tokens
class: AUTH
asset: apis.alfaview.com
confidence: 55
reasoning: API uses JWT for authentication (OpenAPI shows bearerAuth). Company-scoped tokens imply asymmetric or shared-secret signing. If RS256/HS256 confusion or JWKS endpoint misconfiguration exists, attacker could forge tokens with arbitrary companyId/roomId claims.
evidence_needed: JWKS endpoint exposure; token signing algorithm analysis; successful verification of forged HS256 token against RS256 public key
verify_steps: GET /.well-known/jwks.json or /v2/auth/jwks → analyze key structure → capture valid token → test alg confusion (RS256→HS256) with public key as HMAC secret → attempt token forgery with modified companyId
impact: Full authentication bypass, cross-tenant access, privilege escalation; severity CRITICAL
testability: PASSIVE (JWKS) + AUTH_HELPED (token capture)
[PARKED] Multi-tenant SaaS subdomain takeover via dangling CNAME on customer domains: confidence 40 < 60; no dangling CNAME evidence found, all three customer domains resolve to alfaview infrastructure (edge-proxy). PASSIVE-only testability limits exploit confirmation.
[FINAL] Cross-tenant IDOR on room permissions and user deletion via UUID path params — **RANK 1**. Confidence 85, direct OpenAPI evidence, HIGH severity, clear verify steps with auth.
[FINAL] Guest link auth rate-limit bypass on 4-field combo — **RANK 2**. Confidence 70, Auth class, testable with valid companyId/roomId pair, MEDIUM-HIGH impact.
[FINAL] JWT token validation bypass via alg/key confusion — **RANK 3**. Confidence 55, Auth class, PASSIVE JWKS probe first, CRITICAL impact if confirmed.
[NEXT] PROBE: POST `https://apis.alfaview.com/v2/auth/guest-link` with JSON `{"companyId":"<known>","roomId":"<known>","accessKey":"invalid-key-001","displayName":"test"}` — capture exact 401 response body, headers (Retry-After, X-RateLimit-*), and timing; repeat 10x rapidly (≤1 rps) to detect rate-limit enforcement. Requires known companyId/roomId pair (obtain from demo-company.alfaview.com page source or customer subdomain).
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 4-field combo (companyId+roomId+accessKey+displayName) — rate-limit testing needed.
[LEARN] ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both beta and production now expose /v2/languages with identical auth enforcement (401).
[LEARN] REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
[LEARN] REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only (4396B), /health=204, all common paths 404.
[LEARN] REJECTED MISCONFIG @ beta-webclient.alfaview.com: Same SPA shell as insider, no internal endpoints exposed.
[LEARN] ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface (SPA), testable immediately.
[LEARN] ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Media/signaling server ("Hi Client"), not OIDC/auth infrastructure. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — media server. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — media server. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: UNREACHABLE — all 3 probes timed out. Internal/firewalled. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: UNREACHABLE — all 3 probes timed out. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-video.alfaview.com: UNREACHABLE — root probe timed out. Target exhausted.
[LEARN] ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
[LEARN] ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.
[RISK] alfaview: 72/100. Broad scope ("all company-owned infrastructure") with 55 dedicated hosts, 20+ confirmed live HTTP. Production APIs auth-gated (401). High-value IDOR (confidence 85) and AUTH (confidence 70) classes confirmed in OpenAPI spec. Multi-tenant SaaS on customer subdomains (3 confirmed identical SPA shells). 3 hydra media servers across 3 cloud providers expose unauthenticated HTTP 200 but serve only "Hi Client" — no sensitive endpoints. 31 hosts remain unprobed/unreachable (beta-ionoscloud-* fleet, etc.). Realistic path: obtain developer account → test IDOR cross-tenant + guest-link rate limits. Beta environment provides parallel attack surface with identical API. JWT alg/key confusion (confidence 55) adds CRITICAL-severity vector if JWKS misconfigured.
## 2026-09-04 20:04:09 UTC [target] (model nemotron3)
[NEW] beta-ionoscloud-21-* fleet (7 hosts): `beta-ionoscloud-21-beta-audio-65st7`, `beta-ionoscloud-21-beta-audio-bdtmf`, `beta-ionoscloud-21-beta-engine-gw4qw`, `beta-ionoscloud-21-beta-engine-kzmvv`, `beta-ionoscloud-21-beta-hydra-7x5d5`, `beta-ionoscloud-21-beta-video-6pp2m`, `beta-ionoscloud-21-beta-video-l5mbv` — all HTTP unprobed in inventory, not in knowledge base
[NEW] Main domain set from initial recon (6 hosts): `alfaview.com`, `app.alfaview.com`, `dev.alfaview.com`, `sso.alfaview.com`, `test.alfaview.com`, `www.alfaview.com` — only `support`/`staging` probed (301), rest unprobed
[CHANGED] `apis.alfaview.com/v2/languages` now 401 (was 404) — endpoint added, aligns with beta; OpenAPI specs identical
[CHANGED] `alfatraining`/`bhc`/`kh-freiburg` XSS hypothesis REJECTED — byte-identical SPA shells (1381B, MD5 554a39), no tenant-specific rendering
[PRIO] apis.alfaview.com,8.8,a:9 b:9 t:9 g:3 c:9 f:9
[PRIO] beta-apis.alfaview.com,8.3,a:9 b:8 t:9 g:3 c:9 f:8
[PRIO] beta-ionoscloud-21-beta-hydra-7x5d5.alfaview.com,6.8,a:8 b:7 t:7 g:9 c:6 f:5
[PRIO] beta-ionoscloud-21-beta-engine-gw4qw.alfaview.com,6.8,a:8 b:7 t:7 g:9 c:6 f:5
[PRIO] app.alfaview.com,6.5,a:8 b:9 t:6 g:3 c:7 f:6
[PRIO] alfaview.com,6.2,a:7 b:10 t:5 g:5 c:7 f:5
[PRIO] sso.alfaview.com,6.0,a:7 b:9 t:6 g:3 c:7 f:5
[PRIO] internal.alfaview.com,5.5,a:6 b:7 t:6 g:2 c:6 f:5
[PRIO] beta-app.alfaview.com,5.5,a:6 b:7 t:6 g:2 c:6 f:5
[PRIO] dev.alfaview.com,5.0,a:6 b:7 t:5 g:4 c:6 f:4
[HYP] Cross-tenant IDOR on room permissions and user deletion via UUID path params
class: IDOR
asset: apis.alfaview.com
confidence: 85
reasoning: OpenAPI spec confirms DELETE /v2/users/{id} and PATCH/DELETE /v2/rooms/{roomId}/permissions/{userId} with UUID path params. Auth uses company-scoped JWT; server-side authorization (room membership vs global admin) unverified. Cross-tenant IDOR if API trusts token-issued companyId without validating room-level grants.
evidence_needed: Authenticated requests with valid token modifying roomId/userId from different company; 403 (enforced) vs 200/204 (broken) response difference
verify_steps: POST /v2/auth/api-key → obtain token → PATCH /v2/rooms/{victim-roomId}/permissions/{victim-userId} with foreign userId; observe 403 vs 204. Also DELETE /v2/users/{foreign-userId}.
impact: Cross-tenant permission manipulation or user deletion; severity HIGH (OWASP A01:2021)
testability: AUTH_HELPED
[HYP] Unprobed beta-ionoscloud infrastructure surface — potential SSRF or debug endpoints
class: MISCONFIG
asset: beta-ionoscloud-21-beta-hydra-7x5d5.alfaview.com
confidence: 60
reasoning: 7-host beta-ionoscloud fleet entirely unprobed (HTTP unprobed in inventory). Hydra hosts on other clouds (hcloud/noris/ovh) resolved to media/signaling servers ("Hi Client"). This fleet may expose different services (engine, audio, video, hydra). No probes = unknown attack surface.
evidence_needed: Live HTTP response on root, /health, /metrics, /debug, /actuator, /v2/ endpoints; service identification via headers/body
verify_steps: GET https://beta-ionoscloud-21-beta-hydra-7x5d5.alfaview.com/ (HEAD first) → if 200, probe /health, /metrics, /debug, /actuator, /v2/languages, /v2/docs/openapi.json; repeat for engine/audio/video hosts
impact: Unauthenticated debug/metrics exposure, SSRF via internal endpoints, cloud metadata access; severity MEDIUM-HIGH
testability: PASSIVE
[HYP] Main domain alfaview.com — unauthenticated web surface with potential OAuth/SSO flow flaws
class: AUTH
asset: alfaview.com
confidence: 55
reasoning: Primary marketing/auth entry point (alfaview.com) unprobed beyond DNS. Scope includes "alfaview web applications". Likely hosts login, OAuth initiate, password reset, signup flows. SSO subdomain (sso.alfaview.com) also unprobed. OAuth redirect_uri validation, state parameter handling, and password reset token leakage are high-value auth-class targets.
evidence_needed: OAuth authorize endpoint location, redirect_uri parameter acceptance, state parameter enforcement, password reset token delivery method (email vs response body)
verify_steps: GET https://alfaview.com/ → identify login/OAuth links → GET https://sso.alfaview.com/ → probe /authorize, /login, /password-reset endpoints with test redirect_uri values (localhost, evil.com, open redirect chains); check for state parameter requirement
impact: OAuth code theft → account takeover, password reset token leakage → ATO; severity HIGH-CRITICAL
testability: PASSIVE
[PARKED] JWT token validation bypass via alg/key confusion on company-scoped tokens: confidence 55 < 60 threshold; no JWKS endpoint discovered yet, verify_steps require AUTH_HELPED token capture first
[FINAL] Cross-tenant IDOR on room permissions and user deletion via UUID path params — **RANK 1**. Confidence 85, direct OpenAPI evidence, HIGH severity, clear verify steps with auth.
[FINAL] Unprobed beta-ionoscloud infrastructure surface — **RANK 2**. Confidence 60, 7 hosts completely untested, PASSIVE testability, potential new attack surface.
[FINAL] Main domain alfaview.com OAuth/SSO flow flaws — **RANK 3**. Confidence 55, primary auth entry point unprobed, HIGH-CRITICAL impact if redirect_uri/state flaws exist.
[NEXT] PROBE: HEAD `https://beta-ionoscloud-21-beta-hydra-7x5d5.alfaview.com/` → if 2xx/3xx/4xx (not timeout), follow with GET `/health`, `/metrics`, `/actuator/health`, `/v2/docs/openapi.json` on same host; repeat for `beta-ionoscloud-21-beta-engine-gw4qw.alfaview.com` (engine variant). Rate: ≤1 rps.
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 4-field combo (companyId+roomId+accessKey+displayName) — rate-limit testing needed.
[LEARN] ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both beta and production now expose /v2/languages with identical auth enforcement (401).
[LEARN] REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
[LEARN] REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only (4396B), /health=204, all common paths 404.
[LEARN] REJECTED MISCONFIG @ beta-webclient.alfaview.com: Same SPA shell as insider, no internal endpoints exposed.
[LEARN] ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface (SPA), testable immediately.
[LEARN] ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Media/signaling server ("Hi Client"), not OIDC/auth infrastructure. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — media server. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — media server. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: UNREACHABLE — all 3 probes timed out. Internal/firewalled. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: UNREACHABLE — all 3 probes timed out. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-video.alfaview.com: UNREACHABLE — root probe timed out. Target exhausted.
[LEARN] ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
[LEARN] ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.
[LEARN] REJECTED XSS @ alfatraining/bhc/kh-freiburg.alfaview.com: All three multi-tenant hosts serve byte-identical generic alfaview.com SPA shell (1381B, MD5 554a39...). No tenant-specific rendering, no inline data, no reflections. Target exhausted.
[RISK] alfaview: 70/100. Broad scope ("all company-owned infrastructure") with 55 dedicated hosts + 6 main domains. Production APIs auth-gated (401). High-value IDOR (confidence 85) and AUTH (confidence 70) classes confirmed in OpenAPI spec. Multi-tenant SaaS on 3 customer subdomains (identical SPA shells). 3 hydra media servers across 3 clouds expose unauthenticated HTTP 200 but serve only "Hi Client" — no sensitive endpoints. 7 beta-ionoscloud hosts completely unprobed (new surface). 6 main domains (alfaview.com, app, dev, sso, test, www) largely unprobed. 31 hosts exhausted/rejected. Realistic path: obtain developer account → test IDOR cross-tenant + guest-link rate limits. Beta environment provides parallel attack surface with identical API. JWT alg/key confusion (confidence 55) adds CRITICAL vector if JWKS misconfigured. Main domain OAuth/SSO flows untested.
## 2026-09-04 22:09:41 UTC [target] (model nemotron3)
[NEW] beta-ionoscloud-21-beta-hydra-7x5d5.alfaview.com: HTTP timeout (root + trailing slash) — unlike hcloud/noris/ovh hydra hosts which returned "Hi Client"
[NEW] alfaview.com: HTTP 200 — main marketing/auth entry point now confirmed live
[NEW] sso.alfaview.com: HTTP 200 len=0 — SSO endpoint live, empty body (likely redirects or SPA shell)
[CHANGED] beta-ionoscloud-21-* fleet (7 hosts): Previously "HTTP unprobed" → now 1/7 probed (hydra=timeout), 6 remain unprobed
[CHANGED] Main domain set (6 hosts): Previously only support/staging probed (301) → now alfaview.com + sso.alfaview.com confirmed HTTP 200, 4 remain unprobed (app, dev, test, www)
[PRIO] beta-ionoscloud-21-beta-engine-gw4qw.alfaview.com,7.2,a:9 b:8 t:7 g:9 c:6 f:5
[PRIO] beta-ionoscloud-21-beta-engine-kzmvv.alfaview.com,7.2,a:9 b:8 t:7 g:9 c:6 f:5
[PRIO] beta-ionoscloud-21-beta-audio-65st7.alfaview.com,7.0,a:8 b:8 t:7 g:9 c:6 f:5
[PRIO] beta-ionoscloud-21-beta-audio-bdtmf.alfaview.com,7.0,a:8 b:8 t:7 g:9 c:6 f:5
[PRIO] beta-ionoscloud-21-beta-video-6pp2m.alfaview.com,7.0,a:8 b:8 t:7 g:9 c:6 f:5
[PRIO] beta-ionoscloud-21-beta-video-l5mbv.alfaview.com,7.0,a:8 b:8 t:7 g:9 c:6 f:5
[PRIO] alfaview.com,6.8,a:8 b:10 t:6 g:3 c:7 f:5
[PRIO] sso.alfaview.com,6.5,a:7 b:9 t:7 g:3 c:7 f:5
[PRIO] app.alfaview.com,6.3,a:8 b:9 t:6 g:2 c:7 f:5
[PRIO] apis.alfaview.com,6.0,a:7 b:10 t:8 g:1 c:7 f:5
[PRIO] beta-apis.alfaview.com,5.8,a:7 b:9 t:8 g:1 c:7 f:5
[PRIO] dev.alfaview.com,5.5,a:6 b:7 t:5 g:4 c:6 f:4
[PRIO] test.alfaview.com,5.5,a:6 b:7 t:5 g:4 c:6 f:4
[PRIO] www.alfaview.com,5.5,a:6 b:7 t:5 g:4 c:6 f:4
[PRIO] beta-app.alfaview.com,5.0,a:6 b:7 t:6 g:1 c:6 f:5
[PRIO] internal.alfaview.com,5.0,a:6 b:7 t:6 g:1 c:6 f:5
[HYP] OAuth redirect_uri validation bypass on main domain leading to code theft
class: AUTH
asset: alfaview.com
confidence: 65
reasoning: alfaview.com returns HTTP 200 (live), primary marketing/auth entry point per scope. OAuth initiate/login flows likely hosted here or on sso.alfaview.com. redirect_uri parameter validation and state parameter enforcement untested. Open redirect on OAuth authorize → code theft → ATO.
evidence_needed: OAuth authorize endpoint location; redirect_uri acceptance of localhost/evil.com/open-redirect chains; state parameter optional vs required
verify_steps: GET https://alfaview.com/ → identify login/OAuth links → GET https://sso.alfaview.com/ → probe /authorize, /login, /oauth/authorize with redirect_uri=https://evil.com, redirect_uri=http://localhost, redirect_uri=https://alfaview.com/@evil.com; check state parameter requirement
impact: OAuth authorization code theft → full account takeover; severity CRITICAL (OWASP A07:2021)
testability: PASSIVE
[HYP] SSO endpoint OAuth authorize endpoint flaws — state parameter missing or redirect_uri over-permissive
class: AUTH
asset: sso.alfaview.com
confidence: 60
reasoning: sso.alfaview.com returns HTTP 200 len=0 (empty body), likely OAuth authorize endpoint or redirector. Dedicated SSO subdomain suggests centralized auth. State parameter enforcement and redirect_uri allowlist validation untested.
evidence_needed: OAuth authorize endpoint response; redirect_uri parameter behavior with external domains; state parameter presence/enforcement; PKCE support
verify_steps: GET https://sso.alfaview.com/ → if empty, probe /authorize?client_id=test&redirect_uri=https://evil.com&response_type=code&scope=openid → observe 302 location; test without state parameter; test with state=static; test PKCE challenge
impact: OAuth code theft via missing state or redirect_uri bypass → ATO; severity CRITICAL
testability: PASSIVE
[HYP] Unprobed beta-ionoscloud engine host exposes debug/admin/metrics endpoints or SSRF vector
class: MISCONFIG
asset: beta-ionoscloud-21-beta-engine-gw4qw.alfaview.com
confidence: 55
reasoning: 7-host beta-ionoscloud fleet entirely unprobed except hydra (timeout). Other cloud fleets (hcloud/noris/ovh) hydra hosts resolved to media servers ("Hi Client"). Engine hosts may expose different services (admin, metrics, actuator, debug). No HTTP probes = unknown surface.
evidence_needed: Live HTTP response on root; service identification via headers/body; presence of /health, /metrics, /actuator/health, /debug, /admin, /v2/docs/openapi.json, /v2/languages
verify_steps: HEAD https://beta-ionoscloud-21-beta-engine-gw4qw.alfaview.com/ → if 2xx/3xx/4xx (not timeout), GET /health, /metrics, /actuator/health, /debug, /admin, /v2/docs/openapi.json, /v2/languages; repeat for beta-ionoscloud-21-beta-engine-kzmvv.alfaview.com
impact: Unauthenticated debug/metrics exposure, SSRF via internal endpoints, cloud metadata access (169.254.169.254); severity MEDIUM-HIGH
testability: PASSIVE
[FINAL] OAuth redirect_uri validation bypass on main domain leading to code theft — **RANK 1**. Confidence 65, primary auth entry point live, PASSIVE testability, CRITICAL impact if redirect_uri/state flaws exist.
[FINAL] SSO endpoint OAuth authorize endpoint flaws — state parameter missing or redirect_uri over-permissive — **RANK 2**. Confidence 60, dedicated SSO subdomain live (empty body), PASSIVE testability, CRITICAL impact.
[FINAL] Unprobed beta-ionoscloud engine host exposes debug/admin/metrics endpoints or SSRF vector — **RANK 3**. Confidence 55, 7-host fleet completely untested (1 hydra timeout), PASSIVE testability, MEDIUM-HIGH impact.
[NEXT] PROBE: GET `https://alfaview.com/` → extract login/OAuth links → GET `https://sso.alfaview.com/authorize?client_id=test&redirect_uri=https://evil.com&response_type=code&scope=openid&state=test123` → observe 302 Location header and state parameter handling. Rate: 1 rps.
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 4-field combo (companyId+roomId+accessKey+displayName) — rate-limit testing needed.
[LEARN] ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both beta and production now expose /v2/languages with identical auth enforcement (401).
[LEARN] REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
[LEARN] REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only (4396B), /health=204, all common paths 404.
[LEARN] REJECTED MISCONFIG @ beta-webclient.alfaview.com: Same SPA shell as insider, no internal endpoints exposed.
[LEARN] ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface (SPA), testable immediately.
[LEARN] ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Media/signaling server ("Hi Client"), not OIDC/auth infrastructure. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — media server. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — media server. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: UNREACHABLE — all 3 probes timed out. Internal/firewalled. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: UNREACHABLE — all 3 probes timed out. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-video.alfaview.com: UNREACHABLE — root probe timed out. Target exhausted.
[LEARN] ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
[LEARN] ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.
[LEARN] REJECTED XSS @ alfatraining/bhc/kh-freiburg.alfaview.com: All three multi-tenant hosts serve byte-identical generic alfaview.com SPA shell (1381B, MD5 554a39). No tenant-specific rendering, no inline data, no reflections. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-ionoscloud-21-beta-hydra-7x5d5.alfaview.com: HTTP timeout on root and trailing slash — unlike other cloud hydra hosts, no "Hi Client" response. Target behavior unknown, fleet remains 6/7 unprobed.
[RISK] alfaview: 72/100. Broad scope ("all company-owned infrastructure") with 55 dedicated hosts + 6 main domains. Production APIs auth-gated (401). High-value IDOR (confidence 85) and AUTH (confidence 70) classes confirmed in OpenAPI spec. Multi-tenant SaaS on 3 customer subdomains (identical SPA shells). 3 hydra media servers across 3 clouds expose unauthenticated HTTP 200 but serve only "Hi Client" — no sensitive endpoints. 7 beta-ionoscloud hosts: 1 hydra timeout (behavior diverges from other clouds), 6 completely unprobed (engine/audio/video) — new attack surface. 6 main domains (alfaview.com, app, dev, sso, test, www): 2 now live (alfaview.com, sso.alfaview.com), 4 unprobed. 31 hosts exhausted/rejected. Realistic path: probe alfaview.com/sso.alfaview.com OAuth flows (PASSIVE, CRITICAL impact); probe beta-ionoscloud engine hosts (PASSIVE, MEDIUM-HIGH); obtain developer account → test IDOR cross-tenant + guest-link rate limits. Beta environment provides parallel attack surface with identical API. JWT alg/key confusion (confidence 55) adds CRITICAL vector if JWKS misconfigured. Main domain OAuth/SSO flows untested — highest immediate ROI.
## 2026-09-05 00:14:51 UTC [target] (model nemotron3)
[HYP] OAuth redirect_uri validation bypass on sso.alfaview.com leading to authorization code theft
class: AUTH
asset: sso.alfaview.com
confidence: 75
reasoning: FusionAuth 1.63.0 at sso.alfaview.com exposes OIDC discovery with authorization_endpoint=https://sso.alfaview.com/oauth2/authorize. Issuer=acme.com (misconfiguration). Implicit flow enabled. redirect_uri allowlist validation and state parameter enforcement untested. app.alfaview.com is the known client (login links from alfaview.com). No valid client_id discovered yet.
evidence_needed: Valid client_id for app.alfaview.com; redirect_uri acceptance of external domains (evil.com, localhost); state parameter optional vs required; PKCE enforcement
verify_steps: GET https://sso.alfaview.com/oauth2/authorize?client_id=<valid_id>&redirect_uri=https://evil.com&response_type=code&scope=openid&state=test → observe 302 Location; repeat without state; test PKCE challenge_method=S256
impact: OAuth authorization code theft → full account takeover (CRITICAL, OWASP A07:2021)
testability: PASSIVE
[HYP] JWT algorithm confusion via HS256 support in OIDC config with RSA-only JWKS
class: AUTH
asset: sso.alfaview.com
confidence: 70
reasoning: OIDC discovery lists id_token_signing_alg_values_supported including HS256, HS384, HS512 (symmetric) but JWKS only contains 7 RSA public keys (RS256). If token validation uses kid to fetch key but doesn't enforce alg=RS256, attacker could forge HS256-signed JWT using RSA public key as HMAC secret (classic alg confusion). Issuer=acme.com mismatch adds confusion.
evidence_needed: Valid access/id token to inspect alg/kid; token endpoint behavior with alg=HS256 header; JWKS key usage (sig vs enc)
verify_steps: POST https://sso.alfaview.com/oauth2/token with client_credentials (if public client) or observe tokens from app.alfaview.com login flow → decode header; attempt token forge with alg=HS256 using RSA pubkey as secret
impact: Arbitrary token forgery → ATO, privilege escalation (CRITICAL)
testability: AUTH_HELPED
[HYP] Unauthenticated binary distribution on test.alfaview.com — supply chain / version enumeration
class: MISCONFIG
asset: test.alfaview.com
confidence: 60
reasoning: test.alfaview.com serves alfacheck binaries (Linux/Windows/macOS/ARM) with version 470079 in filename. No auth, no integrity hashes, no signature verification visible. Version number enumerable. Could serve malicious binaries if storage compromised or version tampered.
evidence_needed: Binary hash verification mechanism; signature/checksum exposure; version enumeration across builds; CSP/subresource integrity on download links
verify_steps: GET https://test.alfaview.com/ → extract all hrefs; download each binary → check for embedded signatures/certificates; probe for directory listing or version enumeration (e.g., 470078, 470080)
impact: Supply chain compromise → RCE on client machines (HIGH)
testability: PASSIVE
[PARKED] JWT algorithm confusion via HS256 support: Requires valid token sample to confirm validation flaw; without auth, cannot observe actual token structure. Confidence 70 but needs AUTH_HELPED step first.  
[FINAL] OAuth redirect_uri validation bypass on sso.alfaview.com — **RANK 1**. Confidence 75, PASSIVE testability, CRITICAL impact. Need valid client_id — discoverable via app.alfaview.com JS bundles.  
[FINAL] Unauthenticated binary distribution on test.alfaview.com — **RANK 2**. Confidence 60, PASSIVE testability, HIGH impact. Fully verifiable now.
[NEXT] PROBE: GET `https://app.alfaview.com/js/app.min.5b3949112f0cf682adc8.js` → search for `client_id` or `oauth` config → use discovered client_id in `GET https://sso.alfaview.com/oauth2/authorize?client_id=<found>&redirect_uri=https://evil.com&response_type=code&scope=openid&state=test123` → observe 302 Location header and state handling. Rate: 1 rps.
[LEARN] ACCEPTED MISCONFIG @ sso.alfaview.com: OIDC discovery exposed with issuer=acme.com (not alfaview.com), implicit flow enabled, HS256 listed but only RSA keys in JWKS.  
[LEARN] ACCEPTED AUTH @ sso.alfaview.com: FusionAuth 1.63.0, /admin returns 404 (not exposed unauthenticated).  
[LEARN] ACCEPTED MISCONFIG @ test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079) for 4 platforms, no visible integrity verification.  
[LEARN] REJECTED MISCONFIG @ dev.alfaview.com: Timeout/unreachable.  
[LEARN] ACCEPTED MISCONFIG @ www.alfaview.com: 301 redirect to alfaview.com/en (no independent surface).  
[LEARN] REJECTED MISCONFIG @ beta-ionoscloud-21-beta-engine-*.alfaview.com: Both engine hosts timeout (000) — internal/firewalled like alfacheck-* fleet.
[RISK] alfaview: 78/100. Broad scope (55 dedicated hosts + 6 main domains). Production APIs auth-gated (401). Critical IDOR (DELETE /v2/users/{id}, PATCH /v2/rooms/{roomId}/permissions/{userId}) confirmed in OpenAPI — needs account. OAuth/SSO surface now live: sso.alfaview.com (FusionAuth) exposes OIDC metadata with issuer=acme.com misconfiguration, implicit flow, HS256 support vs RSA-only JWKS (alg confusion vector). app.alfaview.com is primary client. test.alfaview.com distributes unsigned binaries. 7-host beta-ionoscloud fleet completely unprobed (all timeout). 31 hosts exhausted. Realistic path: discover client_id via app.alfaview.com JS → probe OAuth redirect_uri/state on sso.alfaview.com (PASSIVE, CRITICAL); verify binary integrity on test.alfaview.com (PASSIVE, HIGH); obtain dev account → test IDOR cross-tenant + guest-link rate limits. JWT alg confusion (confidence 70) adds CRITICAL vector if token validation flawed.
## 2026-09-05 04:31:46 UTC [target] (model nemotron3)
[NEW] sso.alfaview.com: FusionAuth 1.63.0 OIDC discovery live (issuer=acme.com, implicit flow, HS256 in supported algs but RSA-only JWKS) — confirmed 2026-09-05 00:15
[NEW] test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079, 4 platforms) — no integrity verification visible
[NEW] alfaview.com: Marketing page live (301→/en, 177KB, strict CSP, matomo) — no SSO login links on marketing domain
[CHANGED] beta-ionoscloud-21-beta-engine-* (2 hosts): Confirmed timeout (000) — internal/firewalled like alfacheck-* fleet
[CHANGED] alfaview.com: Root now redirects to /en with nginx + Accept-Language vary header
[CHANGED] www.alfaview.com: 301→alfaview.com/en (no independent surface)
[PRIO] sso.alfaview.com,9.2,attack_surface=10,business_value=10,tech_exposure=9,gate_ease=10,cloud_surface=8,freshness=9
[PRIO] test.alfaview.com,7.8,attack_surface=8,business_value=7,tech_exposure=6,gate_ease=10,cloud_surface=7,freshness=9
[PRIO] apis.alfaview.com,7.5,attack_surface=9,business_value=10,tech_exposure=8,gate_ease=2,cloud_surface=8,freshness=7
[PRIO] beta-apis.alfaview.com,7.3,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=2,cloud_surface=8,freshness=7
[PRIO] app.alfaview.com,6.8,attack_surface=7,business_value=9,tech_exposure=7,gate_ease=3,cloud_surface=7,freshness=6
[PRIO] alfaview.com,5.5,attack_surface=6,business_value=6,tech_exposure=5,gate_ease=10,cloud_surface=6,freshness=8
[PRIO] beta-ionoscloud-21-beta-hydra-7x5d5.alfaview.com,5.2,attack_surface=5,business_value=5,tech_exposure=4,gate_ease=10,cloud_surface=6,freshness=8
[PRIO] beta-ionoscloud-21-beta-engine-*.alfaview.com (6 unprobed),4.8,attack_surface=6,business_value=5,tech_exposure=4,gate_ease=1,cloud_surface=6,freshness=8
[HYP] OAuth redirect_uri validation bypass on sso.alfaview.com leading to authorization code theft
class: OATH
asset: sso.alfaview.com
confidence: 75
reasoning: FusionAuth 1.63.0 exposes OIDC discovery with authorization_endpoint=https://sso.alfaview.com/oauth2/authorize. Issuer=acme.com (misconfiguration). Implicit flow enabled. redirect_uri allowlist validation and state parameter enforcement untested. app.alfaview.com is the known client (login links from alfaview.com). No valid client_id discovered yet.
evidence_needed: Valid client_id for app.alfaview.com; redirect_uri acceptance of external domains (evil.com, localhost); state parameter optional vs required; PKCE enforcement
verify_steps: GET https://app.alfaview.com/js/app.min.5b3949112f0cf682adc8.js → search for client_id or oauth config; GET https://sso.alfaview.com/oauth2/authorize?client_id=<found>&redirect_uri=https://evil.com&response_type=code&scope=openid&state=test123 → observe 302 Location; repeat without state; test PKCE challenge_method=S256
impact: OAuth authorization code theft → full account takeover (CRITICAL, OWASP A07:2021)
testability: PASSIVE
[HYP] JWT algorithm confusion via HS256 support in OIDC config with RSA-only JWKS
class: AUTH
asset: sso.alfaview.com
confidence: 70
reasoning: OIDC discovery lists id_token_signing_alg_values_supported including HS256, HS384, HS512 (symmetric) but JWKS only contains 7 RSA public keys (RS256). If token validation uses kid to fetch key but doesn't enforce alg=RS256, attacker could forge HS256-signed JWT using RSA public key as HMAC secret (classic alg confusion). Issuer=acme.com mismatch adds confusion.
evidence_needed: Valid access/id token to inspect alg/kid; token endpoint behavior with alg=HS256 header; JWKS key usage (sig vs enc)
verify_steps: GET https://sso.alfaview.com/.well-known/openid-configuration → confirm algs_supported; GET https://sso.alfaview.com/.well-known/jwks.json → confirm RSA-only keys; if valid token obtained via app.alfaview.com login flow → decode header; attempt token forge with alg=HS256 using RSA pubkey as secret
impact: Arbitrary token forgery → ATO, privilege escalation (CRITICAL)
testability: AUTH_HELPED
[HYP] Unauthenticated binary distribution on test.alfaview.com — supply chain / version enumeration
class: MISCONFIG
asset: test.alfaview.com
confidence: 60
reasoning: test.alfaview.com serves alfacheck binaries (Linux/Windows/macOS/ARM) with version 470079 in filename. No auth, no integrity hashes, no signature verification visible. Version number enumerable. Could serve malicious binaries if storage compromised or version tampered.
evidence_needed: Binary hash verification mechanism; signature/checksum exposure; version enumeration across builds; CSP/subresource integrity on download links
verify_steps: GET https://test.alfaview.com/ → extract all hrefs; download each binary → check for embedded signatures/certificates; probe for directory listing or version enumeration (e.g., 470078, 470080)
impact: Supply chain compromise → RCE on client machines (HIGH)
testability: PASSIVE
[PARKED] JWT algorithm confusion via HS256 support: Requires valid token sample to confirm validation flaw; without auth, cannot observe actual token structure. Confidence 70 but needs AUTH_HELPED step first.
[FINAL] OAuth redirect_uri validation bypass on sso.alfaview.com — **RANK 1**. Confidence 75, PASSIVE testability, CRITICAL impact. Need valid client_id — discoverable via app.alfaview.com JS bundles.
[FINAL] Unauthenticated binary distribution on test.alfaview.com — **RANK 2**. Confidence 60, PASSIVE testability, HIGH impact. Fully verifiable now.
[NEXT] PROBE: GET `https://app.alfaview.com/js/app.min.5b3949112f0cf682adc8.js` → search for `client_id` or `oauth` config → use discovered client_id in `GET https://sso.alfaview.com/oauth2/authorize?client_id=<found>&redirect_uri=https://evil.com&response_type=code&scope=openid&state=test123` → observe 302 Location header and state handling. Rate: 1 rps.
[LEARN] ACCEPTED MISCONFIG @ sso.alfaview.com: OIDC discovery exposed with issuer=acme.com (not alfaview.com), implicit flow enabled, HS256 listed but only RSA keys in JWKS.
[LEARN] ACCEPTED AUTH @ sso.alfaview.com: FusionAuth 1.63.0, /admin returns 404 (not exposed unauthenticated).
[LEARN] ACCEPTED MISCONFIG @ test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079) for 4 platforms, no visible integrity verification.
[LEARN] REJECTED MISCONFIG @ dev.alfaview.com: Timeout/unreachable.
[LEARN] ACCEPTED MISCONFIG @ www.alfaview.com: 301 redirect to alfaview.com/en (no independent surface).
[LEARN] REJECTED MISCONFIG @ beta-ionoscloud-21-beta-engine-*.alfaview.com: Both engine hosts timeout (000) — internal/firewalled like alfacheck-* fleet.
[RISK] alfaview: 78/100. Broad scope (55 dedicated hosts + 6 main domains). Production APIs auth-gated (401). Critical IDOR (DELETE /v2/users/{id}, PATCH /v2/rooms/{roomId}/permissions/{userId}) confirmed in OpenAPI — needs account. OAuth/SSO surface now live: sso.alfaview.com (FusionAuth) exposes OIDC metadata with issuer=acme.com misconfiguration, implicit flow, HS256 support vs RSA-only JWKS (alg confusion vector). app.alfaview.com is primary client. test.alfaview.com distributes unsigned binaries. 7-host beta-ionoscloud fleet completely unprobed (all timeout). 31 hosts exhausted. Realistic path: discover client_id via app.alfaview.com JS → probe OAuth redirect_uri/state on sso.alfaview.com (PASSIVE, CRITICAL); verify binary integrity on test.alfaview.com (PASSIVE, HIGH); obtain dev account → test IDOR cross-tenant + guest-link rate limits. JWT alg confusion (confidence 70) adds CRITICAL vector if token validation flawed.
## 2026-09-05 08:50:49 UTC [target] (model nemotron3)
[NEW] sso.alfaview.com: FusionAuth 1.63.0 OIDC discovery confirmed with issuer=acme.com (misconfiguration), implicit flow enabled, HS256/HS384/HS512 in id_token_signing_alg_values_supported but JWKS contains only 7 RSA keys (RS256) with CN=acme.com certificates
[NEW] test.alfaview.com: Unauthenticated binary distribution confirmed (alfacheck v470079, 4 platforms: linux/amd64, windows/amd64, mac/amd64, mac/arm64) — statically linked ELF, no integrity hashes/signatures visible
[NEW] app.alfaview.com: SPA loads from alfaview-com-assets.alfaview.com; no client_id discoverable in HTML/JS bundles; common client_id patterns (alfaview, app, web, client, spa, alfaview.com, app.alfaview.com) all return invalid_client_id
[CHANGED] beta-app.alfaview.com: HTTP 401 with WWW-Authenticate: Basic (not OAuth) — different auth mechanism than main app
[CHANGED] alfaview.com: Marketing page now 301→/en with nginx + Accept-Language vary, 177KB, strict CSP, matomo analytics — no SSO login links
[CHANGED] www.alfaview.com: 301→alfaview.com/en (no independent surface)
[PRIO] sso.alfaview.com,9.3,attack_surface=10,business_value=10,tech_exposure=10,gate_ease=10,cloud_surface=8,freshness=9
[PRIO] test.alfaview.com,7.8,attack_surface=8,business_value=7,tech_exposure=6,gate_ease=10,cloud_surface=7,freshness=9
[PRIO] apis.alfaview.com,7.5,attack_surface=9,business_value=10,tech_exposure=8,gate_ease=2,cloud_surface=8,freshness=7
[PRIO] app.alfaview.com,7.0,attack_surface=8,business_value=9,tech_exposure=7,gate_ease=3,cloud_surface=7,freshness=7
[PRIO] beta-apis.alfaview.com,7.3,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=2,cloud_surface=8,freshness=7
[PRIO] alfaview.com,5.5,attack_surface=6,business_value=6,tech_exposure=5,gate_ease=10,cloud_surface=6,freshness=8
[HYP] OAuth redirect_uri validation bypass on sso.alfaview.com leading to authorization code theft
class: OATH
asset: sso.alfaview.com
confidence: 75
reasoning: FusionAuth 1.63.0 exposes OIDC discovery with authorization_endpoint=https://sso.alfaview.com/oauth2/authorize. Issuer=acme.com (misconfiguration vs alfaview.com). Implicit flow enabled. redirect_uri allowlist validation and state parameter enforcement untested. app.alfaview.com is the known client but client_id not yet discovered. /oauth2/authorize enforces client_id registration (returns invalid_client_id for unregistered clients).
evidence_needed: Valid client_id for app.alfaview.com; redirect_uri acceptance of external domains (evil.com, localhost); state parameter optional vs required; PKCE enforcement behavior
verify_steps: GET https://app.alfaview.com/ → monitor network for OAuth config API call; GET https://sso.alfaview.com/oauth2/authorize?client_id=<found>&redirect_uri=https://evil.com&response_type=code&scope=openid&state=test123 → observe 302 Location header; repeat without state parameter; test PKCE challenge_method=S256
impact: OAuth authorization code theft → full account takeover (CRITICAL, OWASP A07:2021)
testability: AUTH_HELPED
[HYP] JWT algorithm confusion via HS256 support in OIDC config with RSA-only JWKS
class: AUTH
asset: sso.alfaview.com
confidence: 70
reasoning: OIDC discovery lists id_token_signing_alg_values_supported including HS256, HS384, HS512 (symmetric) but JWKS only contains 7 RSA public keys (RS256, use=sig). All certificates have CN=acme.com (self-signed, 2021-2031). If token validation uses kid to fetch key but doesn't enforce alg=RS256, attacker could forge HS256-signed JWT using RSA public key as HMAC secret (classic alg confusion). Issuer=acme.com mismatch adds confusion.
evidence_needed: Valid access/id token to inspect alg/kid header; token endpoint behavior with alg=HS256 header; JWKS key usage confirmation (all use=sig)
verify_steps: GET https://sso.alfaview.com/.well-known/openid-configuration → confirm algs_supported; GET https://sso.alfaview.com/.well-known/jwks.json → confirm RSA-only keys; obtain valid token via app.alfaview.com login flow → decode header; attempt token forge with alg=HS256 using RSA pubkey (n,e) as HMAC secret
impact: Arbitrary token forgery → ATO, privilege escalation (CRITICAL)
testability: AUTH_HELPED
[HYP] Unauthenticated binary distribution on test.alfaview.com — supply chain / version enumeration
class: MISCONFIG
asset: test.alfaview.com
confidence: 65
reasoning: test.alfaview.com serves alfacheck binaries (Linux/Windows/macOS/ARM) with version 470079 in filename. No auth, no integrity hashes (SHA256), no signature verification visible on download page. Version number enumerable (470078, 470080 return 404). Binary is statically linked ELF with certificate-related strings but no embedded PEM certificates. Could serve malicious binaries if storage compromised or version tampered.
evidence_needed: Binary hash verification mechanism (subresource integrity, checksums); signature/checksum exposure via API; version enumeration across builds; CSP/subresource integrity on download links
verify_steps: GET https://test.alfaview.com/ → extract all hrefs; download each binary → check for embedded signatures/certificates (strings, objdump); probe for directory listing or version enumeration (470078, 470080, 470000); check response headers for CSP, SRI
impact: Supply chain compromise → RCE on client machines (HIGH)
testability: PASSIVE
[PARKED] JWT algorithm confusion via HS256 support: Requires valid token sample to confirm validation flaw; without auth, cannot observe actual token structure. Confidence 70 but needs AUTH_HELPED step first.
[FINAL] OAuth redirect_uri validation bypass on sso.alfaview.com — **RANK 1**. Confidence 75, AUTH_HELPED testability, CRITICAL impact. Need valid client_id — discoverable via app.alfaview.com network monitoring or FusionAuth client enumeration.
[FINAL] Unauthenticated binary distribution on test.alfaview.com — **RANK 2**. Confidence 65, PASSIVE testability, HIGH impact. Fully verifiable now.
[NEXT] PROBE: GET `https://app.alfaview.com/` with browser DevTools / HAR capture → search network requests for OAuth config endpoint (client_id, authorization_endpoint, redirect_uri) → use discovered client_id in `GET https://sso.alfaview.com/oauth2/authorize?client_id=<found>&redirect_uri=https://evil.com&response_type=code&scope=openid&state=test123` → observe 302 Location header and state/PKCE handling. Rate: 1 rps.
[LEARN] ACCEPTED MISCONFIG @ sso.alfaview.com: OIDC discovery exposed with issuer=acme.com (not alfaview.com), implicit flow enabled, HS256 listed but only RSA keys in JWKS.
[LEARN] ACCEPTED AUTH @ sso.alfaview.com: FusionAuth 1.63.0, /admin returns 404 (not exposed unauthenticated).
[LEARN] ACCEPTED MISCONFIG @ test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079) for 4 platforms, no visible integrity verification.
[LEARN] ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — HTTP Basic auth gate (not OAuth).
[LEARN] REJECTED MISCONFIG @ dev.alfaview.com: Timeout/unreachable.
[LEARN] ACCEPTED MISCONFIG @ www.alfaview.com: 301 redirect to alfaview.com/en (no independent surface).
[LEARN] REJECTED MISCONFIG @ beta-ionoscloud-21-beta-engine-*.alfaview.com: Both engine hosts timeout (000) — internal/firewalled like alfacheck-* fleet.
[RISK] alfaview: 80/100. Broad scope (55 dedicated hosts + 6 main domains). Production APIs auth-gated (401). Critical IDOR (DELETE /v2/users/{id}, PATCH /v2/rooms/{roomId}/permissions/{userId}) confirmed in OpenAPI — needs account. OAuth/SSO surface live: sso.alfaview.com (FusionAuth 1.63.0) exposes OIDC metadata with issuer=acme.com misconfiguration, implicit flow, HS256 support vs RSA-only JWKS (alg confusion vector). app.alfaview.com is primary client but client_id not yet discovered. test.alfaview.com distributes unsigned binaries (supply chain risk). 7-host beta-ionoscloud fleet largely unprobed (1/7 hydra timeout, 6 unprobed). 31 hosts exhausted. Realistic path: discover client_id via app.alfaview.com network traffic → probe OAuth redirect_uri/state on sso.alfaview.com (AUTH_HELPED, CRITICAL); verify binary integrity on test.alfaview.com (PASSIVE, HIGH); obtain dev account → test IDOR cross-tenant + guest-link rate limits. JWT alg confusion (confidence 70) adds CRITICAL vector if token validation flawed.
## 2026-09-05 12:20:00 UTC [target] (model nemotron3)
## 2026-09-05 15:02:25 UTC [target] (model nemotron3)
## 2026-09-05 17:05:45 UTC [target] (model nemotron3)
[NEW] sso.alfaview.com: FusionAuth 1.63.0 OIDC discovery confirmed — issuer=acme.com (misconfiguration vs alfaview.com), implicit flow enabled, HS256/HS384/HS512 in id_token_signing_alg_values_supported but JWKS contains only 7 RSA keys (RS256, use=sig, CN=acme.com self-signed 2021-2031)
[NEW] app.alfaview.com/graphql: Full admin GraphQL schema leaked in public JS bundle (1.09MB) — 60+ mutations, 45+ queries with exact args; introspection disabled but resolver auth INCONSISTENT: listIdentityProviders returns data unauthenticated, listComponents returns 500 unauthenticated
[NEW] app.alfaview.com/graphql: guestAuthenticate(userId,companyId,roomId) and guestJoin(userId,companyId,roomId,displayName) mutations reachable unauthenticated (BAD_USER_INPUT not UNAUTHENTICATED) — NO accessKey parameter unlike REST 4-field combo (companyId+roomId+accessKey+displayName)
[NEW] test.alfaview.com: Unauthenticated binary distribution confirmed — alfacheck v470079 for 4 platforms (linux/amd64, windows/amd64, mac/amd64, mac/arm64), statically linked ELF, no integrity hashes/signatures visible on download page
[NEW] alfaview.com: Marketing page now 301→/en (nginx, Accept-Language vary), 177KB, strict CSP, matomo analytics — no unauthenticated SSO login links on marketing domain
[NEW] www.alfaview.com: 301→alfaview.com/en (no independent surface)
[NEW] beta-ionoscloud-21-beta-engine-* (2 hosts): Confirmed timeout (000) — internal/firewalled like alfacheck-* fleet
[NEW] beta-ionoscloud-21 fleet: 7 hosts total, only 1/7 probed (hydra=timeout), 6 remain unprobed
[CHANGED] apis.alfaview.com/v2/languages: Now returns 401 (was 404) — endpoint added to production, aligns with beta; OpenAPI specs now identical including /v2/languages
[CHANGED] beta-app.alfaview.com: HTTP 401 with WWW-Authenticate: Basic realm — different auth mechanism than main app (OAuth)
[CHANGED] alfacheck-engine/audio/video.alfaview.com: Confirmed UNREACHABLE via timeout probes — target exhausted
[CHANGED] beta-hcloud-19-beta-hydra-dzwx8 / beta-noris-33-beta-hydra-2zm7t / beta-ovh-29-beta-hydra-z4tf8: All confirmed media/signaling servers ("Hi Client" on all paths) — not OIDC/auth infrastructure, targets exhausted
[CHANGED] alfatraining/bhc/kh-freiburg.alfaview.com: XSS hypothesis REJECTED — all three serve byte-identical generic SPA shell (1381B, MD5 554a39), no tenant-specific rendering, no reflections
[CHANGED] insider-webclient.alfaview.com / beta-webclient.alfaview.com: SPA shell only (4396B), /health=204, all admin/debug paths 404 — targets exhausted
[CHANGED] demo-company.alfaview.com: SPA catch-all confirmed — /api/v1/users returns identical HTML shell as root, no unauthenticated data exposure
[PRIO] sso.alfaview.com,9.3,attack_surface=10,business_value=10,tech_exposure=10,gate_ease=10,cloud_surface=8,freshness=9
[PRIO] app.alfaview.com/graphql,8.8,attack_surface=9,business_value=10,tech_exposure=9,gate_ease=8,cloud_surface=7,freshness=9
[PRIO] test.alfaview.com,7.8,attack_surface=8,business_value=7,tech_exposure=6,gate_ease=10,cloud_surface=7,freshness=9
[PRIO] apis.alfaview.com,7.5,attack_surface=9,business_value=10,tech_exposure=8,gate_ease=2,cloud_surface=8,freshness=7
[PRIO] beta-apis.alfaview.com,7.3,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=2,cloud_surface=8,freshness=7
[PRIO] beta-ionoscloud-21-* fleet,6.5,attack_surface=7,business_value=6,tech_exposure=7,gate_ease=5,cloud_surface=8,freshness=8
[PRIO] alfaview.com,5.5,attack_surface=6,business_value=6,tech_exposure=5,gate_ease=10,cloud_surface=6,freshness=8
[HYP] OAuth redirect_uri validation bypass on sso.alfaview.com leading to authorization code theft
class: OATH
asset: sso.alfaview.com
confidence: 75
reasoning: FusionAuth 1.63.0 exposes OIDC discovery with authorization_endpoint=https://sso.alfaview.com/oauth2/authorize. Issuer=acme.com (misconfiguration vs alfaview.com). Implicit flow enabled. redirect_uri allowlist validation and state parameter enforcement untested. app.alfaview.com is the known client but client_id not yet discovered. /oauth2/authorize enforces client_id registration (returns invalid_client_id for unregistered clients).
evidence_needed: Valid client_id for app.alfaview.com; redirect_uri acceptance of external domains (evil.com, localhost); state parameter optional vs required; PKCE enforcement behavior
verify_steps: GET https://app.alfaview.com/ → monitor network for OAuth config API call (client_id, authorization_endpoint, redirect_uri); GET https://sso.alfaview.com/oauth2/authorize?client_id=<found>&redirect_uri=https://evil.com&response_type=code&scope=openid&state=test123 → observe 302 Location header; repeat without state parameter; test PKCE challenge_method=S256
impact: OAuth authorization code theft → full account takeover (CRITICAL, OWASP A07:2021)
testability: AUTH_HELPED
[HYP] Unauthenticated guest token issuance via GraphQL missing accessKey parameter
class: AUTH
asset: app.alfaview.com/graphql
confidence: 85
reasoning: GraphQL mutations guestAuthenticate(userId,companyId,roomId) and guestJoin(userId,companyId,roomId,displayName) are reachable unauthenticated (return BAD_USER_INPUT not UNAUTHENTICATED). REST guest-link flow at apis.alfaview.com/v2/auth/guest-link requires 4-field combo (companyId+roomId+accessKey+displayName). GraphQL path omits accessKey entirely — divergent auth logic. Full admin schema leaked in JS bundle confirms mutation signatures.
evidence_needed: Valid companyId/roomId/userId tuple from own tenant to test guestAuthenticate mutation; observe if token returned without accessKey; compare token scope/claims vs REST guest token
verify_steps: POST https://app.alfaview.com/graphql with {"query":"mutation{guestAuthenticate(userId:\"<own-guest-id>\",companyId:\"<own-company-id>\",roomId:\"<own-room-id>\"){token,errors}}"} → observe response; repeat with guestJoin including displayName; capture any returned token and decode (if JWT) or test against APIs
impact: Guest token issuance bypassing accessKey requirement → unauthorized room access, potential cross-tenant room joining (HIGH)
testability: AUTH_HELPED
[HYP] Unauthenticated binary distribution on test.alfaview.com — supply chain / version enumeration
class: MISCONFIG
asset: test.alfaview.com
confidence: 65
reasoning: test.alfaview.com serves alfacheck binaries (Linux/Windows/macOS/ARM) with version 470079 in filename. No auth, no integrity hashes (SHA256), no signature verification visible on download page. Version number enumerable (470078, 470080 return 404). Binary is statically linked ELF with certificate-related strings but no embedded PEM certificates. Could serve malicious binaries if storage compromised or version tampered.
evidence_needed: Binary hash verification mechanism (subresource integrity, checksums); signature/checksum exposure via API; version enumeration across builds; CSP/subresource integrity on download links
verify_steps: GET https://test.alfaview.com/ → extract all hrefs; download each binary → check for embedded signatures/certificates (strings, objdump); probe for directory listing or version enumeration (470078, 470080, 470000); check response headers for CSP, SRI
impact: Supply chain compromise → RCE on client machines (HIGH)
testability: PASSIVE
[PARKED] JWT algorithm confusion via HS256 support in OIDC config with RSA-only JWKS: Requires valid token sample to confirm validation flaw; without auth, cannot observe actual token structure. Confidence 70 but needs AUTH_HELPED step first. API gateway already rejected crafted HS256 JWT with RSA pubkey as HMAC secret (distinct 401 "No base64 encoded access token was provided").
[PARKED] Cross-tenant IDOR on apis.alfaview.com/v2 DELETE /v2/users/{id} and PATCH /v2/rooms/{roomId}/permissions/{userId}: Confirmed in OpenAPI (UUID path params) but requires authenticated account — gate_ease=2. Needs HUMAN account creation via self-service flow first.
[FINAL] 1. OAuth redirect_uri validation bypass on sso.alfaview.com — **RANK 1**. Confidence 75, AUTH_HELPED, CRITICAL impact. Need valid client_id discoverable via app.alfaview.com network monitoring.
[FINAL] 2. Unauthenticated guest token issuance via GraphQL missing accessKey — **RANK 2**. Confidence 85, AUTH_HELPED, HIGH impact. Divergent auth logic between REST (4-field+accessKey) and GraphQL (3-field no accessKey).
[FINAL] 3. Unauthenticated binary distribution on test.alfaview.com — **RANK 3**. Confidence 65, PASSIVE, HIGH impact. Fully verifiable now without auth.
[NEXT] PROBE: GET `https://app.alfaview.com/` with browser DevTools / HAR capture → search network requests for OAuth config endpoint (client_id, authorization_endpoint, redirect_uri) → use discovered client_id in `GET https://sso.alfaview.com/oauth2/authorize?client_id=<found>&redirect_uri=https://evil.com&response_type=code&scope=openid&state=test123` → observe 302 Location header and state/PKCE handling. Rate: 1 rps.
[LEARN] ACCEPTED MISCONFIG @ sso.alfaview.com: OIDC discovery exposed with issuer=acme.com (not alfaview.com), implicit flow enabled, HS256 listed but only RSA keys in JWKS.
[LEARN] ACCEPTED AUTH @ sso.alfaview.com: FusionAuth 1.63.0, /admin returns 404 (not exposed unauthenticated).
[LEARN] ACCEPTED MISCONFIG @ test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079) for 4 platforms, no visible integrity verification.
[LEARN] ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — HTTP Basic auth gate (not OAuth).
[LEARN] REJECTED MISCONFIG @ dev.alfaview.com: Timeout/unreachable.
[LEARN] ACCEPTED MISCONFIG @ www.alfaview.com: 301 redirect to alfaview.com/en (no independent surface).
[LEARN] REJECTED MISCONFIG @ beta-ionoscloud-21-beta-engine-*.alfaview.com: Both engine hosts timeout (000) — internal/firewalled like alfacheck-* fleet.
[LEARN] ACCEPTED MISCONFIG @ app.alfaview.com: Full GraphQL admin schema in public bundle; per-resolver auth (listIdentityProviders unauth data, listComponents unauth 500).
[LEARN] ACCEPTED AUTH @ app.alfaview.com/graphql: guestAuthenticate/guestJoin unauthenticated-reachable (BAD_USER_INPUT, not UNAUTHENTICATED); no accessKey in GraphQL guest signature — diverges from REST 4-field accessKey combo.
[LEARN] REJECTED AUTH @ apis.alfaview.com: Access tokens are opaque/base64 (distinct 401 "No base64 encoded access token was provided in the Authorization header."), not raw JWTs — JWT alg-confusion avenue closed.
[LEARN] ACCEPTED MISCONFIG @ alfaview.com: root 301→/en (nginx, Accept-Language vary), /en 177KB marketing page with strict CSP and matomo; no unauthenticated SSO login links on marketing domain.
[LEARN] REJECTED XSS @ alfatraining/bhc/kh-freiburg.alfaview.com: All three multi-tenant hosts serve byte-identical generic alfaview.com SPA shell (1381B, MD5 554a39). No tenant-specific rendering, no inline data, no reflections. Target exhausted.
[LEARN] REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only, /health=204, all common paths 404. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-webclient.alfaview.com: Identical SPA shell to insider, no internal endpoints. Target exhausted.
[LEARN] REJECTED MISCONFIG @ demo-company.alfaview.com: SPA catch-all confirmed — /api/v1/users returns identical HTML shell as root. No unauthenticated data exposure.
[LEARN] REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Media/signaling server ("Hi Client"), not OIDC/auth infrastructure. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — media server. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — media server. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: UNREACHABLE — all 3 probes timed out. Internal/firewalled. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: UNREACHABLE — all 3 probes timed out. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-video.alfaview.com: UNREACHABLE — root probe timed out. Target exhausted.
[RISK] alfaview: 80/100. Broad scope (55 dedicated hosts + 6 main domains). Production APIs auth-gated (401). Critical IDOR (DELETE /v2/users/{id}, PATCH /v2/rooms/{roomId}/permissions/{userId}) confirmed in OpenAPI — needs account. OAuth/SSO surface live: sso.alfaview.com (FusionAuth 1.63.0) exposes OIDC metadata with issuer=acme.com misconfiguration, implicit flow, HS256 support vs RSA-only JWKS (alg confusion vector). app.alfaview.com is primary client but client_id not yet discovered; GraphQL schema fully leaked with inconsistent resolver auth and divergent guest path (no accessKey). test.alfaview.com distributes unsigned binaries (supply chain risk). 7-host beta-ionoscloud fleet largely unprobed (1/7 hydra timeout, 6 unprobed). 31 hosts exhausted. Realistic path: discover client_id via app.alfaview.com network traffic → probe OAuth redirect_uri/state on sso.alfaview.com (AUTH_HELPED, CRITICAL); verify binary integrity on test.alfaview.com (PASSIVE, HIGH); obtain dev account → test IDOR cross-tenant + guest-link rate limits. JWT alg confusion (confidence 70) adds CRITICAL vector if token validation flawed.
## 2026-09-05 18:56:35 UTC [target] (model nemotron3)
[NEW] app.alfaview.com: Signup mutation unauthenticated (AppSignup.min.js lazy chunk) — sends NO token header; activation email-gated via finishSignup({companyId,username,activationToken,password}) at /finish-signup route
[NEW] app.alfaview.com/graphql: Apollo client uses credentials:"include" + custom token header (not Authorization/Bearer); Signup/FinishSignup send NO token → anonymous reachable; no CSRF token observed
[NEW] sso.alfaview.com: FusionAuth 1.63.0 OIDC discovery confirmed — issuer=acme.com (misconfiguration vs alfaview.com), implicit flow enabled, HS256/HS384/HS512 in id_token_signing_alg_values_supported but JWKS contains ONLY RSA keys
[NEW] app.alfaview.com/graphql: Full admin GraphQL schema leaked in public JS bundle (1.09MB) — 60+ mutations, 45+ queries with exact args; introspection disabled but resolver auth INCONSISTENT (listIdentityProviders returns data unauth, listComponents returns 500 unauth)
[NEW] app.alfaview.com/graphql: guestAuthenticate(userId,companyId,roomId) and guestJoin(userId,companyId,roomId,displayName) mutations reachable unauthenticated (BAD_USER_INPUT not UNAUTHENTICATED) — NO accessKey parameter (REST requires 4-field: companyId+roomId+accessKey+displayName)
[NEW] test.alfaview.com: Unauthenticated binary distribution confirmed — alfacheck v470079 for 4 platforms (linux/amd64, windows/amd64, mac/amd64, mac/arm64), statically linked ELF, no integrity hashes/signatures visible
[CHANGED] beta-ionoscloud-21-* fleet: 7 hosts total, only 1/7 probed (beta-ionoscloud-21-beta-hydra-7x5d5 = timeout); beta-engine-* (2 hosts) confirmed timeout (000) — internal/firewalled like alfacheck-* fleet; 6 hosts remain unprobed
[CHANGED] alfaview.com: Root 301→/en (nginx, Accept-Language vary), /en 177KB marketing page with strict CSP and matomo; no unauthenticated SSO login links on marketing domain
[CHANGED] www.alfaview.com: 301→alfaview.com/en (no independent surface)
[CHANGED] apis.alfaview.com/v2/languages: Now returns 401 (was 404) — endpoint added to production, aligns with beta; OpenAPI specs now identical including /v2/languages
[CHANGED] beta-app.alfaview.com: HTTP 401 with WWW-Authenticate: Basic realm — different auth mechanism than main app (OAuth)
[CHANGED] alfacheck-engine/audio/video.alfaview.com: Confirmed UNREACHABLE via timeout probes — target exhausted
[CHANGED] beta-hcloud-19-beta-hydra-dzwx8 / beta-noris-33-beta-hydra-2zm7t / beta-ovh-29-beta-hydra-z4tf8: All confirmed media/signaling servers ("Hi Client" on all paths) — not OIDC/auth infrastructure, target exhausted
[CHANGED] alfatraining/bhc/kh-freiburg.alfaview.com: XSS hypothesis REJECTED — all three serve byte-identical generic SPA shell (1381B, MD5 554a39), no tenant-specific rendering, no reflections
[CHANGED] insider-webclient.alfaview.com / beta-webclient.alfaview.com: SPA shell only (4396B), /health=204, all admin/debug paths 404 — targets exhausted
[CHANGED] demo-company.alfaview.com: SPA catch-all confirmed — /api/v1/users returns identical HTML shell as root, no unauthenticated data exposure
[PRIO] sso.alfaview.com,8.8,attack_surface=9,business_value=10,tech_exposure=9,gate_ease=10,cloud_surface=8,freshness=9
[PRIO] app.alfaview.com/graphql,8.5,attack_surface=9,business_value=9,tech_exposure=9,gate_ease=10,cloud_surface=7,freshness=9
[PRIO] test.alfaview.com,7.8,attack_surface=8,business_value=7,tech_exposure=6,gate_ease=10,cloud_surface=7,freshness=9
[PRIO] apis.alfaview.com,7.5,attack_surface=9,business_value=10,tech_exposure=8,gate_ease=2,cloud_surface=8,freshness=7
[PRIO] beta-apis.alfaview.com,7.3,attack_surface=9,business_value=9,tech_exposure=8,gate_ease=2,cloud_surface=8,freshness=7
[PRIO] beta-ionoscloud-21-* fleet,6.5,attack_surface=7,business_value=6,tech_exposure=7,gate_ease=5,cloud_surface=8,freshness=8
[PRIO] alfaview.com,5.5,attack_surface=6,business_value=6,tech_exposure=5,gate_ease=10,cloud_surface=6,freshness=8
[HYP] OAuth redirect_uri validation bypass on sso.alfaview.com leading to authorization code theft
class: OATH
asset: sso.alfaview.com
confidence: 75
reasoning: FusionAuth 1.63.0 exposes OIDC discovery with authorization_endpoint=https://sso.alfaview.com/oauth2/authorize. Issuer=acme.com (misconfiguration vs alfaview.com). Implicit flow enabled. redirect_uri allowlist validation and state parameter enforcement untested. app.alfaview.com is the known client but client_id not yet discovered. /oauth2/authorize enforces client_id registration (returns invalid_client_id for unregistered clients).
evidence_needed: Valid client_id for app.alfaview.com; redirect_uri acceptance of external domains (evil.com, localhost); state parameter optional vs required; PKCE enforcement behavior
verify_steps: GET https://app.alfaview.com/ → monitor network for OAuth config API call (client_id, authorization_endpoint, redirect_uri); GET https://sso.alfaview.com/oauth2/authorize?client_id=<found>&redirect_uri=https://evil.com&response_type=code&scope=openid&state=test123 → observe 302 Location header; repeat without state parameter; test PKCE challenge_method=S256
impact: OAuth authorization code theft → full account takeover (CRITICAL, OWASP A07:2021)
testability: AUTH_HELPED
[HYP] Unauthenticated guest token issuance via GraphQL missing accessKey parameter
class: AUTH
asset: app.alfaview.com/graphql
confidence: 85
reasoning: GraphQL mutations guestAuthenticate(userId,companyId,roomId) and guestJoin(userId,companyId,roomId,displayName) are reachable unauthenticated (return BAD_USER_INPUT not UNAUTHENTICATED). REST guest-link flow at apis.alfaview.com/v2/auth/guest-link requires 4-field combo (companyId+roomId+accessKey+displayName). GraphQL path omits accessKey entirely — divergent auth logic. Full admin schema leaked in JS bundle confirms mutation signatures.
evidence_needed: Valid companyId/roomId/userId tuple from own tenant to test guestAuthenticate mutation; observe if token returned without accessKey; compare token scope/claims vs REST guest token
verify_steps: POST https://app.alfaview.com/graphql with {"query":"mutation{guestAuthenticate(userId:\"<own-guest-id>\",companyId:\"<own-company-id>\",roomId:\"<own-room-id>\"){token,errors}}"} → observe response; repeat with guestJoin including displayName; capture any returned token and decode (if JWT) or test against APIs
impact: Guest token issuance bypassing accessKey requirement → unauthorized room access, potential cross-tenant room joining (HIGH)
testability: AUTH_HELPED
[HYP] Unauthenticated binary distribution on test.alfaview.com — supply chain / version enumeration
class: MISCONFIG
asset: test.alfaview.com
confidence: 65
reasoning: test.alfaview.com serves alfacheck binaries (Linux/Windows/macOS/ARM) with version 470079 in filename. No auth, no integrity hashes (SHA256), no signature verification visible on download page. Version number enumerable (470078, 470080 return 404). Binary is statically linked ELF with certificate-related strings but no embedded PEM certificates. Could serve malicious binaries if storage compromised or version tampered.
evidence_needed: Binary hash verification mechanism (subresource integrity, checksums); signature/checksum exposure via API; version enumeration across builds; CSP/subresource integrity on download links
verify_steps: GET https://test.alfaview.com/ → extract all hrefs; download each binary → check for embedded signatures/certificates (strings, objdump); probe for directory listing or version enumeration (470078, 470080, 470000); check response headers for CSP, SRI
impact: Supply chain compromise → RCE on client machines (HIGH)
testability: PASSIVE
[PARKED] JWT algorithm confusion via HS256 support in OIDC config with RSA-only JWKS: Requires valid token sample to confirm validation flaw; without auth, cannot observe actual token structure. Confidence 70 but needs AUTH_HELPED step first. API gateway already rejected crafted HS256 JWT with RSA pubkey as HMAC secret (distinct 401 "No base64 encoded access token was provided").
[PARKED] Cross-tenant IDOR on apis.alfaview.com/v2 DELETE /v2/users/{id} and PATCH /v2/rooms/{roomId}/permissions/{userId}: Confirmed in OpenAPI (UUID path params) but requires authenticated account — gate_ease=2. Needs HUMAN account creation via self-service flow first.
[FINAL] 1. OAuth redirect_uri validation bypass on sso.alfaview.com — **RANK 1**. Confidence 75, AUTH_HELPED, CRITICAL impact. Need valid client_id discoverable via app.alfaview.com network traffic.
[FINAL] 2. Unauthenticated guest token issuance via GraphQL missing accessKey — **RANK 2**. Confidence 85, AUTH_HELPED, HIGH impact. Divergent auth logic between REST (4-field+accessKey) and GraphQL (3-field no accessKey).
[FINAL] 3. Unauthenticated binary distribution on test.alfaview.com — **RANK 3**. Confidence 65, PASSIVE, HIGH impact. Fully verifiable now without auth.
[NEXT] PROBE: GET `https://app.alfaview.com/` with browser DevTools / HAR capture → search network requests for OAuth config endpoint (client_id, authorization_endpoint, redirect_uri) → use discovered client_id in `GET https://sso.alfaview.com/oauth2/authorize?client_id=<found>&redirect_uri=https://evil.com&response_type=code&scope=openid&state=test123` → observe 302 Location header and state/PKCE handling. Rate: 1 rps.
[LEARN] ACCEPTED MISCONFIG @ sso.alfaview.com: OIDC discovery exposed with issuer=acme.com (not alfaview.com), implicit flow enabled, HS256 listed but only RSA keys in JWKS.
[LEARN] ACCEPTED AUTH @ sso.alfaview.com: FusionAuth 1.63.0, /admin returns 404 (not exposed unauthenticated).
[LEARN] ACCEPTED MISCONFIG @ test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079) for 4 platforms, no visible integrity verification.
[LEARN] ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — HTTP Basic auth gate (not OAuth).
[LEARN] REJECTED MISCONFIG @ dev.alfaview.com: Timeout/unreachable.
[LEARN] ACCEPTED MISCONFIG @ www.alfaview.com: 301 redirect to alfaview.com/en (no independent surface).
[LEARN] REJECTED MISCONFIG @ beta-ionoscloud-21-beta-engine-*.alfaview.com: Both engine hosts timeout (000) — internal/firewalled like alfacheck-* fleet.
[LEARN] ACCEPTED MISCONFIG @ app.alfaview.com: Full GraphQL admin schema in public bundle; per-resolver auth (listIdentityProviders unauth data, listComponents unauth 500).
[LEARN] ACCEPTED AUTH @ app.alfaview.com/graphql: guestAuthenticate/guestJoin unauthenticated-reachable (BAD_USER_INPUT, not UNAUTHENTICATED); no accessKey in GraphQL guest signature — diverges from REST 4-field accessKey combo.
[LEARN] REJECTED AUTH @ apis.alfaview.com: Access tokens are opaque/base64 (distinct 401 "No base64 encoded access token was provided in the Authorization header."), not raw JWTs — JWT alg-confusion avenue closed.
[LEARN] ACCEPTED MISCONFIG @ alfaview.com: root 301→/en (nginx, Accept-Language vary), /en 177KB marketing page with strict CSP and matomo; no unauthenticated SSO login links on marketing domain.
[LEARN] REJECTED XSS @ alfatraining/bhc/kh-freiburg.alfaview.com: All three multi-tenant hosts serve byte-identical generic alfaview.com SPA shell (1381B, MD5 554a39). No tenant-specific rendering, no inline data, no reflections. Target exhausted.
[LEARN] REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only, /health=204, all common paths 404. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-webclient.alfaview.com: Identical SPA shell to insider, no internal endpoints. Target exhausted.
[LEARN] REJECTED MISCONFIG @ demo-company.alfaview.com: SPA catch-all confirmed — /api/v1/users returns identical HTML shell as root. No unauthenticated data exposure.
[LEARN] REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Media/signaling server ("Hi Client"), not OIDC/auth infrastructure. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — media server. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — media server. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: UNREACHABLE — all 3 probes timed out. Internal/firewalled. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: UNREACHABLE — all 3 probes timed out. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-video.alfaview.com: UNREACHABLE — root probe timed out. Target exhausted.
[RISK] alfaview: 80/100. Broad scope (55 dedicated hosts + 6 main domains). Production APIs auth-gated (401). Critical IDOR (DELETE /v2/users/{id}, PATCH /v2/rooms/{roomId}/permissions/{userId}) confirmed in OpenAPI — needs account. OAuth/SSO surface live: sso.alfaview.com (FusionAuth 1.63.0) exposes OIDC metadata with issuer=acme.com misconfiguration, implicit flow, HS256 support vs RSA-only JWKS (alg confusion vector). app.alfaview.com is primary client but client_id not yet discovered; GraphQL schema fully leaked with inconsistent resolver auth and divergent guest path (no accessKey). test.alfaview.com distributes unsigned binaries (supply chain risk). 7-host beta-ionoscloud fleet largely unprobed (1/7 hydra timeout, 6 unprobed). 31 hosts exhausted. Realistic path: discover client_id via app.alfaview.com network traffic → probe OAuth redirect_uri/state on sso.alfaview.com (AUTH_HELPED, CRITICAL); verify binary integrity on test.alfaview.com (PASSIVE, HIGH); obtain dev account → test IDOR cross-tenant + guest-link rate limits. JWT alg confusion (confidence 70) adds CRITICAL vector if token validation flawed.
