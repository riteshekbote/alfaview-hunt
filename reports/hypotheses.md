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
