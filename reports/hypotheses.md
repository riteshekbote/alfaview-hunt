# Hypotheses (ranked)

## RANKED HYPOTHESES 2026-09-03 11:40:30 UTC

## RANKED HYPOTHESES 2026-09-03 14:23:21 UTC
- [72] apis.alfaview.com: IDOR on room permissions and user deletion (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET `https://beta-apis.alfaview.com/v2/languages` — no Authorization header, compare response code/body vs production `https://apis.alfaview.com/v2/langu
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: guest link auth flow requires 3-field combo (companyId+roomId+accessKey) — rate-limit testing needed.
- LEARN: REJECTED MISCONFIG @ internal.alfaview.com: HTTP Basic auth gate confirmed; default credential testing is out of scope per program rules (brute-force rejected c
