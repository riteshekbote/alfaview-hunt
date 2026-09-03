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
