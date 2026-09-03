# Hypotheses (ranked)

## RANKED HYPOTHESES 2026-09-03 11:40:30 UTC

## RANKED HYPOTHESES 2026-09-03 14:23:21 UTC
- [72] apis.alfaview.com: IDOR on room permissions and user deletion (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET `https://beta-apis.alfaview.com/v2/languages` — no Authorization header, compare response code/body vs production `https://apis.alfaview.com/v2/langu
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: guest link auth flow requires 3-field combo (companyId+roomId+accessKey) — rate-limit testing needed.
- LEARN: REJECTED MISCONFIG @ internal.alfaview.com: HTTP Basic auth gate confirmed; default credential testing is out of scope per program rules (brute-force rejected c

## RANKED HYPOTHESES 2026-09-03 15:21:33 UTC
- [72] apis.alfaview.com: IDOR on room permissions and user deletion via UUID path params (from art/lead_nemotron3.txt)
- [72] apis.alfaview.com: IDOR on room permissions and user deletion (from art/lead_bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET `https://beta-apis.alfaview.com/openapi.json` and GET `https://apis.alfaview.com/openapi.json` — compare endpoint sets, auth requirements, and schema
- NEXT(hypotheses-bigpickle.txt): PROBE: GET `https://demo-company.alfaview.com/api/v1/users` — no auth, check for exposed user list or test data.
- LEARN: REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — both beta and production require auth (beta 401, prod 404 for /v2/lang
- LEARN: ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift confirmed — beta exposes /v2/languages endpoint absent in production (401 vs 404).
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 3-field combo (companyId+roomId+accessKey) — rate-limit testing needed.
- LEARN: REJECTED MISCONFIG @ beta-apis.alfaview.com: Auth response identical to production (401 + same error body). Beta environment has same auth enforcement.
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: Highest-priority authenticated test target.
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 3-field combo.
- LEARN: ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface, testable immediately.

## RANKED HYPOTHESES 2026-09-03 18:48:37 UTC
- [75] apis.alfaview.com: Cross-tenant IDOR on room permissions via UUID path params (from art/lead_nemotron3.txt)
- [55] insider-webclient.alfaview.com: Insider web client exposes internal admin panel without auth (from art/lead_bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET `https://beta-apis.alfaview.com/v2/debug`, `https://beta-apis.alfaview.com/v2/admin`, `https://beta-apis.alfaview.com/v2/internal`, `https://beta-api
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://insider-webclient.alfaview.com/ — observe response body, content-type, title tag, script src URLs. Then GET https://insider-webclient.alfavie
- LEARN: REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth (beta 401, prod 404 for /v2
- LEARN: ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift confirmed — beta exposes /v2/languages endpoint absent in production (401 vs 404).
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 3-field combo (companyId+roomId+accessKey) — rate-limit testing needed.
- LEARN: ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface, testable immediately (but /api/v1/users redirects to /).
- LEARN: REJECTED MISCONFIG @ demo-company.alfaview.com: SPA confirmed — /api/v1/users serves same HTML shell as root. No real data exposure.
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: Highest-priority authenticated test target.
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 3-field combo.
- LEARN: ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift confirmed — beta has /v2/languages absent in production.

## RANKED HYPOTHESES 2026-09-03 21:22:00 UTC
- [80] apis.alfaview.com: Cross-tenant IDOR on room permissions and user deletion via UUID path params (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET `https://insider-webclient.alfaview.com/` — analyze response body for API base URLs, admin routes, feature flags, script src URLs. Then GET `https://
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://insider-webclient.alfaview.com/ — capture Content-Type header, <title> tag, and all <script src> URLs. Then GET https://insider-webclient.alf
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 3-field combo (companyId+roomId+accessKey) — rate-limit testing needed.
- LEARN: ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift confirmed — beta exposes /v2/languages endpoint absent in production (401 vs 404 historically).
- LEARN: REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
- LEARN: REJECTED MISCONFIG @ demo-company.alfaview.com: SPA confirmed — /api/v1/users serves same HTML shell as root. No real data exposure.
- LEARN: ACCEPTED MISCONFIG @ insider-webclient.alfaview.com: Unauthenticated web surface (HTTP 200), testable immediately — NEW target.
- LEARN: REJECTED MISCONFIG @ beta-apis.alfaview.com: Undocumented endpoints (/v2/debug, /v2/admin, /v2/internal, /v2/test, /v2/health) all 404. Beta surface fully enume
- LEARN: ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift limited to /v2/languages only — confirmed after full endpoint probe.
- LEARN: REJECTED MISCONFIG @ demo-company.alfaview.com: SPA catch-all confirmed — /api/v1/users returns identical HTML shell as root (len=1381). No unauthenticated data
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI specs identical beta/prod. UUID path params on DELETE /v2/users/{id} and PATCH /v2/rooms/{roomId}/permissions/{userId
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth requires 3-field combo. Rate-limit status unknown — needs authenticated probe.
