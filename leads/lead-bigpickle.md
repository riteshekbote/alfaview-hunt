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
## 2026-09-03 23:19:35 UTC [target] (model bigpickle)
## 2026-09-04 01:03:20 UTC [target] (model bigpickle)
[PRIO] alfacheck-engine.alfaview.com, 6.2, a:7 b:6 t:7 g:9 c:3 f:8
[PRIO] alfacheck-audio.alfaview.com, 6.0, a:7 b:5 t:7 g:9 c:3 f:8
[PRIO] alfacheck-video.alfaview.com, 6.0, a:7 b:5 t:7 g:9 c:3 f:8
[PRIO] beta-hcloud-19-beta-hydra-dzwx8.alfaview.com, 5.8, a:6 b:7 t:6 g:9 c:3 f:8
[PRIO] alfatraining.alfaview.com, 5.5, a:5 b:5 t:4 g:7 c:2 f:3
[PRIO] apis.alfaview.com, 6.8, a:9 b:10 t:9 g:1 c:4 f:5
[HYP] Alfacheck engineering service exposes internal health/debug endpoints
class: MISCONFIG
asset: alfacheck-engine.alfaview.com
confidence: 55
reasoning: Dedicated host "alfacheck-engine" naming implies media infrastructure control plane. Currently HTTP-unprobed. Hosts in dedicated DNS (not wildcard) — each resolves to a unique IP. If it serves HTTP, a /health, /status, /debug, or /config endpoint could expose internal service topology, build info, or test credentials without auth gate.
evidence_needed: Response to GET on root path; presence of /health, /status, /debug, /config, /admin returning non-SPA content (JSON, plaintext).
verify_steps: GET https://alfacheck-engine.alfaview.com/ — observe HTTP status, Content-Type, body length. GET https://alfacheck-engine.alfaview.com/health and https://alfacheck-engine.alfaview.com/status.
impact: Internal service debug info, version disclosure, API endpoint discovery; severity LOW-MEDIUM.
testability: PASSIVE
[HYP] Alfacheck media services expose upload/download endpoints without auth
class: MISCONFIG
asset: alfacheck-audio.alfaview.com
confidence: 48
reasoning: "alfacheck-audio" naming implies media processing/storage service. If it serves HTTP, media upload or download endpoints (e.g. /upload, /download, /recordings, /media) could lack auth if designed for internal network only. Combined with the similar alfacheck-video host, these may be the media pipeline for alfaview meetings.
evidence_needed: Response to GET on root; probe /upload, /download, /media, /recordings, /files for non-404/non-SPA responses.
verify_steps: GET https://alfacheck-audio.alfaview.com/ — observe status, Content-Type. GET https://alfacheck-audio.alfaview.com/media and https://alfacheck-audio.alfaview.com/recordings.
impact: Exposure of meeting recordings or media files; severity MEDIUM if files accessible.
testability: PASSIVE
[HYP] Beta hydra endpoints expose orchestration or auth service internals
class: MISCONFIG
asset: beta-hcloud-19-beta-hydra-dzwx8.alfaview.com
confidence: 45
reasoning: Three beta hydra subdomains across different cloud providers (hcloud, noris, ovh) all return HTTP 200. "Hydra" naming in auth/identity context (OpenID Connect) or container orchestration suggests an auth proxy or service mesh component. If the 200 response includes a dashboard, config, or API docs, internal service discovery is exposed.
evidence_needed: Content-Type and body of GET on root; presence of /docs, /admin, /config, /metrics, /health endpoints.
verify_steps: GET https://beta-hcloud-19-beta-hydra-dzwx8.alfaview.com/ — capture Content-Type, title, body. GET same host + /docs and /admin.
impact: Internal service discovery, potential auth infrastructure exposure; severity MEDIUM.
testability: PASSIVE
[PARKED] Alfacheck media upload without auth: confidence 48, below threshold. Reasoning relies on naming convention only — "alfacheck" could be a health-check service rather than media pipeline. No evidence of upload functionality. Park until passive probe confirms.
[FINAL] Alfacheck engineering debug exposure: confidence 55. Unprobed dedicated host, naming implies control plane. Passive probe in next step.
[FINAL] Beta hydra orchestration exposure: confidence 45. Three separate cloud provider instances returning 200 — unusual pattern for internal service. Passive probe in next step.
[NEXT] PROBE: GET https://alfacheck-engine.alfaview.com/ — capture status, Content-Type, body. GET https://alfacheck-audio.alfaview.com/ — same. GET https://alfacheck-video.alfaview.com/ — same. GET https://beta-hcloud-19-beta-hydra-dzwx8.alfaview.com/ — same. All 4 probes in sequence, <=1 rps, read-only GET only.
[LEARN] REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only (4396B), /health=204, all common paths 404. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-webclient.alfaview.com: Identical SPA shell to insider, no internal endpoints. Target exhausted.
[LEARN] REJECTED MISCONFIG @ demo-company.alfaview.com: SPA catch-all confirmed — /api/v1/users returns identical HTML shell as root. No unauthenticated data exposure.
[LEARN] REJECTED MISCONFIG @ beta-apis.alfaview.com: Undocumented endpoints (/v2/debug, /v2/admin, /v2/internal, /v2/test, /v2/health) all 404. Surface fully enumerated — only /v2/languages diverges (now resolved).
[LEARN] ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both environments now expose /v2/languages with identical auth enforcement (401).
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI specs identical beta/prod. UUID path params on DELETE /v2/users/{id} and PATCH /v2/rooms/{roomId}/permissions/{userId}. Highest-priority authenticated test target — needs account.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth requires 3-field combo (companyId+roomId+accessKey). Rate-limit status unknown — needs authenticated probe.
[RISK] alfaview: 58/100. All high-value API endpoints are auth-gated (401). No unauthenticated data exposure found on demo-company, beta-apis, insider-webclient, or beta-webclient. Remaining untested surface: 3 alfacheck dedicated hosts (unprobed), 3 beta hydra endpoints (200), alfatraining (200). IDOR at apis.alfaview.com remains highest-impact hypothesis but requires auth to test. Realistic next steps: (1) passive probe alfacheck-* and hydra-* subdomains, (2) obtain demo/test account for authenticated IDOR testing, (3) test guest-link rate limiting. Program's broad scope ("all company-owned infrastructure") is favorable for internal-tool findings, but no confirmed vulnerabilities yet.
## 2026-09-04 05:55:33 UTC [target] (model bigpickle)
[CHANGED] alfacheck-engine.alfaview.com: Was "HTTP unprobed" → now confirmed UNREACHABLE (3 timeout probes: root, /health, /status)
[CHANGED] alfacheck-audio.alfaview.com: Was "HTTP unprobed" → now confirmed UNREACHABLE (3 timeout probes: root, /media, /recordings)
[CHANGED] alfacheck-video.alfaview.com: Was "HTTP unprobed" → now confirmed UNREACHABLE (1 timeout probe: root)
[NEW] beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: HTTP 200 with 9-byte body; trailing slash returns HTTP 400 — minimal surface, likely health-check endpoint
[PRIO] apis.alfaview.com, 6.8, a:9 b:10 t:9 g:1 c:4 f:5
[PRIO] alfatraining.alfaview.com, 5.5, a:5 b:5 t:4 g:7 c:2 f:3
[PRIO] bhc.alfaview.com, 5.0, a:5 b:5 t:3 g:5 c:2 f:3
[PRIO] kh-freiburg.alfaview.com, 5.0, a:5 b:5 t:3 g:5 c:2 f:3
[PRIO] beta-hcloud-19-beta-hydra-dzwx8.alfaview.com, 4.5, a:3 b:5 t:3 g:9 c:3 f:8
[HYP] Cross-tenant IDOR on room permissions and user deletion via UUID path params
class: IDOR
asset: apis.alfaview.com
confidence: 80
reasoning: OpenAPI spec confirms DELETE /v2/users/{id} and PATCH /v2/rooms/{roomId}/permissions/{userId} with UUID path params. Auth uses company-scoped JWT tokens. Server-side authorization (room membership vs global admin scope) unverified. OpenAPI specs are identical between beta and prod — no beta-only escape. Classic cross-tenant IDOR if the API trusts token-issued companyId without validating room-level permission grants. This is the highest-value untested hypothesis in the entire surface.
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
[HYP] Beta hydra endpoints expose auth infrastructure health endpoint
class: MISCONFIG
asset: beta-hcloud-19-beta-hydra-dzwx8.alfaview.com
confidence: 40
reasoning: Three beta hydra subdomains across different cloud providers (hcloud, noris, ovh) all return HTTP 200 with 9-byte body. "Hydra" naming in auth/identity context (OpenID Connect hydra) suggests an auth proxy. The 9-byte response is likely a minimal health check ("alive\n" or similar). Trailing slash returns 400 — indicates a simple HTTP handler, not SPA catch-all. If the endpoint is a health probe for an OAuth/OIDC service, further probing of /oauth2/*, /.well-known/*, or /consent endpoints could expose auth configuration.
evidence_needed: Full response body of GET on root; probe /.well-known/openid-configuration, /oauth2/auth, /consent, /health, /metrics on the same host.
verify_steps: GET https://beta-hcloud-19-beta-hydra-dzwx8.alfaview.com/ — capture exact 9-byte body. GET same host + /.well-known/openid-configuration and /health.
impact: Auth infrastructure exposure, OIDC config leak; severity MEDIUM.
testability: PASSIVE
[PARKED] Alfacheck engineering debug exposure: confidence 55. All 3 alfacheck-* hosts are UNREACHABLE (confirmed timeout). Cannot test; deprioritized.
[PARKED] Alfacheck media upload without auth: confidence 48. Unreachable hosts, no evidence. Deprioritized.
[FINAL] Cross-tenant IDOR: 80. Survives — highest impact, confirmed by OpenAPI spec, needs auth. Rank 1.
[FINAL] Guest link access key enumeration: 65. Survives — confirmed endpoint, distinct error oracle, needs auth. Rank 2.
[FINAL] Beta hydra OIDC exposure: 40. Survives but marginal — 9-byte body limits hypothesis. Worth one passive probe to confirm body content. Rank 3.
[NEXT] PROBE: GET https://beta-hcloud-19-beta-hydra-dzwx8.alfaview.com/ — capture exact 9-byte body, Content-Type. Then GET https://beta-hcloud-19-beta-hydra-dzwx8.alfaview.com/.well-known/openid-configuration and https://beta-hcloud-19-beta-hydra-dzwx8.alfaview.com/health — check for OIDC metadata or health endpoint. Sequential, ≤1 rps, GET-only.
[LEARN] REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: UNREACHABLE — all 3 probes (root, /health, /status) timed out. Host resolves in DNS but does not serve HTTP. Internal-only or firewalled. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: UNREACHABLE — all 3 probes (root, /media, /recordings) timed out. Same as engine. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-video.alfaview.com: UNREACHABLE — root probe timed out. Same pattern. Target exhausted.
[LEARN] ACCEPTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: HTTP 200 with 9-byte body, trailing slash 400 — minimal health-check surface, likely OAuth/OIDC hydra service.
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target. Needs account.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth requires 3-field combo (companyId+roomId+accessKey). Rate-limit status unknown — needs authenticated probe.
[RISK] alfaview: 55/100. All high-value API endpoints are auth-gated (401). No unauthenticated data exposure found. Three alfacheck dedicated hosts confirmed UNREACHABLE (internal/firewalled). Beta hydra endpoints return minimal 9-byte health check — surface near-exhausted. Remaining passive surface: alfatraining (SPA), bhc (SPA), kh-freiburg (SPA), beta hydra (9 bytes). IDOR at apis.alfaview.com remains highest-impact hypothesis but requires auth to test. Realistic next steps: (1) confirm hydra body content and probe OIDC well-known, (2) obtain demo/test account for IDOR and guest-link testing, (3) test guest-link rate limiting. The program's broad scope ("all company-owned infrastructure") is favorable but confirmed vuln count remains zero.
## 2026-09-04 10:04:56 UTC [target] (model bigpickle)
## 2026-09-04 14:24:55 UTC [target] (model bigpickle)
[CHANGED] beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Was ACCEPTED MISCONFIG (health-check surface) → now REJECTED after probing — "Hi Client" body on all paths confirms media/signaling server, not OIDC/auth infrastructure.
[CHANGED] beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — media server, REJECTED.
[CHANGED] beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — media server, REJECTED.
[CHANGED] All 3 alfacheck-* hosts: Confirmed UNREACHABLE via timeout (root, /health, /status, /media, /recordings). Internal/firewalled. REJECTED.
[CHANGED] RISK score dropped from 58 → 55 — all hydra/alfacheck surfaces resolved (rejected). Remaining surface is auth-gated.
[PRIO] apis.alfaview.com, 6.8, a:9 b:10 t:9 g:1 c:4 f:5
[PRIO] alfatraining.alfaview.com, 5.5, a:5 b:5 t:4 g:7 c:2 f:3
[PRIO] bhc.alfaview.com, 5.0, a:5 b:5 t:3 g:5 c:2 f:3
[PRIO] kh-freiburg.alfaview.com, 5.0, a:5 b:5 t:3 g:5 c:2 f:3
[PRIO] beta-app.alfaview.com, 4.5, a:4 b:6 t:5 g:1 c:3 f:3
[PRIO] internal.alfaview.com, 3.5, a:3 b:7 t:4 g:1 c:2 f:3
[HYP] Cross-tenant IDOR on room permissions and user deletion via UUID path params
class: IDOR
asset: apis.alfaview.com
confidence: 80
reasoning: OpenAPI spec confirms DELETE /v2/users/{id} and PATCH /v2/rooms/{roomId}/permissions/{userId} with UUID path params. Auth uses company-scoped JWT tokens. Server-side authorization (room membership vs global admin scope) unverified. OpenAPI specs identical beta/prod — no beta escape. Classic cross-tenant IDOR if the API trusts token-issued companyId without validating room-level permission grants. Highest-value untested hypothesis.
evidence_needed: Authenticated requests with valid token modifying roomId/userId belonging to a different company; 403 (enforced) vs 204 (broken) response.
verify_steps: POST /v2/auth/api-key → obtain token → PATCH /v2/rooms/{victim-roomId}/permissions/{victim-userId} with a foreign userId; observe 403 vs 204. Also DELETE /v2/users/{foreign-userId}.
impact: Cross-tenant permission manipulation or user deletion; severity HIGH (OWASP A01:2021).
testability: AUTH_HELPED
[HYP] Guest link access key enumeration via auth oracle
class: AUTH
asset: apis.alfaview.com/v2/auth/group-link
confidence: 65
reasoning: Guest auth endpoint POST /v2/auth/group-link requires companyId+roomId+accessKey+displayName. Endpoint returns distinct 401 "guest not found" vs 422 errors — oracle for accessKey validity. If accessKeys are low-entropy/sequential and per-scope rate limiting absent, brute-force yields guest session token.
evidence_needed: Rate-limited probe with known companyId+roomId + random accessKeys; 429 after N requests confirming (or not) brute-force feasibility.
verify_steps: POST /v2/auth/group-link with companyId=<known>, roomId=<known>, accessKey=<random-12-char>, displayName="test" — measure 401 response time. Repeat 10x at 1 rps; detect 429 or timing delta.
impact: Guest session impersonation → private meeting room access; severity MEDIUM-HIGH.
testability: AUTH_HELPED
[HYP] Multi-tenant SaaS tenant name injection via SPA client-side logic
class: XSS
asset: alfatraining.alfaview.com / bhc.alfaview.com / kh-freiburg.alfaview.com
confidence: 35
reasoning: Three multi-tenant subdomains (alfatraining, bhc, kh-freiburg) all serve HTTP 200 SPA shells. Multi-tenant SaaS applications often render tenant-specific branding/names in the SPA shell or via API. If tenant names are reflected without sanitization in HTML/JS context, stored or reflected XSS is possible. However, no evidence of unauthenticated API surface or reflected parameters yet — pure structural inference.
evidence_needed: Capture full SPA HTML body; inspect for tenant-specific rendering, inline JSON payloads, or API calls with tenant identifiers in the URL path.
verify_steps: GET https://alfatraining.alfaview.com/ — capture full HTML body. Compare response sizes and content between alfatraining, bhc, and kh-freiburg. Check for inline JSON, script tags with tenant data, or API calls.
impact: Cross-site scripting → session theft → potential ATO; severity MEDIUM-HIGH.
testability: PASSIVE
[NEXT] PROBE: GET https://alfatraining.alfaview.com/ — capture full HTML body (Content-Length, inline scripts, <script src> URLs, any tenant-specific data in HTML or JSON). Then GET https://bhc.alfaview.com/ and https://kh-freiburg.alfaview.com/ — compare response sizes and content. Check for:
[LEARN] REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Hydra hosts are media/signaling servers — "Hi Client" body on all paths, not OIDC/auth infrastructure. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — media server, "Hi Client" response. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — media server, "Hi Client" response. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: UNREACHABLE — all 3 probes (root, /health, /status) timed out. Internal/firewalled. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: UNREACHABLE — all 3 probes (root, /media, /recordings) timed out. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-video.alfaview.com: UNREACHABLE — root probe timed out. Target exhausted.
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target. Needs account.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth requires 4-field combo (companyId+roomId+accessKey+displayName). Rate-limit status unknown — needs probe.
[LEARN] ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both environments now expose /v2/languages with identical auth enforcement (401).
[LEARN] REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
[LEARN] REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only, /health=204, all common paths 404. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-webclient.alfaview.com: Identical SPA shell to insider, no internal endpoints. Target exhausted.
[LEARN] REJECTED MISCONFIG @ demo-company.alfaview.com: SPA catch-all confirmed — /api/v1/users returns identical HTML shell as root. No unauthenticated data exposure.
[LEARN] ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
[LEARN] ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.
[LEARN] ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[RISK] alfaview: 52/100. Surface is largely exhausted for unauthenticated testing. All high-value API endpoints are auth-gated (401). Confirmed findings: zero. Eliminated surfaces: beta hydra (media servers, not auth), alfacheck-* (unreachable/firewalled), demo-company/insider/beta-webclient (SPA catch-all, no data exposure), beta-apis (auth identical to prod, version drift resolved). Remaining unauthenticated surface: 3 multi-tenant SPA shells (alfatraining, bhc, kh-freiburg) — low probability of findings without JavaScript analysis. Highest-impact hypothesis (IDOR on apis.alfaview.com, confidence 80) is blocked by auth requirement. Program's broad scope ("all company-owned infrastructure") is favorable but productive unauthenticated attack path is not visible. Critical next step: obtain test account to unlock IDOR and guest-link testing. Without auth, the remaining surface is SPA shells with low expected value.
[CHANGED] beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: ACCEPTED MISCONFIG → REJECTED — media/signaling server ("Hi Client"), not OIDC.
[CHANGED] beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — REJECTED.
[CHANGED] beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — REJECTED.
[CHANGED] alfacheck-engine.alfaview.com: UNREACHABLE confirmed (3 timeout probes).
[CHANGED] alfacheck-audio.alfaview.com: UNREACHABLE confirmed (3 timeout probes).
[CHANGED] alfacheck-video.alfaview.com: UNREACHABLE confirmed (1 timeout probe).
[PRIO] apis.alfaview.com, 6.8, a:9 b:10 t:9 g:1 c:4 f:5
[PRIO] alfatraining.alfaview.com, 5.5, a:5 b:5 t:4 g:7 c:2 f:3
[PRIO] bhc.alfaview.com, 5.0, a:5 b:5 t:3 g:5 c:2 f:3
[PRIO] kh-freiburg.alfaview.com, 5.0, a:5 b:5 t:3 g:5 c:2 f:3
[PRIO] beta-app.alfaview.com, 4.5, a:4 b:6 t:5 g:1 c:3 f:3
[PRIO] internal.alfaview.com, 3.5, a:3 b:7 t:4 g:1 c:2 f:3
[HYP] Cross-tenant IDOR on room permissions and user deletion via UUID path params
class: IDOR
asset: apis.alfaview.com
confidence: 80
reasoning: OpenAPI spec confirms DELETE /v2/users/{id} and PATCH /v2/rooms/{roomId}/permissions/{userId} with UUID path params. Auth uses company-scoped JWT tokens. Server-side authorization (room membership vs global admin scope) unverified. OpenAPI specs identical beta/prod — no beta escape. Classic cross-tenant IDOR if the API trusts token-issued companyId without validating room-level permission grants. Highest-value untested hypothesis.
evidence_needed: Authenticated requests with valid token modifying roomId/userId belonging to a different company; 403 (enforced) vs 204 (broken) response.
verify_steps: POST /v2/auth/api-key → obtain token → PATCH /v2/rooms/{victim-roomId}/permissions/{victim-userId} with a foreign userId; observe 403 vs 204. Also DELETE /v2/users/{foreign-userId}.
impact: Cross-tenant permission manipulation or user deletion; severity HIGH (OWASP A01:2021).
testability: AUTH_HELPED
[HYP] Guest link access key enumeration via auth oracle
class: AUTH
asset: apis.alfaview.com/v2/auth/group-link
confidence: 65
reasoning: Guest auth endpoint POST /v2/auth/group-link requires companyId+roomId+accessKey+displayName. Endpoint returns distinct 401 "guest not found" vs 422 errors — oracle for accessKey validity. If accessKeys are low-entropy/sequential and per-scope rate limiting absent, brute-force yields guest session token.
evidence_needed: Rate-limited probe with known companyId+roomId + random accessKeys; 429 after N requests confirming (or not) brute-force feasibility.
verify_steps: POST /v2/auth/group-link with companyId=<known>, roomId=<known>, accessKey=<random-12-char>, displayName="test" — measure 401 response time. Repeat 10x at 1 rps; detect 429 or timing delta.
impact: Guest session impersonation → private meeting room access; severity MEDIUM-HIGH.
testability: AUTH_HELPED
[HYP] Multi-tenant SPA tenant name injection via client-side logic
class: XSS
asset: alfatraining.alfaview.com / bhc.alfaview.com / kh-freiburg.alfaview.com
confidence: 35
reasoning: Three multi-tenant subdomains all serve HTTP 200 SPA shells. Multi-tenant SaaS applications often render tenant-specific branding/names in the SPA shell or via API. If tenant names are reflected without sanitization in HTML/JS context, stored or reflected XSS is possible. However, no evidence of unauthenticated API surface or reflected parameters yet — pure structural inference.
evidence_needed: Capture full SPA HTML body; inspect for tenant-specific rendering, inline JSON payloads, or API calls with tenant identifiers in the URL path.
verify_steps: GET https://alfatraining.alfaview.com/ — capture full HTML body. Compare response sizes and content between alfatraining, bhc, and kh-freiburg. Check for inline JSON, script tags with tenant data, or API calls.
impact: Cross-site scripting → session theft → potential ATO; severity MEDIUM-HIGH.
testability: PASSIVE
[PARKED] Multi-tenant SPA tenant name injection: confidence 35 (<40 threshold). No evidence of reflected parameters or tenant-specific rendering. SPA catch-all routing confirmed on demo-company. Would need JavaScript bundle analysis to validate — separate probe required.
[FINAL] Cross-tenant IDOR: 80. Survives — Rank 1. Strongest structural evidence, highest impact, needs auth.
[FINAL] Guest link access key enumeration: 65. Survives — Rank 2. Confirmed endpoint, distinct error oracle, needs auth probe.
[NEXT] PROBE: GET https://alfatraining.alfaview.com/ — capture full HTML body (Content-Length, inline scripts, <script src> URLs, any tenant-specific data). Then GET https://bhc.alfaview.com/ and https://kh-freiburg.alfaview.com/ — compare response sizes and content. Check for: (1) inline <script> tags with config objects or API endpoints, (2) JavaScript bundle URLs, (3) any tenant/company names rendered in HTML, (4) differences between the 3 SPA shells. Sequential, ≤1 rps, GET-only.
[LEARN] REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Media/signaling server ("Hi Client"), not OIDC/auth infrastructure. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — media server. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — media server. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: UNREACHABLE — all 3 probes timed out. Internal/firewalled. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: UNREACHABLE — all 3 probes timed out. Target exhausted.
[LEARN] REJECTED MISCONFIG @ alfacheck-video.alfaview.com: UNREACHABLE — root probe timed out. Target exhausted.
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target. Needs account.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth requires 4-field combo (companyId+roomId+accessKey+displayName). Rate-limit status unknown — needs probe.
[LEARN] ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both environments now expose /v2/languages with identical auth enforcement (401).
[LEARN] REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
[LEARN] REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only, /health=204, all common paths 404. Target exhausted.
[LEARN] REJECTED MISCONFIG @ beta-webclient.alfaview.com: Identical SPA shell to insider, no internal endpoints. Target exhausted.
[LEARN] REJECTED MISCONFIG @ demo-company.alfaview.com: SPA catch-all confirmed — /api/v1/users returns identical HTML shell as root. No unauthenticated data exposure.
[LEARN] ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
[LEARN] ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.
[LEARN] ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[LEARN] ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
[RISK] alfaview: 52/100. Surface is largely exhausted for unauthenticated testing. All high-value API endpoints are auth-gated (401). Confirmed findings: zero. Eliminated surfaces: beta hydra (media servers), alfacheck-* (unreachable/firewalled), demo-company/insider/beta-webclient (SPA catch-all), beta-apis (auth identical to prod, version drift resolved). Remaining unauthenticated surface: 3 multi-tenant SPA shells (alfatraining, bhc, kh-freiburg) — low probability without JS analysis. Highest-impact hypothesis (IDOR, confidence 80) blocked by auth. Critical next step: obtain test account to unlock IDOR and guest-link testing.
## 2026-09-04 17:45:13 UTC [target] (model bigpickle)
[LEARN] REJECTED XSS @ alfatraining/bhc/kh-freiburg.alfaview.com: All three multi-tenant hosts serve byte-identical generic alfaview.com SPA shell (1381B, MD5 554a39...). No tenant-specific rendering, no inline data, no reflections. Target exhausted.
[LEARN] ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms UUID path params on DELETE /v2/users/{id} and PATCH /v2/rooms/{roomId}/permissions/{userId} — highest-priority authenticated target. Needs account.
[LEARN] ACCEPTED AUTH @ apis.alfaview.com: Guest link auth requires 4-field combo (companyId+roomId+accessKey+displayName). Rate-limit status unknown — needs authenticated probe.
[RISK] alfaview: 45/100. All unauthenticated surfaces now exhausted: multi-tenant SPA shells (alfatraining/bhc/kh-freiburg) byte-identical to generic marketing shell; hydra hosts = media servers; alfacheck-* = unreachable; demo-company/insider/beta-webclient = SPA catch-all; beta-apis = auth identical to prod. Zero findings. The two surviving high-value hypotheses (IDOR conf 80, guest-link conf 65) both require an authenticated account. Without one, no further progress is possible via passive testing.
## 2026-09-04 20:01:52 UTC [target] (model bigpickle)
## 2026-09-04 22:04:47 UTC [target] (model bigpickle)
## 2026-09-05 00:10:38 UTC [target] (model bigpickle)
[PRIO] sso.alfaview.com, 7.3, a:7 b:8 t:9 g:7 c:1 f:10
[PRIO] alfaview.com, 6.4, a:6 b:8 t:6 g:8 c:1 f:7
[PRIO] apis.alfaview.com, 6.2, a:8 b:8 t:7 g:2 c:2 f:6
[HYP] OAuth redirect_uri validation bypass on FusionAuth authorize endpoint → code theft
class: OATH
asset: sso.alfaview.com/oauth2/authorize
confidence: 65
reasoning: OIDC discovery confirms /oauth2/authorize with response_type code, response_modes query/form_post/fragment, frontchannel logout on. Unknown client_id yields invalid_client, proving registration gate precedes redirect_uri validation. FusionAuth's standard redirect_uri handling = exact match (with registered_redirect_uris), but bypasses exist (e.g., path/query confusion per registered URI, fragment-in-uri, client-credentials flow with leaked secret). Cannot test without a valid client_id.
evidence_needed: 302 Location to attacker URI after authorize with a registered client_id + unregistered redirect_uri; or attacker-controlled code/state issuance.
verify_steps: harvest valid client_id from desktop/web-client public bundle → GET /oauth2/authorize?client_id=<real>&redirect_uri=https://evil.example/&response_type=code&scope=openid → expect 302 to login page (valid_client) then observe 302 redirect_uri check → compare with exact-match registered URI variant.
impact: OAuth authorization-code theft → account takeover of any alfaview user; severity HIGH.
testability: AUTH_HELPED
[HYP] FusionAuth default tenant issuer drift on production IAM
class: MISCONFIG
asset: sso.alfaview.com/.well-known/openid-configuration
confidence: 90
reasoning: issuer field literally "acme.com"; JWKS X.509 CN=acme.com self-signed 2021-2023/31; stock FusionAuth landing page. Production SSO was never re-issued/branded — default tenant metadata live. Combination with id_token_signing HS256 support and public auth endpoints = drift signal, exploitable only if any relying party does weak iss/alg validation.
evidence_needed: confirmation that this is the live production issuer used by app/desktop logins (present it as config drift; low-severity finding).
verify_steps: GET /oauth2/authorize with any registered client_id shows login; compare issuer value against desktop-client-validated tokens (HUMAN). Passive evidence already conclusive for the drift claim.
impact: trust ambiguity for any SSO consumer doing loose iss verification; indicates hardening gap; severity LOW.
testability: PASSIVE
[HYP] Device authorization flow abuse via public client
class: AUTH
asset: sso.alfaview.com/oauth2/device_authorize
confidence: 45
reasoning: discovery advertises device_authorization_endpoint; grant device_code enabled; GET=405 POST-only (endpoint live). If alfaview ships a public (non-secret) native client_id (mobile/desktop), attacker can initiate device flow, phish the user_code approval page, then redeem victim's authorization → session/account hijack.
evidence_needed: a registered public client_id (no secret) that device_authorize accepts.
verify_steps: POST device_authorize with harvested public client_id; if device/user codes returned without client_secret, phishing-to-account-takeover path confirmed. Requires client_id harvest first.
impact: user session/account takeover via device-code phishing; severity HIGH if exploitable.
testability: AUTH_HELPED
[PARKED] Device-flow abuse (45): below momentum — needs a public client_id, phishing precondition, POST-only; parked pending client_id harvest.
[PARKED] JWT alg-confusion on apis gateway: REJECTED this session — token oracle proves opaque/base64 access tokens (not raw JWTs); crafted HS256-JWT (RS256-modulus-as-HMAC) rejected at base64-decode. Reframable to SSO id_token consumers but not passively testable.
[FINAL] redirect_uri bypass: 65. Survives — rank 1, now grounded on confirmed FusionAuth + exact `invalid_client` gate. Blocked on client_id harvest.
[FINAL] issuer=acme.com drift: 90. Survives — direct read-only evidence, low severity baselined for reporting.
[NEXT] PROBE: GET `https://sso.alfaview.com/oauth2/register` (checks open tenant self-registration on prod IAM — if live, also maps default-registration surface); follow-up HUMAN: extract registered client_id from desktop client's public config/bundle to unlock redirect_uri + device-flow testing on `/oauth2/authorize`.
[LEARN] ACCEPTED MISCONFIG @ sso.alfaview.com: Production IAM is FusionAuth with default tenant issuer "acme.com" (OIDC `issuer` + JWK CN=acme.com, self-signed 2021-12-03+10y); /api/status 200 `{"status":"Ok"}` unauthenticated; device endpoint live (405 GET).
[LEARN] ACCEPTED OATH @ sso.alfaview.com: /oauth2/authorize enforces client_id registration before any redirect_uri handling (`invalid_client`, reason `invalid_client_id`) — redirect_uri testing requires a registered client_id.
[LEARN] REJECTED AUTH @ apis.alfaview.com: Access tokens are opaque/base64 (distinct 401 "No base64 encoded access token was provided in the Authorization header."), not raw JWTs — crafted-HS256-JWT with JWKS public key as HMAC secret rejected at decode; JWT alg-confusion against API gateway closed.
[LEARN] ACCEPTED MISCONFIG @ alfaview.com: root now 301→/en (nginx, Accept-Language vary), /en 177KB marketing page with strict CSP and matomo; no unauthenticated SSO login links present on marketing domain.
[RISK] alfaview: 48/100. Newly exposed FusionAuth IAM is the highest-value remaining surface (identity = crown jewels) with concrete drift (issuer acme.com) and live OAuth/device endpoints, but every exploit path (redirect_uri, device-flow, api-key IDOR) still resolves to a valid client_id or account — auth-gated. CLI-side JWT/alg avenues closed by the opaque-token oracle. Risk rises if a public client_id or test account is obtained; until then, passive yield is near exhausted.
## 2026-09-05 04:33:59 UTC [target] (model bigpickle)
[HYP] Authz-gap on guest-link/company GraphQL ops after free-tenant self-signup
class: IDOR
asset: app.alfaview.com/graphql
confidence: 70
reasoning: Bundle proves designed free-tier signup (`signup(displayName,user,agreedToTerms,agreedToPrivacyPolicy,planId)` mutation, `# businessLogicService` SDL) and cross-tenant-shaped ops (`FetchUserPermissions($userId)`, `listCompanyLinks`, `FetchGuestInfo($userId,$companyId,$roomId)`). GraphQL reachable unauthenticated (__typename=Query); introspection off so schema/field enforcement must be tested via known op names. A created low-privilege tenant is the standard IDOR baseline for the confirmed DELETE /v2/users/{id} REST pattern.
evidence_needed: 2nd tenant's userId/roomId accessible or their permission set readable from the 1st tenant's session token.
verify_steps: (AUTH) signup own tenant → CredentialsAuthenticate → Authorization bearer → run FetchUserPermissions against victim-tenant userId; GET apis.alfaview.com/v2/users/{id} with same bearer; observe cross-tenant 200 vs 403.
impact: cross-tenant user/room permission read and user deletion — PII exposure, account takeover; severity HIGH.
testability: AUTH_HELPED
[HYP] OAuth register/authorize client_id harvest from desktop bundle unlocks redirect_uri + device-flow
class: AUTH
asset: sso.alfaview.com/oauth2/authorize
confidence: 60
reasoning: `invalid_client` gate reconfirmed; `oauth2/register` live requires redirect_uri. Web bundle carries no client_id (`clientId` absent), so desktop client (S3 `stable/{mac,win,linux}/`) is the only public source. No versioned file names leaked — filename discovery needed before download.
evidence_needed: real client_id + its registered redirect_uri from desktop client resources; then 302-to-attacker on mismatched redirect_uri.
verify_steps: obtain installer → strings/extract config for client_id/issuer → GET /oauth2/authorize?...&redirect_uri=https://evil.example/ → compare 302 target vs exact-match registered URI.
impact: authorization-code theft → ATO of any alfaview user; severity HIGH.
testability: AUTH_HELPED
[HYP] Magic-token / guest-link bearer strength in unauthenticated join flow
class: AUTH
asset: app.alfaview.com/graphql
confidence: 45
reasoning: `MagicTokenAuthenticate($magicToken)` and `GuestJoin(userId,companyId,roomId,dialIn...)` exist; guest join is designed-unauthenticated with 4-field accessKey combo. If accessKey/guestId sequence space is small/guessable or magic tokens replay, unauthorized room entry follows.
evidence_needed: create guest link in own tenant; observe accessKey entropy + whether room-scoped; test cross-tenant roomId with wrong accessKey (rejects) vs correct (accepts).
verify_steps: (AUTH) GenerateGroupLink in own tenant → inspect accessKey format/entropy → attempt GuestJoin against a second tenant's roomId without their accessKey.
impact: unauthorized room access / replay across tenants; severity MEDIUM-HIGH if guessable.
testability: AUTH_HELPED
[NEXT] HUMAN: Create one throwaway free alfaview company via the documented self-service flow ("Create your free alfaview company now" on app.alfaview.com login page) or supply a test account — this single action unlocks all three live high-value hypotheses (REST IDOR id 80, GraphQL authz-gap id 70, guest-link testing id 45) that are otherwise permanently gated. Then run: GET apis.alfaview.com/v2/users/{ownUserId} with the issued opaque bearer as the IDOR baseline.
[RISK] alfaview: 55/100. New, previously-unmapped high-value surface exposed: production Apollo GraphQL on app.alfaview.com with a feature-complete guest-link/company admin schema and a designed self-service free-tenant path that would unlock the top IDOR hypotheses. Offsetting positives: introspection disabled, CSRF-preflight enforced, FusionAuth `/oauth2/register` still client-gated, desktop download bucket not enumerable. Every remaining exploit path still resolves to a created account or a desktop-only client_id; passive yield is exhausted and risk is now conditional on the signup unblock, which needs human/decision authorization to mutate live infrastructure.
## 2026-09-05 08:39:12 UTC [target] (model bigpickle)
[NEW] app.alfaview.com/js/app.min.5b3949112f0cf682adc8.js (1.09MB) leaks FULL admin/company GraphQL schema: 60+ mutations + 45+ queries with exact args. Not just SDL strings — includes CreateCompany($companyName,$user,$planId), CredentialsAuthenticate($username,$password,$companyId), adminSwitchCompany, assignGrantCompanyAdminPermission, FetchUserPermissions, generateFileDownloadURL, getPasscode, listRoomLinks(accessKey,guestId), searchCompanies, listUsersByIds, listAllUserIds, GetPendingUserAccount, CreateMagicToken, MagicTokenAuthenticate, guestAuthenticate, guestJoin.
[NEW] GraphQL live at app.alfaview.com/graphql: __typename OK unauth; introspection disabled; sensitive ops return UNAUTHENTICATED. Resolver auth is INCONSISTENT: listIdentityProviders returns data unauth (empty), listComponents executes resolver unauth (INTERNAL_SERVER_ERROR), **guestAuthenticate + guestJoin mutations return BAD_USER_INPUT (NOT UNAUTHENTICATED)** — both reachable without any access token.
[NEW] CRITICAL DELTA: GraphQL guest path guestAuthenticate(userId,companyId,roomId) and guestJoin(userId,companyId,roomId,displayName) take NO accessKey — while REST guest-link flow requires 4-field combo incl. accessKey. GraphQL guest auth may bypass the designed secret check if guest records resolve by ID tuple only.
[NEW] Hosts: webclient.alfaview.com=200/4396B(insider family); staging-webclient.alfaview.com=401 Basic realm=Protected (/health=204); plausible.alfaview.com=204; production-alfaview-assets/staging-alfaview-assets stable+beta buckets=403 (not enumerable); webviewer.dev.alfaview.com=timeout; app.alfaview.com/web/join=302->/.
[LEARN] ACCEPTED MISCONFIG @ app.alfaview.com: Full GraphQL admin schema in public bundle; per-resolver auth (listIdentityProviders unauth data, listComponents unauth 500).
[LEARN] ACCEPTED AUTH @ app.alfaview.com/graphql: guestAuthenticate/guestJoin unauthenticated-reachable (BAD_USER_INPUT, not UNAUTHENTICATED); no accessKey in GraphQL guest signature — diverges from REST accessKey combo.
[RISK] alfaview: 58/100. GraphQL surface now fully mapped passively with exact op names for the self-signup→IDOR chain; unauth guest mutations identified as the highest-value AUTH_HELPED target. Everything else auth-gated. Next blocker remains: valid IDs/token (create throwaway free tenant) to test guestAuthenticate ID-tuple authz and cross-tenant IDOR.
[HYP] Unauthenticated guest-token issuance via GraphQL ID-tuple guest path (accessKey omitted)
class: AUTH
asset: app.alfaview.com/graphql (guestAuthenticate/guestJoin)
confidence: 62
reasoning: Bundled ops `guestAuthenticate(userId,companyId,roomId)` and `guestJoin(userId,companyId,roomId,displayName)` return accessToken/joinLink with NO accessKey arg, while REST guest-link auth mandates the 4-field accessKey combo. Both mutations are unauthenticated-reachable (error code BAD_USER_INPUT, NOT UNAUTHENTICATED). If guest records resolve by ID tuple alone, any party that learns a (userId,companyId,roomId) triple — e.g. from a shared/leaked invite link where the secret is a separate accessKey field attendees must paste — could mint a session without the secret.
evidence_needed: A real guest triple; observe guestAuthenticate returning accessToken with wrong/absent accessKey (vs REST rejecting same triple without accessKey).
verify_steps: (AUTH) create own tenant → GenerateGroupLink → take returned userId/companyId/roomId → call mutation guestAuthenticate (NO accessKey) vs POST REST /v2/auth/guest-link (same triple, no accessKey) → compare 200+accessToken vs 401. Then cross-tenant roomId variant.
impact: unauthorized room entry / guest-token mint without accessKey — MEDIUM-HIGH; if guest IDs predictable, enumeration up to session hijack.
testability: AUTH_HELPED
[HYP] OAuth redirect_uri validation bypass on FusionAuth authorize → authorization-code theft
class: OATH
asset: sso.alfaview.com/oauth2/authorize
confidence: 65
reasoning: FusionAuth 1.63.0 confirmed; `invalid_client` gate precedes redirect_uri handling; standard FusionAuth redirect_uri = exact-match registry, but path/query substitution bugs exist. Desktop client (S3 buckets) is the only client_id source; buckets 403 so harvest requires installer URL discovery.
evidence_needed: registered client_id + its USP; then 302-to-attacker on mutated URI.
verify_steps: harvest client_id → GET /oauth2/authorize?client_id&response_type=code&redirect_uri=<registered-uri>/../attacker|?extra= → compare 302 Location vs exact-match.
impact: ATO of any alfaview user; HIGH.
testability: AUTH_HELPED
[HYP] Cross-tenant IDOR on GraphQL/REST user+room ops after free-tenant signup
class: IDOR
asset: apis.alfaview.com/v2 + app.alfaview.com/graphql
confidence: 80
reasoning: OpenAPI UUID path params (DELETE /v2/users/{id}, PATCH /v2/rooms/{roomId}/permissions/{userId}); bundle exposes matching GraphQL ops (fetchUserPermissions, generateFileDownloadURL, getPasscode, listRoomLinks, listUsersByIds, searchCompanies) — schema is the same multi-tenant admin surface. Company-scoped tokens; cross-tenant authz unverified. All data resolvers auth-gated (UNAUTHENTICATED) so a low-privilege token is the test baseline.
evidence_needed: token of tenant A reads/alters tenant B's userId/roomId → 200 vs 403/204.
verify_steps: (AUTH) signup free tenant → credentialsAuthenticate → bearer → GET /v2/users/{victimId} + query fetchUserPermissions(victimId) → observe cross-tenant 200 vs 403.
impact: cross-tenant PII read, file download, permission/account mod, user delete; HIGH.
testability: AUTH_HELPED
[NEXT] PROBE: POST `https://app.alfaview.com/graphql` with `{"query":"mutation{guestAuthenticate(userId:\"<own-tenant-guestId>\",companyId:\"<own-companyId>\",roomId:\"<own-roomId>\"){accessToken expiry}}"}` — repeat with the identical triple on REST `POST https://apis.alfaview.com/v2/auth/guest-link` (with and without accessKey); the 200+accessToken-vs-401 delta is the one-shot proof of the accessKey-bypass. Requires own-tenant guest triple: obtain via `[NEXT] HUMAN` — create one throwaway free alfaview company via the self-service signup ("Create your free alfaview company now" on app.alfaview.com login / `createCompany` mutation), which also unlocks the rank-1 IDOR test suite (GET /v2/users/{ownUserId} baseline, then fetchUserPermissions with a victim ID).
[RISK] alfaview: 58/100. Passive yield re-expanded this session: the public bundle exposes the entire multi-tenant admin GraphQL schema and proves unauthenticated guest mutations exist with an accessKey-less signature — a concrete, testable Authz divergence from REST, plus the still-unchallenged cross-tenant IDOR pattern (conf 80) and FusionAuth redirect_uri surface (65). Risk is tempered by per-resolver auth gating on all PII ops and the fact that every exploit still resolves to obtaining a valid test tenant (HUMAN) or the desktop-only client_id; passive avenues are now essentially exhausted.
## 2026-09-05 12:06:17 UTC [target] (model bigpickle)
[HYP] Cross-tenant IDOR on REST user/room/permission ops via UUID path params
class: IDOR
asset: apis.alfaview.com/v2
confidence: 80
reasoning: Live OpenAPI (this session) confirms `DELETE /v2/users/{id}`, `PATCH|DELETE /v2/rooms/{roomId}/permissions/{userId}`, plus read-heavy `GET /v2/rooms/{roomId}/passcode` (returns passcode string), `GET /v2/stats`, `GET /v2/users` — all token-authed, company-scoped, cross-tenant authz unverified. `GET /v2/users/me` unauth → 401 opaque-token gate reconfirmed. Same schema exposed by GraphQL resolvers (fetchUserPermissions/generateFileDownloadURL/getPasscode) which are auth-gated (verified this session).
evidence_needed: token of tenant A returns 200/204 for tenant B's userId/roomId where 403 is expected (esp. passcode + permissions + stats).
verify_steps: (AUTH) signup free tenant → POST /v2/auth/password `{"username","password"}` (or api-key) → keep opaque base64 bearer → GET /v2/users/me baseline 200 → GET /v2/rooms/{victimUUID}/passcode + PATCH /v2/rooms/{victimRoomId}/permissions/{victimUserId} → 403 vs 200/204 verdict.
impact: cross-tenant PII read, room passcode/recording access, permission/account mutation, user deletion; HIGH.
testability: AUTH_HELPED
[HYP] Guest-authz gap: expired/disabled or cross-tenant guest triples still authenticate via GraphQL guest path
class: AUTH
asset: app.alfaview.com/graphql (guestAuthenticate/guestJoin)
confidence: 55
reasoning: guestAuthenticate(guestId,companyId,roomId) is anonymous-executing (previous session: BAD_USER_INPUT not UNAUTHENTICATED; mutation-over-GET now verified blocked, so classification stands from POST). Database lookup is by ID triple only (no secret), per confirmed op signature. Two distinct flaw candidates survive design analysis: (1) lifecycle — revoked/expired guestIDs still mint tokens; (2) cross-tenant — companyId/roomId from tenant B accepted with tenant A triple (tuple splicing).
evidence_needed: with own tenant guest triple, expired/re-keyed link still returns accessToken; BAD_USER_INPUT→rejects wrong-pair (already seen) vs cross-pair accepts.
verify_steps: (AUTH) GenerateGroupLink/guest-link in own tenant → guestAuthenticate with own triple (token issued?) → delete/expire link → repeat (still token?) → splice roomId of second tenant → compare BAD_USER_INPUT vs accessToken.
impact: unauthorized room entry by dead/re-keyed or cross-tenant links; MEDIUM-HIGH.
testability: AUTH_HELPED
[HYP] OAuth redirect_uri validation bypass → authorization-code theft
class: OATH
asset: sso.alfaview.com/oauth2/authorize
confidence: 65
reasoning: FusionAuth 1.63.0 OIDC source; `invalid_client` (reason `invalid_client_id`) precedes redirect_uri handling, so bypass variants (path/query template confusion, fragment carriage) only testable with a registered client_id; web bundle carries no client_id (verified prior sessions); desktop installer S3 (production-alfaview-assets) is the remaining source, buckets return 403 (not enumerable).
evidence_needed: registered client_id + its exact registered redirect_uri, then 302 Location on `...&redirect_uri=<registered>/../evil` vs exact-match.
verify_steps: harvest client_id from desktop installer resources → GET /oauth2/authorize?client_id&response_type=code&redirect_uri=<reg-uri>/../evil → compare 302 target.
impact: code theft → ATO of any alfaview user; HIGH.
testability: AUTH_HELPED
[NEXT] HUMAN: Create one throwaway free alfaview company via the documented self-service flow ("Create your free alfaview company now" on app.alfaview.com login page) — unlock rank-1 (REST IDOR) and the guest-authz splice test. Baseline on acceptance: POST https://apis.alfaview.com/v2/auth/password {"username","password"} → store opaque bearer → GET https://apis.alfaview.com/v2/users/me (expect 200, own user/company IDs) → then GET https://apis.alfaview.com/v2/rooms/{victimRoomId}/passcode (expect 403 = enforced vs 200 = IDOR). Anything below that signup resolves all remaining live hypotheses (also unblocks POSTing GET-blocked GraphQL mutations magicTokenAuthenticate/guestAuthenticate).
[RISK] alfaview: 48/100. Anonymous GraphQL surface now fully mapped and largely closed (sensitive resolvers verified auth-gated; only empty listIdentityProviders and crashing listComponents are anonymously reachable). Remaining exposure concentrates on the two auth-gated chains — REST cross-tenant IDOR (80) and guest-authz splicing (55) — plus the plausible FusionAuth redirect_uri surface (65) that only a valid client_id unlocks. No live bug is yet proven; every remaining path requires either a self-service tenant (HUMAN) or a harvested desktop client_id, so probability-weighted risk is pulled down by those gates while upside stays constant if either resolves.
## 2026-09-05 14:44:19 UTC [target] (model bigpickle)
[PRIO] sso.alfaview.com,7.6,a:7 b:9 t:9(OAuth/FusionAuth/OIDC) g:6 c:2 f:10
[PRIO] app.alfaview.com/graphql,7.4,a:7 b:9 t:9(GraphQL schema+guest ops) g:3 c:2 f:10
[PRIO] apis.alfaview.com,7.3,a:9 b:9 t:8(OpenAPI+UUID-paths) g:2 c:2 f:6
[PRIO] test.alfaview.com,5.8,a:4 b:6 t:6(binary dist) g:9 c:2 f:10
[PRIO] alfaview.com,5.2,a:4 b:6 t:5(marketing) g:9 c:2 f:10
[HYP] Cross-tenant IDOR on REST user/room/permission ops via UUID path params
class: IDOR
asset: apis.alfaview.com/v2
confidence: 80
reasoning: OpenAPI confirms UUID path params on destructive ops (`DELETE /v2/users/{id}`, `PATCH|DELETE /v2/rooms/{roomId}/permissions/{userId}`, `GET /v2/rooms/{roomId}/passcode`). Auth uses company-scoped opaque tokens; cross-tenant authz unverified. High-confidence structural finding backed by spec.
evidence_needed: token of tenant A returns 200/204 for tenant B's userId/roomId where 403 is expected (esp. passcode + permissions + stats).
verify_steps: (AUTH) signup free tenant → POST /v2/auth/password {"username","password"} → opaque bearer → GET /v2/users/me baseline → GET /v2/rooms/{victimUUID}/passcode → 403 vs 200 verdict.
impact: cross-tenant PII read, room passcode/recording access, permission/account mutation, user deletion; HIGH.
testability: AUTH_HELPED
[HYP] OAuth redirect_uri validation bypass on FusionAuth authorize → authorization-code theft
class: OATH
asset: sso.alfaview.com/oauth2/authorize
confidence: 65
reasoning: FusionAuth 1.63.0 confirmed; `invalid_client` gate precedes redirect_uri handling; standard FusionAuth redirect_uri = exact-match registry but path/query substitution bugs exist. Desktop client (S3 buckets) is the only client_id source; buckets 403 so harvest requires installer URL discovery.
evidence_needed: registered client_id + its exact registered redirect_uri, then 302 Location on `...&redirect_uri=<registered>/../evil` vs exact-match.
verify_steps: harvest client_id from desktop installer resources → GET /oauth2/authorize?client_id&response_type=code&redirect_uri=<reg-uri>/../evil → compare 302 target.
impact: code theft → ATO of any alfaview user; HIGH.
testability: AUTH_HELPED
[HYP] Guest-authz gap: expired/disabled or cross-tenant guest triples still authenticate via GraphQL guest path
class: AUTH
asset: app.alfaview.com/graphql (guestAuthenticate/guestJoin)
confidence: 55
reasoning: `guestAuthenticate(userId,companyId,roomId)` is anonymous-executing (BAD_USER_INPUT not UNAUTHENTICATED; GET blocked). DB lookup by ID triple only (no secret). Two flaw candidates: (1) revoked/expired guestIDs still mint tokens; (2) companyId/roomId from tenant B accepted with tenant A triple (tuple splicing).
evidence_needed: own tenant guest triple: expired/re-keyed link still returns accessToken; cross-pair accepts vs BAD_USER_INPUT.
verify_steps: (AUTH) GenerateGroupLink → guestAuthenticate own triple (token issued?) → delete/expire link → repeat (still token?) → splice roomId of second tenant → compare BAD_USER_INPUT vs accessToken.
impact: unauthorized room entry by dead/re-keyed or cross-tenant links; MEDIUM-HIGH.
testability: AUTH_HELPED
[PARKED] Device-flow abuse (45): needs public client_id + phishing precondition + POST-only. Parked pending client_id harvest.
[PARKED] JWT alg-confusion: REJECTED — opaque/base64 tokens confirmed, not raw JWTs. Closed.
[FINAL] 1. Cross-tenant IDOR — 80. Highest impact, strongest structural evidence, clear verify steps. Blocked on: account creation (HUMAN).
[FINAL] 2. OAuth redirect_uri bypass — 65. HIGH impact if client_id harvested. Blocked on: desktop installer extraction (HUMAN).
[FINAL] 3. Guest-authz lifecycle/cross-tenant splice — 55. Design-level authz gap (no accessKey in GraphQL guest path). Blocked on: own tenant guest triple (HUMAN).
[LEARN] REJECTED AUTH @ apis.alfaview.com: Access tokens are opaque/base64 (distinct 401 "No base64 encoded access token was provided in the Authorization header."), not raw JWTs — JWT alg-confusion avenue closed.
[LEARN] ACCEPTED OATH @ sso.alfaview.com: /oauth2/authorize enforces client_id registration before any redirect_uri handling (`invalid_client`, reason `invalid_client_id`) — redirect_uri testing requires a registered client_id.
[LEARN] ACCEPTED MISCONFIG @ alfaview.com: root 301→/en (nginx, Accept-Language vary), /en 177KB marketing page with strict CSP and matomo; no unauthenticated SSO login links on marketing domain.
[LEARN] ACCEPTED MISCONFIG @ sso.alfaview.com: OIDC discovery exposed with issuer=acme.com (not alfaview.com), implicit flow enabled, HS256 listed but only RSA keys in JWKS.
[LEARN] ACCEPTED AUTH @ sso.alfaview.com: FusionAuth 1.63.0, /admin returns 404 (not exposed unauthenticated).
[LEARN] ACCEPTED MISCONFIG @ test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079, 4 platforms), no visible integrity verification.
[LEARN] ACCEPTED MISCONFIG @ app.alfaview.com: Full GraphQL admin schema in public bundle; per-resolver auth (listIdentityProviders unauth data, listComponents unauth 500).
[LEARN] ACCEPTED AUTH @ app.alfaview.com/graphql: guestAuthenticate/guestJoin unauthenticated-reachable (BAD_USER_INPUT, not UNAUTHENTICATED); no accessKey in GraphQL guest signature — diverges from REST 4-field accessKey combo.
[LEARN] ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — HTTP Basic auth gate (not OAuth).
[LEARN] REJECTED MISCONFIG @ dev.alfaview.com: Timeout/unreachable.
[LEARN] ACCEPTED MISCONFIG @ www.alfaview.com: 301 redirect to alfaview.com/en (no independent surface).
[LEARN] REJECTED MISCONFIG @ beta-ionoscloud-21-beta-engine-*.alfaview.com: Both engine hosts timeout (000) — internal/firewalled like alfacheck-* fleet.
[RISK] alfaview: 48/100. Remaining exposure concentrated on three auth-gated chains: REST cross-tenant IDOR (80), OAuth redirect_uri bypass (65), guest-authz lifecycle/cross-tenant splice (55). No live bug proven. All paths resolve to either a self-service tenant (HUMAN) or a harvested desktop client_id. Anonymous GraphQL surface largely closed (sensitive resolvers auth-gated; only empty listIdentityProviders + crashing listComponents remain). Passive yield exhausted; risk conditional on human action to unlock credential-gated test surfaces.
## 2026-09-05 17:06:07 UTC [target] (model bigpickle)
[NEW] app.alfaview.com/js/AppSignup.min.3329eeac503c038b44b8.js (48KB lazy chunk) recovered: SIGNUP IS UNAUTHENTICATED — Signup action sends NO token header (vs CreateCompany which sends headers:{token:s}). Exact payload: signup({displayName, user:{email, userProfile:{firstname,lastname}, locale:{UILanguage}}, agreedToTerms, agreedToPrivacyPolicy, planId}). Free-tier plan constant: "free-business" ({return"free-business"===this.paymentPlanId}).
[NEW] app.alfaview.com/js/AppFinishSignup.min.c05a2146b0620d1167ae.js recovered: activation is EMAIL-GATED — finishSignup({companyId, username, activationToken, password}) pulled from URL route /finish-signup/{companyId}/{activationToken} (username fallback from query param "principal"). Signup redirects to SIGNUP_EMAIL_SENT route — company created pre-email, activated post-click. Headless signup impossible without the email — this gate is HUMAN (mailbox access), the remaining chain is fully automatable.
[NEW] GraphQL client posts to /graphql with credentials:"include" (Apollo), authenticated ops use a token header (not Authorization/Bearer); Signup/FinishSignup send NO token → anonymous reachable. No CSRF header observed on GraphQL client.
[NEXT] HUMAN: Create one throwaway free alfaview company — now fully prepped: either (a) UI: https://app.alfaview.com signup (plan "Continue with free") with a disposable inbox, click activation email, report the chosen email+password; or (b) operator approves + drops a catch-all inbox address and I run the two mutation chain: POST app.alfaview.com/graphql {q: signup(displayName,user:{email,userProfile:{firstname,lastname},locale:{UILanguage}},agreedToTerms:true,agreedToPrivacyPolicy:true,planId:"free-business")}, await activation email for {companyId,activationToken} → finishSignup(username,password,companyId,activationToken) → POST apis.alfaview.com/v2/auth/password {"username","password"} → opaque base64 bearer → GET /v2/users/me baseline 200 → GET /v2/rooms/{victimUUID}/passcode (403=enforced vs 200=IDOR) → fetchUserPermissions(victimUserId) cross-tenant verdict. After handoff all remaining probes are read/mutate-own-tenant only at <=1 rps.
[RISK] alfaview: 48/100 — unchanged. Signup gate reduced to a single human mailbox action; the moment a test tenant exists, rank-1 IDOR (80) + guest-authz splice (55) go from static to one-command testable.
[HYP] Cross-tenant IDOR via UUID path params on REST user/room/permission ops
class: IDOR
asset: apis.alfaview.com/v2
confidence: 80
reasoning: OpenAPI confirms token-authed UUID path params (DELETE /v2/users/{id}, PATCH/DELETE /v2/rooms/{roomId}/permissions/{userId}, GET /v2/rooms/{roomId}/passcode). Tenant A's opaque bearer vs tenant B UUIDs → cross-tenant authz unverified. Signup roadblock now fully mapped: unauth Signup mutation (planId="free-business") → email activation → REST password auth.
evidence_needed: tenant A token returns 200/204 for tenant B userId/roomId where 403 expected (passcode+permissions first).
verify_steps: signup free tenant → POST /v2/auth/password {"username","password"} → bearer → GET /v2/users/me (200 baseline) → GET /v2/rooms/{victimUUID}/passcode → 403 vs 200 → PATCH /v2/rooms/{victimRoomId}/permissions/{victimUserId} → 403 vs 204.
impact: cross-tenant PII read, passcode/recording access, permission/account mutation, user delete; HIGH.
testability: AUTH_HELPED
[HYP] OAuth redirect_uri validation bypass on FusionAuth authorize → code theft
class: OATH
asset: sso.alfaview.com/oauth2/authorize
confidence: 65
reasoning: invalid_client gate precedes redirect_uri handling (confirmed); FusionAuth exact-match registry with known path/query-substitution bypass variants; web bundle carries no client_id; S3 buckets 403.
evidence_needed: registered client_id + its exact redirect_uri, then 302 Location on mutated URI.
verify_steps: harvest client_id from desktop installer → GET /oauth2/authorize?...&redirect_uri=<reg>/../evil → compare 302 target vs exact-match.
impact: code theft → ATO of any alfaview user; HIGH.
testability: AUTH_HELPED
[HYP] Guest-authz gap: dead/re-keyed or cross-tenant guest triples still mint tokens via GraphQL
class: AUTH
asset: app.alfaview.com/graphql (guestAuthenticate/guestJoin)
confidence: 55
reasoning: guestAuthenticate(userId,companyId,roomId) anonymous-executing, lookup by ID triple only (no secret; REST requires 4-field accessKey combo). Candidate flaws: revoked/expired triple still returns accessToken; cross-tenant tuple splice accepted.
evidence_needed: own tenant guest triple: token issued without accessKey; persists after link revoke; cross-pair accepts.
verify_steps: GenerateGroupLink → guestAuthenticate own triple → revoke link → retry → splice second-tenant roomId → compare accessToken vs BAD_USER_INPUT.
impact: unauthorized room entry by dead/re-keyed or cross-tenant links; MEDIUM-HIGH.
testability: AUTH_HELPED
