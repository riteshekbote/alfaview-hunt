## 2026-09-03 14:06:59 UTC [target] (model bigpickle)
[PRIO] beta-apis.alfaview.com, 8.8, a:7 b:8 t:9(API+OpenAPI) g:6(cloud:3,auth-gate:3) c:3 f:5
[PRIO] demo-company.alfaview.com, 8.3, a:9 b:8 t:6(web app) g:8(no basic auth gate) c:3 f:5
[PRIO] internal.alfaview.com, 7.0, a:6 b:9 t:5(Basic auth) g:3(closed) c:3 f:5
[HYP] IDOR on room permissions and user deletion
class: IDOR
asset: apis.alfaview.com
confidence: 72
reasoning: OpenAPI spec exposes DELETE /v2/users/{id} and PATCH /v2/rooms/{roomId}/permissions/{userId} — both accept UUID path params. Auth model uses company-scoped tokens; server-side authorization (room membership vs global admin) is unverified. Classic IDOR pattern if the API trusts token-issued companyId without checking room-level permission grants.
evidence_needed: Authenticated requests with a valid token modifying roomId/userId belonging to a different company; different 403 vs 200/204 response confirming cross-tenant enforcement.
verify_steps: POST /v2/auth/api-key with valid creds → PATCH /v2/rooms/{roomId}/permissions/{userId} with a userId from a different company account; observe 403 (enforced) vs 204 (broken). Also DELETE /v2/users/{id} with a foreign userId.
impact: Cross-tenant permission manipulation or user deletion; severity HIGH (broken access control = OWASP A01).
testability: AUTH_HELPED
[HYP] Guest link access key enumeration via auth oracle
class: AUTH
asset: apis.alfaview.com/v2/auth/group-link
confidence: 65
reasoning: Group/guest link auth requires companyId + roomId + accessKey. The auth endpoint returns distinct errors: 401 "A guest with the provided information could not be found and authenticated" vs 422 if wrong endpoint type used. If accessKeys are low-entropy or sequential, and rate-limiting is absent per-scope, brute force of accessKey for a known companyId+roomId yields a guest session token.
evidence_needed: Rate-limited probe of /v2/auth/group-link with a known companyId+roomId and random accessKeys; observe response timing or 429 after N requests confirming (or not) brute-force feasibility.
verify_steps: POST /v2/auth/group-link with companyId=<known>, roomId=<known>, accessKey=<random-12-char>, displayName="test" — note 401 response time. Repeat 10x at 1 rps to detect rate limiting.
impact: Guest session impersonation; access to private meeting rooms; severity MEDIUM-HIGH.
testability: AUTH_HELPED
[HYP] Beta API environment weaker auth enforcement
class: MISCONFIG
asset: beta-apis.alfaview.com
confidence: 55
reasoning: beta-apis resolves to a separate IP pool (185.127.28.102, 185.245.100.219, 195.201.44.172) from production apis. Beta environments commonly have relaxed validation, disabled features, or test credentials. If the same OpenAPI spec applies but auth middleware differs, unauthenticated access or test API keys may work.
evidence_needed: Comparison of /v2/languages or /v2/room-types response on beta-apis vs apis without Authorization header; check for different error bodies or 200 vs 401.
verify_steps: GET https://beta-apis.alfaview.com/v2/languages (no auth) — compare response code and body against the same request on apis.alfaview.com (401).
impact: Unauthenticated API access to user data, room data, and meeting statistics; severity HIGH.
testability: PASSIVE (no auth needed to probe response codes)
[PARKED] IDOR on room permissions: requires valid API credentials to confirm. Confidence 72 is justified by the explicit path-param UUID pattern in the spec but server-side authorization is unverified without auth testing. Remains as highest-priority authenticated test.
[PARKED] Guest link access key enumeration: needs valid companyId+roomId to confirm rate limiting. Confidence 65 reflects the speculative brute-force angle. Keep for authenticated phase.
[FINAL] Beta API weaker auth enforcement — **RANK 1 for next probe**. PASSIVE, no auth required, directly testable.
[NEXT] PROBE: GET `https://beta-apis.alfaview.com/v2/languages` — no Authorization header, compare response code/body vs production `https://apis.alfaview.com/v2/languages` (known 401).
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: guest link auth flow requires 3-field combo (companyId+roomId+accessKey) — rate-limit testing needed.
[LEARN] REJECTED MISCONFIG @ internal.alfaview.com: HTTP Basic auth gate confirmed; default credential testing is out of scope per program rules (brute-force rejected class).
[RISK] alfaview: 65/100. Scope is broad ("all company-owned infrastructure") but active testing on production APIs requires an API key or guest account. 1 rps rate limit constrains brute-force. All high-value endpoints are auth-gated. Realistic path: obtain a developer account, test IDOR and guest-link flows.
## 2026-09-03 15:10:59 UTC [target] (model bigpickle)
[CHANGED] beta-apis.alfaview.com: Auth response identical to production (401 + same error body). Beta weaker auth hypothesis disconfirmed.
[NEW] beta-webclient.alfaview.com (HTTP 200): High-value web client surface, untested.
[NEW] insider-webclient.alfaview.com (HTTP 200): Internal tooling potentially exposed.
[PRIO] demo-company.alfaview.com, 8.7, a:9 b:9 t:6(web app) g:9(no auth gate) c:3 f:5
[PRIO] beta-webclient.alfaview.com, 8.2, a:7 b:8 t:7(web client) g:7(no basic auth) c:3 f:4
[PRIO] apis.alfaview.com, 8.0, a:8 b:8 t:9(API) g:6(cloud:3,auth-gate:3) c:3 f:5
[HYP] IDOR on room permissions and user deletion
class: IDOR
asset: apis.alfaview.com
confidence: 72
reasoning: OpenAPI spec exposes DELETE /v2/users/{id} and PATCH /v2/rooms/{roomId}/permissions/{userId} — both accept UUID path params. Auth model uses company-scoped tokens; server-side authorization (room membership vs global admin) is unverified. Classic IDOR pattern if the API trusts token-issued companyId without checking room-level permission grants.
evidence_needed: Authenticated requests with a valid token modifying roomId/userId belonging to a different company; different 403 vs 200/204 response confirming cross-tenant enforcement.
verify_steps: POST /v2/auth/api-key with valid creds → PATCH /v2/rooms/{roomId}/permissions/{userId} with a userId from a different company account; observe 403 (enforced) vs 204 (broken). Also DELETE /v2/users/{id} with a foreign userId.
impact: Cross-tenant permission manipulation or user deletion; severity HIGH (broken access control = OWASP A01).
testability: AUTH_HELPED
[HYP] Guest link access key enumeration via auth oracle
class: AUTH
asset: apis.alfaview.com/v2/auth/group-link
confidence: 65
reasoning: Group/guest link auth requires companyId + roomId + accessKey. The auth endpoint returns distinct errors: 401 "A guest with the provided information could not be found and authenticated" vs 422 if wrong endpoint type used. If accessKeys are low-entropy or sequential, and rate-limiting is absent per-scope, brute force of accessKey for a known companyId+roomId yields a guest session token.
evidence_needed: Rate-limited probe of /v2/auth/group-link with a known companyId+roomId and random accessKeys; observe response timing or 429 after N requests confirming (or not) brute-force feasibility.
verify_steps: POST /v2/auth/group-link with companyId=<known>, roomId=<known>, accessKey=<random-12-char>, displayName="test" — note 401 response time. Repeat 10x at 1 rps to detect rate limiting.
impact: Guest session impersonation; access to private meeting rooms; severity MEDIUM-HIGH.
testability: AUTH_HELPED
[HYP] Demo company exposes test user data via unauthenticated API
class: MISCONFIG
asset: demo-company.alfaview.com
confidence: 60
reasoning: demo-company.alfaview.com serves HTTP 200 without authentication. Demo/test environments often have weakened security controls, default credentials, or exposed test data. If this is a publicly accessible demo instance, it may leak test user information, company structures, or internal API endpoints.
evidence_needed: Probe common endpoints (/api, /v1, /v2, /graphql) on demo-company.alfaview.com without auth; check for exposed user lists, company data, or debug interfaces.
verify_steps: GET https://demo-company.alfaview.com/ and observe response; then GET https://demo-company.alfaview.com/api/v1/users (or similar) to test for unauthenticated data access.
impact: Exposure of test/demo user data, company structures; severity MEDIUM.
testability: PASSIVE (no auth needed)
[PARKED] Beta API weaker auth enforcement — disconfirmed by identical 401 responses.
[FINAL] IDOR on room permissions: 72% confidence, highest value, AUTH_HELPED.
[FINAL] Guest link access key enumeration: 65% confidence, requires valid credentials.
[FINAL] Demo company test data exposure: 60% confidence, PASSIVE, immediate testability.
[NEXT] PROBE: GET `https://demo-company.alfaview.com/api/v1/users` — no auth, check for exposed user list or test data.
[LEARN] REJECTED MISCONFIG @ beta-apis.alfaview.com: Auth response identical to production (401 + same error body). Beta environment has same auth enforcement.
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: Highest-priority authenticated test target.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 3-field combo.
[LEARN] ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface, testable immediately.
[RISK] alfaview: 62/100. Scope is broad but all high-value endpoints are auth-gated. Beta API disconfirmed as weaker. Demo company presents immediate passive test opportunity. Realistic path: obtain demo/test account, test IDOR and guest-link flows; meanwhile probe demo-company for exposed test data.
## 2026-09-03 18:41:01 UTC [target] (model bigpickle)
[PRIO] insider-webclient.alfaview.com, 7.2, a:8 b:9 t:7 g:8 c:3 f:4
[PRIO] beta-webclient.alfaview.com, 6.4, a:6 b:7 t:7 g:7 c:3 f:4
[PRIO] alfatraining.alfaview.com, 6.3, a:7 b:6 t:5 g:7 c:3 f:3
[PRIO] demo-company.alfaview.com, 5.5, a:4 b:4 t:3 g:9 c:3 f:5 (SPA disconfirmed)
[PRIO] bhc.alfaview.com, 5.0, a:5 b:5 t:3 g:5 c:3 f:3
[HYP] Insider web client exposes internal admin panel without auth
class: MISCONFIG
asset: insider-webclient.alfaview.com
confidence: 55
reasoning: Returns HTTP 200 with no auth gate. Name explicitly says "insider" — likely internal tooling for employees/admins. Internal tools are frequently deployed with weaker auth controls, debug modes, or default sessions. If this is a React/Vue SPA, client-side routes may expose admin functionality hidden behind JS routing but accessible via direct path.
evidence_needed: Response body content-type and HTML structure; presence of API base URL, admin routes, or debug flags in bundled JS; probe /api, /admin, /graphql, /internal paths.
verify_steps: GET https://insider-webclient.alfaview.com/ — observe content-type, title, script tags. GET https://insider-webclient.alfaview.com/api/health or /api/config for exposed backend routes.
impact: Exposure of internal admin tooling, potential access to user management, room administration, or meeting data; severity MEDIUM-HIGH.
testability: PASSIVE
[HYP] Beta web client exposes debug/test routes or weaker CSP
class: MISCONFIG
asset: beta-webclient.alfaview.com
confidence: 45
reasoning: Beta web clients often have debug flags, source maps, verbose error handling, or test accounts baked in. If the SPA bundles contain dev-mode conditionals (e.g., window.__DEV__, REACT_APP_ENV=beta), these may expose admin functions or API endpoints not present in production. Beta API version drift already confirmed (beta has /v2/languages, prod doesn't).
evidence_needed: Response body inspection for env flags, source map URLs, test account references in JS bundles; compare CSP headers vs production app.alfaview.com.
verify_steps: GET https://beta-webclient.alfaview.com/ — observe content-type, title, script src paths. GET https://beta-webclient.alfaview.com/asset-manifest.json or /manifest.json for build info.
impact: Leaked beta API endpoints, debug functionality, or test credentials; severity MEDIUM.
testability: PASSIVE
[HYP] Training platform exposes user data or test accounts
class: MISCONFIG
asset: alfatraining.alfaview.com
confidence: 40
reasoning: Training platforms often contain test user accounts, course completion data, or internal employee information. Returns HTTP 200 without auth gate. If this is a standalone training LMS, it may have exposed user lists, admin panels, or API endpoints.
evidence_needed: Response body content-type and structure; probe /api, /users, /admin, /courses paths; check for login page vs direct content access.
verify_steps: GET https://alfatraining.alfaview.com/ — observe response. GET https://alfatraining.alfaview.com/api/v1/users or /admin.
impact: Exposure of employee training data, user accounts; severity LOW-MEDIUM.
testability: PASSIVE
[NEXT] PROBE: GET https://insider-webclient.alfaview.com/ — observe response body, content-type, title tag, script src URLs. Then GET https://insider-webclient.alfaview.com/api/health or /api/config for exposed backend routes.
[LEARN] REJECTED MISCONFIG @ demo-company.alfaview.com: SPA confirmed — /api/v1/users serves same HTML shell as root. No real data exposure.
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: Highest-priority authenticated test target.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 3-field combo.
[LEARN] ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift confirmed — beta has /v2/languages absent in production.
[RISK] alfaview: 60/100. Scope is broad but all high-value API endpoints are auth-gated. Beta API weaker auth disproven. Demo company is SPA shell. Insider-webclient is the most promising untested passive target — internal tooling naming strongly suggests exposed admin surface. Realistic path: probe insider-webclient/beta-webclient passively, then obtain credentials for IDOR/guest-link testing.
## 2026-09-03 21:19:55 UTC [target] (model bigpickle)
[PRIO] insider-webclient.alfaview.com, 7.8, a:8 b:9 t:7 g:8 c:3 f:4
[PRIO] beta-webclient.alfaview.com, 6.8, a:7 b:7 t:7 g:7 c:3 f:4
[PRIO] apis.alfaview.com, 8.2, a:9 b:10 t:9 g:4 c:4 f:5
[PRIO] alfatraining.alfaview.com, 5.8, a:6 b:5 t:4 g:7 c:2 f:3
[PRIO] bhc.alfaview.com, 4.5, a:5 b:4 t:3 g:5 c:2 f:3
[HYP] Cross-tenant IDOR on room permissions and user deletion via UUID path params
class: IDOR
asset: apis.alfaview.com
confidence: 75
reasoning: OpenAPI spec confirms DELETE /v2/users/{id} and PATCH/DELETE /v2/rooms/{roomId}/permissions/{userId} with UUID path params. Auth uses company-scoped JWT tokens. Server-side authorization (room membership vs global admin scope) unverified. OpenAPI specs are identical between beta and prod — no beta-only escape. Classic cross-tenant IDOR if the API trusts token-issued companyId without validating room-level permission grants.
evidence_needed: Authenticated requests with valid token modifying roomId/userId belonging to a different company; 403 (enforced) vs 204 (broken) response.
verify_steps: POST /v2/auth/api-key → obtain token → PATCH /v2/rooms/{victim-roomId}/permissions/{victim-userId} with a foreign userId; observe 403 vs 204. Also DELETE /v2/users/{foreign-userId}.
impact: Cross-tenant permission manipulation or user deletion; severity HIGH (OWASP A01:2021).
testability: AUTH_HELPED
[HYP] Guest link access key enumeration via auth oracle
class: AUTH
asset: apis.alfaview.com/v2/auth/group-link
confidence: 65
reasoning: Guest auth endpoint POST /v2/auth/group-link requires companyId+roomId+accessKey. Endpoint returns distinct 401 "guest not found" vs 422 errors — oracle for accessKey validity. If accessKeys are low-entropy/sequential and per-scope rate limiting absent, brute-force yields guest session token.
evidence_needed: Rate-limited probe with known companyId+roomId + random accessKeys; 429 after N requests confirming (or not) brute-force feasibility.
verify_steps: POST /v2/auth/group-link with companyId=<known>, roomId=<known>, accessKey=<random-12-char>, displayName="test" — measure 401 response time. Repeat 10x at 1 rps; detect 429 or timing delta.
impact: Guest session impersonation → private meeting room access; severity MEDIUM-HIGH.
testability: AUTH_HELPED
[HYP] Insider web client exposes admin SPA routes or API backend config
class: MISCONFIG
asset: insider-webclient.alfaview.com
confidence: 58
reasoning: Returns HTTP 200 with 4396 bytes of HTML and no auth gate. Name "insider" strongly implies internal/employee-facing tool. SPA shells typically embed API base URLs, feature flags, or build metadata in HTML or linked JS bundles. If the SPA loads client-side routes for admin panels (user management, room admin, meeting history) without server-side auth, direct path access bypasses the login UI. Previous probe confirmed /api/* paths return same SPA shell on demo-company — insider-webclient may differ since it may have its own backend.
evidence_needed: Content-type header, HTML title tag, script src URLs (reveal API base, build version, env flags); probe /api/health, /api/config, /admin, /internal for non-SPA responses.
verify_steps: GET https://insider-webclient.alfaview.com/ — capture Content-Type, title, and all <script src> URLs. GET https://insider-webclient.alfaview.com/api/health and /api/config for exposed backend routes.
impact: Internal admin panel exposure, API endpoint discovery, potential access to user/room management; severity MEDIUM-HIGH.
testability: PASSIVE
[NEXT] PROBE: GET https://insider-webclient.alfaview.com/ — capture Content-Type header, <title> tag, and all <script src> URLs. Then GET https://insider-webclient.alfaview.com/api/health and https://insider-webclient.alfaview.com/api/config — check for non-SPA responses indicating exposed backend routes.
[LEARN] REJECTED MISCONFIG @ beta-apis.alfaview.com: Undocumented endpoints (/v2/debug, /v2/admin, /v2/internal, /v2/test, /v2/health) all 404. Beta surface fully enumerated — only /v2/languages diverges.
[LEARN] ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift limited to /v2/languages only — confirmed after full endpoint probe.
[LEARN] REJECTED MISCONFIG @ demo-company.alfaview.com: SPA catch-all confirmed — /api/v1/users returns identical HTML shell as root (len=1381). No unauthenticated data exposure.
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI specs identical beta/prod. UUID path params on DELETE /v2/users/{id} and PATCH /v2/rooms/{roomId}/permissions/{userId}. Highest-priority authenticated test target.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth requires 3-field combo. Rate-limit status unknown — needs authenticated probe.
[RISK] alfaview: 62/100. All high-value API endpoints are auth-gated (401/404). No unauthenticated data exposure found on demo-company, beta-apis, or internal. Insider-webclient is the last high-value passive target — if SPA routes expose admin panels without auth, severity jumps to MEDIUM-HIGH. Realistic next steps: (1) probe insider-webclient JS bundles for admin routes/API base URLs, (2) obtain demo or test account to test IDOR cross-tenant, (3) test guest-link rate limiting. The program's broad scope ("all company-owned infrastructure") is a positive signal for acceptance of internal-tool findings.
