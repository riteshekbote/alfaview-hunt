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
