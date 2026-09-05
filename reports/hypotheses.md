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

## RANKED HYPOTHESES 2026-09-03 23:24:23 UTC
- [80] apis.alfaview.com: Cross-tenant IDOR on room permissions and user deletion via UUID path params (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET `https://alfacheck-audio.alfaview.com/`, `https://alfacheck-engine.alfaview.com/`, `https://alfacheck-video.alfaview.com/` — probe unprobed dedicated
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 3-field combo (companyId+roomId+accessKey) — rate-limit testing needed.
- LEARN: ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both beta and production now expose /v2/languages with identical auth enforcement (401
- LEARN: REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
- LEARN: REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only, /health=204, all common paths 404.
- LEARN: REJECTED MISCONFIG @ beta-webclient.alfaview.com: Same SPA shell as insider, no internal endpoints exposed.
- LEARN: ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface (SPA), testable immediately.

## RANKED HYPOTHESES 2026-09-04 01:12:26 UTC
- [80] apis.alfaview.com: Cross-tenant IDOR on room permissions and user deletion via UUID path params (from art/lead_nemotron3.txt)
- [55] alfacheck-engine.alfaview.com: Alfacheck engineering service exposes internal health/debug endpoints (from art/lead_bigpickle.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET `https://beta-hcloud-19-beta-hydra-dzwx8.alfaview.com/` — capture response body, Content-Type, headers. Then GET `/health`, `/metrics`, `/debug`, `/a
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://alfacheck-engine.alfaview.com/ — capture status, Content-Type, body. GET https://alfacheck-audio.alfaview.com/ — same. GET https://alfacheck-
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 3-field combo (companyId+roomId+accessKey) — rate-limit testing needed.
- LEARN: ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both beta and production now expose /v2/languages with identical auth enforcement (401
- LEARN: REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
- LEARN: REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only, /health=204, all common paths 404.
- LEARN: REJECTED MISCONFIG @ beta-webclient.alfaview.com: Same SPA shell as insider, no internal endpoints exposed.
- LEARN: ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface (SPA), testable immediately.
- LEARN: ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (HTTP 200), testable immediately — NEW target.
- LEARN: ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (HTTP 200), testable immediately — NEW target.
- LEARN: ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (HTTP 200), testable immediately — NEW target.
- LEARN: ACCEPTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Unauthenticated infrastructure surface (HTTP 200) — NEW target.
- LEARN: ACCEPTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Unauthenticated infrastructure surface (HTTP 200) — NEW target.
- LEARN: ACCEPTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Unauthenticated infrastructure surface (HTTP 200) — NEW target.
- LEARN: REJECTED MISCONFIG @ appstats.alfaview.com: Unreachable/timeout — no surface.
- LEARN: REJECTED MISCONFIG @ consul-monitoring.alfaview.com: Unreachable/timeout — no surface.
- LEARN: REJECTED MISCONFIG @ equipment.alfaview.com: Unreachable/timeout — no surface.
- LEARN: REJECTED MISCONFIG @ ip-185-245-101-240.alfaview.com: Unreachable/timeout — no surface.
- LEARN: ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
- LEARN: ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.
- LEARN: REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only (4396B), /health=204, all common paths 404. Tar
- LEARN: REJECTED MISCONFIG @ beta-webclient.alfaview.com: Identical SPA shell to insider, no internal endpoints. Target exhausted.
- LEARN: REJECTED MISCONFIG @ demo-company.alfaview.com: SPA catch-all confirmed — /api/v1/users returns identical HTML shell as root. No unauthenticated data exposure.
- LEARN: REJECTED MISCONFIG @ beta-apis.alfaview.com: Undocumented endpoints (/v2/debug, /v2/admin, /v2/internal, /v2/test, /v2/health) all 404. Surface fully enumerated
- LEARN: ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both environments now expose /v2/languages with identical auth enforcement (401).
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI specs identical beta/prod. UUID path params on DELETE /v2/users/{id} and PATCH /v2/rooms/{roomId}/permissions/{userId
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth requires 3-field combo (companyId+roomId+accessKey). Rate-limit status unknown — needs authenticated probe.

## RANKED HYPOTHESES 2026-09-04 06:00:56 UTC
- [80] apis.alfaview.com: Cross-tenant IDOR on room permissions and user deletion via UUID path params (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://beta-hcloud-19-beta-hydra-dzwx8.alfaview.com/ — capture exact 9-byte body, Content-Type. Then GET https://beta-hcloud-19-beta-hydra-dzwx8.alf
- NEXT(hypotheses-nemotron3.txt): PROBE: GET `https://apis.alfaview.com/v2/docs/openapi.json` — fetch full OpenAPI spec to identify exact guest auth endpoint path and request schema for rate-lim
- LEARN: REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: UNREACHABLE — all 3 probes (root, /health, /status) timed out. Host resolves in DNS but does not serve HTTP.
- LEARN: REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: UNREACHABLE — all 3 probes (root, /media, /recordings) timed out. Same as engine. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-video.alfaview.com: UNREACHABLE — root probe timed out. Same pattern. Target exhausted.
- LEARN: ACCEPTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: HTTP 200 with 9-byte body, trailing slash 400 — minimal health-check surface, likely OAuth/OI
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target. N
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth requires 3-field combo (companyId+roomId+accessKey). Rate-limit status unknown — needs authenticated probe.
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 3-field combo (companyId+roomId+accessKey) — rate-limit testing needed.
- LEARN: ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both beta and production now expose /v2/languages with identical auth enforcement (401
- LEARN: REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
- LEARN: REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only (4396B), /health=204, all common paths 404.
- LEARN: REJECTED MISCONFIG @ beta-webclient.alfaview.com: Same SPA shell as insider, no internal endpoints exposed.
- LEARN: ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface (SPA), testable immediately.
- LEARN: ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Hydra hosts are media/signaling servers returning "Hi Client" on all paths — no internal endp
- LEARN: REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same as above — media server, not admin panel.
- LEARN: REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same as above — media server, not admin panel.
- LEARN: REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: Unreachable/timeout — no surface.
- LEARN: REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: Unreachable/timeout — no surface.
- LEARN: REJECTED MISCONFIG @ alfacheck-video.alfaview.com: Unreachable/timeout — no surface.
- LEARN: ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
- LEARN: ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.

## RANKED HYPOTHESES 2026-09-04 10:12:15 UTC
- [80] apis.alfaview.com: Cross-tenant IDOR on room permissions and user deletion via UUID path params (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: POST `https://apis.alfaview.com/v2/auth/guest-link` with JSON `{"companyId":"<known>","roomId":"<known>","accessKey":"invalid-key-001","displayName":"tes
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 4-field combo (companyId+roomId+accessKey+displayName) — rate-limit testing needed.
- LEARN: ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both beta and production now expose /v2/languages with identical auth enforcement (401
- LEARN: REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
- LEARN: REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only (4396B), /health=204, all common paths 404.
- LEARN: REJECTED MISCONFIG @ beta-webclient.alfaview.com: Same SPA shell as insider, no internal endpoints exposed.
- LEARN: ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface (SPA), testable immediately.
- LEARN: ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Hydra hosts are media/signaling servers returning "Hi Client" on all paths — no internal endp
- LEARN: REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same as above — media server, not admin panel.
- LEARN: REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same as above — media server, not admin panel.
- LEARN: REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: Unreachable/timeout — no surface.
- LEARN: REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: Unreachable/timeout — no surface.
- LEARN: REJECTED MISCONFIG @ alfacheck-video.alfaview.com: Unreachable/timeout — no surface.
- LEARN: ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
- LEARN: ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.

## RANKED HYPOTHESES 2026-09-04 14:25:07 UTC
- [85] apis.alfaview.com: Cross-tenant IDOR on room permissions and user deletion via UUID path params (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET https://alfatraining.alfaview.com/ — capture full HTML body (Content-Length, inline scripts, <script src> URLs, any tenant-specific data in HTML or J
- NEXT(hypotheses-nemotron3.txt): PROBE: POST `https://apis.alfaview.com/v2/auth/guest-link` with JSON `{"companyId":"<known>","roomId":"<known>","accessKey":"invalid-key-001","displayName":"tes
- LEARN: REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Hydra hosts are media/signaling servers — "Hi Client" body on all paths, not OIDC/auth infras
- LEARN: REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — media server, "Hi Client" response. Target exhausted.
- LEARN: REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — media server, "Hi Client" response. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: UNREACHABLE — all 3 probes (root, /health, /status) timed out. Internal/firewalled. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: UNREACHABLE — all 3 probes (root, /media, /recordings) timed out. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-video.alfaview.com: UNREACHABLE — root probe timed out. Target exhausted.
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target. N
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth requires 4-field combo (companyId+roomId+accessKey+displayName). Rate-limit status unknown — needs probe.
- LEARN: ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both environments now expose /v2/languages with identical auth enforcement (401).
- LEARN: REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
- LEARN: REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only, /health=204, all common paths 404. Target exha
- LEARN: REJECTED MISCONFIG @ beta-webclient.alfaview.com: Identical SPA shell to insider, no internal endpoints. Target exhausted.
- LEARN: REJECTED MISCONFIG @ demo-company.alfaview.com: SPA catch-all confirmed — /api/v1/users returns identical HTML shell as root. No unauthenticated data exposure.
- LEARN: ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
- LEARN: ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.
- LEARN: ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Media/signaling server ("Hi Client"), not OIDC/auth infrastructure. Target exhausted.
- LEARN: REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — media server. Target exhausted.
- LEARN: REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — media server. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: UNREACHABLE — all 3 probes timed out. Internal/firewalled. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: UNREACHABLE — all 3 probes timed out. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-video.alfaview.com: UNREACHABLE — root probe timed out. Target exhausted.
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target. N
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth requires 4-field combo (companyId+roomId+accessKey+displayName). Rate-limit status unknown — needs probe.
- LEARN: ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both environments now expose /v2/languages with identical auth enforcement (401).
- LEARN: REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
- LEARN: REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only, /health=204, all common paths 404. Target exha
- LEARN: REJECTED MISCONFIG @ beta-webclient.alfaview.com: Identical SPA shell to insider, no internal endpoints. Target exhausted.
- LEARN: REJECTED MISCONFIG @ demo-company.alfaview.com: SPA catch-all confirmed — /api/v1/users returns identical HTML shell as root. No unauthenticated data exposure.
- LEARN: ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
- LEARN: ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.
- LEARN: ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 4-field combo (companyId+roomId+accessKey+displayName) — rate-limit testing needed.
- LEARN: ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both beta and production now expose /v2/languages with identical auth enforcement (401
- LEARN: REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
- LEARN: REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only (4396B), /health=204, all common paths 404.
- LEARN: REJECTED MISCONFIG @ beta-webclient.alfaview.com: Same SPA shell as insider, no internal endpoints exposed.
- LEARN: ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface (SPA), testable immediately.
- LEARN: ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Hydra hosts are media/signaling servers returning "Hi Client" on all paths — no internal endp
- LEARN: REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same as above — media server, not admin panel.
- LEARN: REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same as above — media server, not admin panel.
- LEARN: REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: Unreachable/timeout — no surface.
- LEARN: REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: Unreachable/timeout — no surface.
- LEARN: REJECTED MISCONFIG @ alfacheck-video.alfaview.com: Unreachable/timeout — no surface.
- LEARN: ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
- LEARN: ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.

## RANKED HYPOTHESES 2026-09-04 17:51:04 UTC
- [85] apis.alfaview.com: Cross-tenant IDOR on room permissions and user deletion via UUID path params (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: POST `https://apis.alfaview.com/v2/auth/guest-link` with JSON `{"companyId":"<known>","roomId":"<known>","accessKey":"invalid-key-001","displayName":"tes
- LEARN: REJECTED XSS @ alfatraining/bhc/kh-freiburg.alfaview.com: All three multi-tenant hosts serve byte-identical generic alfaview.com SPA shell (1381B, MD5 554a39...
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms UUID path params on DELETE /v2/users/{id} and PATCH /v2/rooms/{roomId}/permissions/{userId} — highest-p
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth requires 4-field combo (companyId+roomId+accessKey+displayName). Rate-limit status unknown — needs authentica
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 4-field combo (companyId+roomId+accessKey+displayName) — rate-limit testing needed.
- LEARN: ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both beta and production now expose /v2/languages with identical auth enforcement (401
- LEARN: REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
- LEARN: REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only (4396B), /health=204, all common paths 404.
- LEARN: REJECTED MISCONFIG @ beta-webclient.alfaview.com: Same SPA shell as insider, no internal endpoints exposed.
- LEARN: ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface (SPA), testable immediately.
- LEARN: ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Media/signaling server ("Hi Client"), not OIDC/auth infrastructure. Target exhausted.
- LEARN: REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — media server. Target exhausted.
- LEARN: REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — media server. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: UNREACHABLE — all 3 probes timed out. Internal/firewalled. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: UNREACHABLE — all 3 probes timed out. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-video.alfaview.com: UNREACHABLE — root probe timed out. Target exhausted.
- LEARN: ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
- LEARN: ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.

## RANKED HYPOTHESES 2026-09-04 20:04:20 UTC
- [85] apis.alfaview.com: Cross-tenant IDOR on room permissions and user deletion via UUID path params (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: HEAD `https://beta-ionoscloud-21-beta-hydra-7x5d5.alfaview.com/` → if 2xx/3xx/4xx (not timeout), follow with GET `/health`, `/metrics`, `/actuator/health
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 4-field combo (companyId+roomId+accessKey+displayName) — rate-limit testing needed.
- LEARN: ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both beta and production now expose /v2/languages with identical auth enforcement (401
- LEARN: REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
- LEARN: REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only (4396B), /health=204, all common paths 404.
- LEARN: REJECTED MISCONFIG @ beta-webclient.alfaview.com: Same SPA shell as insider, no internal endpoints exposed.
- LEARN: ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface (SPA), testable immediately.
- LEARN: ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Media/signaling server ("Hi Client"), not OIDC/auth infrastructure. Target exhausted.
- LEARN: REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — media server. Target exhausted.
- LEARN: REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — media server. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: UNREACHABLE — all 3 probes timed out. Internal/firewalled. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: UNREACHABLE — all 3 probes timed out. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-video.alfaview.com: UNREACHABLE — root probe timed out. Target exhausted.
- LEARN: ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
- LEARN: ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.
- LEARN: REJECTED XSS @ alfatraining/bhc/kh-freiburg.alfaview.com: All three multi-tenant hosts serve byte-identical generic alfaview.com SPA shell (1381B, MD5 554a39...

## RANKED HYPOTHESES 2026-09-04 22:09:53 UTC
- [65] alfaview.com: OAuth redirect_uri validation bypass on main domain leading to code theft (from art/lead_nemotron3.txt)
- NEXT(hypotheses-nemotron3.txt): PROBE: GET `https://alfaview.com/` → extract login/OAuth links → GET `https://sso.alfaview.com/authorize?client_id=test&redirect_uri=https://evil.com&response_t
- LEARN: ACCEPTED IDOR @ apis.alfaview.com: OpenAPI spec confirms path-param UUID patterns for permission and user delete — highest-priority authenticated test target.
- LEARN: ACCEPTED AUTH @ apis.alfaview.com: Guest link auth flow requires 4-field combo (companyId+roomId+accessKey+displayName) — rate-limit testing needed.
- LEARN: ACCEPTED MISCONFIG @ beta-apis.alfaview.com: API version drift resolved — both beta and production now expose /v2/languages with identical auth enforcement (401
- LEARN: REJECTED MISCONFIG @ beta-apis.alfaview.com: Beta API weaker auth enforcement disproven — OpenAPI specs identical, both require auth identically.
- LEARN: REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only (4396B), /health=204, all common paths 404.
- LEARN: REJECTED MISCONFIG @ beta-webclient.alfaview.com: Same SPA shell as insider, no internal endpoints exposed.
- LEARN: ACCEPTED MISCONFIG @ demo-company.alfaview.com: Unauthenticated web surface (SPA), testable immediately.
- LEARN: ACCEPTED MISCONFIG @ alfatraining.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED MISCONFIG @ bhc.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: ACCEPTED MISCONFIG @ kh-freiburg.alfaview.com: Unauthenticated web surface (SPA), testable immediately — multi-tenant SaaS confirmed.
- LEARN: REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Media/signaling server ("Hi Client"), not OIDC/auth infrastructure. Target exhausted.
- LEARN: REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — media server. Target exhausted.
- LEARN: REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — media server. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: UNREACHABLE — all 3 probes timed out. Internal/firewalled. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: UNREACHABLE — all 3 probes timed out. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-video.alfaview.com: UNREACHABLE — root probe timed out. Target exhausted.
- LEARN: ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — auth-gated, requires credentials.
- LEARN: ACCEPTED AUTH @ internal.alfaview.com: HTTP 401 — HTTP Basic auth gate confirmed.
- LEARN: REJECTED XSS @ alfatraining/bhc/kh-freiburg.alfaview.com: All three multi-tenant hosts serve byte-identical generic alfaview.com SPA shell (1381B, MD5 554a39). 
- LEARN: REJECTED MISCONFIG @ beta-ionoscloud-21-beta-hydra-7x5d5.alfaview.com: HTTP timeout on root and trailing slash — unlike other cloud hydra hosts, no "Hi Client" 

## RANKED HYPOTHESES 2026-09-05 00:15:07 UTC
- [75] sso.alfaview.com: OAuth redirect_uri validation bypass on sso.alfaview.com leading to authorization code theft (from art/lead_nemotron3.txt)
- [65] sso.alfaview.com/oauth2/authorize: OAuth redirect_uri validation bypass on FusionAuth authorize endpoint → code theft (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET `https://sso.alfaview.com/oauth2/register` (checks open tenant self-registration on prod IAM — if live, also maps default-registration surface); foll
- NEXT(hypotheses-nemotron3.txt): PROBE: GET `https://app.alfaview.com/js/app.min.5b3949112f0cf682adc8.js` → search for `client_id` or `oauth` config → use discovered client_id in `GET https://s
- LEARN: ACCEPTED MISCONFIG @ sso.alfaview.com: Production IAM is FusionAuth with default tenant issuer "acme.com" (OIDC `issuer` + JWK CN=acme.com, self-signed 2021-12-
- LEARN: ACCEPTED OATH @ sso.alfaview.com: /oauth2/authorize enforces client_id registration before any redirect_uri handling (`invalid_client`, reason `invalid_client_i
- LEARN: REJECTED AUTH @ apis.alfaview.com: Access tokens are opaque/base64 (distinct 401 "No base64 encoded access token was provided in the Authorization header."), no
- LEARN: ACCEPTED MISCONFIG @ alfaview.com: root now 301→/en (nginx, Accept-Language vary), /en 177KB marketing page with strict CSP and matomo; no unauthenticated SSO l
- LEARN: ACCEPTED MISCONFIG @ sso.alfaview.com: OIDC discovery exposed with issuer=acme.com (not alfaview.com), implicit flow enabled, HS256 listed but only RSA keys in 
- LEARN: ACCEPTED AUTH @ sso.alfaview.com: FusionAuth 1.63.0, /admin returns 404 (not exposed unauthenticated).
- LEARN: ACCEPTED MISCONFIG @ test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079) for 4 platforms, no visible integrity verification.
- LEARN: REJECTED MISCONFIG @ dev.alfaview.com: Timeout/unreachable.
- LEARN: ACCEPTED MISCONFIG @ www.alfaview.com: 301 redirect to alfaview.com/en (no independent surface).
- LEARN: REJECTED MISCONFIG @ beta-ionoscloud-21-beta-engine-*.alfaview.com: Both engine hosts timeout (000) — internal/firewalled like alfacheck-* fleet.

## RANKED HYPOTHESES 2026-09-05 04:34:13 UTC
- [75] sso.alfaview.com: OAuth redirect_uri validation bypass on sso.alfaview.com leading to authorization code theft (from art/lead_nemotron3.txt)
- [70] app.alfaview.com/graphql: Authz-gap on guest-link/company GraphQL ops after free-tenant self-signup (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): HUMAN: Create one throwaway free alfaview company via the documented self-service flow ("Create your free alfaview company now" on app.alfaview.com login page) 
- NEXT(hypotheses-nemotron3.txt): PROBE: GET `https://app.alfaview.com/js/app.min.5b3949112f0cf682adc8.js` → search for `client_id` or `oauth` config → use discovered client_id in `GET https://s
- LEARN: ACCEPTED MISCONFIG @ sso.alfaview.com: OIDC discovery exposed with issuer=acme.com (not alfaview.com), implicit flow enabled, HS256 listed but only RSA keys in 
- LEARN: ACCEPTED AUTH @ sso.alfaview.com: FusionAuth 1.63.0, /admin returns 404 (not exposed unauthenticated).
- LEARN: ACCEPTED MISCONFIG @ test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079) for 4 platforms, no visible integrity verification.
- LEARN: REJECTED MISCONFIG @ dev.alfaview.com: Timeout/unreachable.
- LEARN: ACCEPTED MISCONFIG @ www.alfaview.com: 301 redirect to alfaview.com/en (no independent surface).
- LEARN: REJECTED MISCONFIG @ beta-ionoscloud-21-beta-engine-*.alfaview.com: Both engine hosts timeout (000) — internal/firewalled like alfacheck-* fleet.

## RANKED HYPOTHESES 2026-09-05 08:50:59 UTC
- [75] sso.alfaview.com: OAuth redirect_uri validation bypass on sso.alfaview.com leading to authorization code theft (from art/lead_nemotron3.txt)
- [62] app.alfaview.com/graphql: Unauthenticated guest-token issuance via GraphQL ID-tuple guest path (accessKey omitted) (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: POST `https://app.alfaview.com/graphql` with `{"query":"mutation{guestAuthenticate(userId:\"<own-tenant-guestId>\",companyId:\"<own-companyId>\",roomId:\
- NEXT(hypotheses-nemotron3.txt): PROBE: GET `https://app.alfaview.com/` with browser DevTools / HAR capture → search network requests for OAuth config endpoint (client_id, authorization_endpoin
- LEARN: ACCEPTED MISCONFIG @ app.alfaview.com: Full GraphQL admin schema in public bundle; per-resolver auth (listIdentityProviders unauth data, listComponents unauth 5
- LEARN: ACCEPTED AUTH @ app.alfaview.com/graphql: guestAuthenticate/guestJoin unauthenticated-reachable (BAD_USER_INPUT, not UNAUTHENTICATED); no accessKey in GraphQL g
- LEARN: ACCEPTED MISCONFIG @ sso.alfaview.com: OIDC discovery exposed with issuer=acme.com (not alfaview.com), implicit flow enabled, HS256 listed but only RSA keys in 
- LEARN: ACCEPTED AUTH @ sso.alfaview.com: FusionAuth 1.63.0, /admin returns 404 (not exposed unauthenticated).
- LEARN: ACCEPTED MISCONFIG @ test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079) for 4 platforms, no visible integrity verification.
- LEARN: ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — HTTP Basic auth gate (not OAuth).
- LEARN: REJECTED MISCONFIG @ dev.alfaview.com: Timeout/unreachable.
- LEARN: ACCEPTED MISCONFIG @ www.alfaview.com: 301 redirect to alfaview.com/en (no independent surface).
- LEARN: REJECTED MISCONFIG @ beta-ionoscloud-21-beta-engine-*.alfaview.com: Both engine hosts timeout (000) — internal/firewalled like alfacheck-* fleet.

## RANKED HYPOTHESES 2026-09-05 12:20:10 UTC
- [80] apis.alfaview.com/v2: Cross-tenant IDOR on REST user/room/permission ops via UUID path params (from art/lead_bigpickle.txt)
- NEXT(hypotheses-bigpickle.txt): HUMAN: Create one throwaway free alfaview company via the documented self-service flow ("Create your free alfaview company now" on app.alfaview.com login page) 

## RANKED HYPOTHESES 2026-09-05 15:02:37 UTC
- [80] apis.alfaview.com/v2: Cross-tenant IDOR on REST user/room/permission ops via UUID path params (from art/lead_bigpickle.txt)
- LEARN: REJECTED AUTH @ apis.alfaview.com: Access tokens are opaque/base64 (distinct 401 "No base64 encoded access token was provided in the Authorization header."), no
- LEARN: ACCEPTED OATH @ sso.alfaview.com: /oauth2/authorize enforces client_id registration before any redirect_uri handling (`invalid_client`, reason `invalid_client_i
- LEARN: ACCEPTED MISCONFIG @ alfaview.com: root 301→/en (nginx, Accept-Language vary), /en 177KB marketing page with strict CSP and matomo; no unauthenticated SSO login
- LEARN: ACCEPTED MISCONFIG @ sso.alfaview.com: OIDC discovery exposed with issuer=acme.com (not alfaview.com), implicit flow enabled, HS256 listed but only RSA keys in 
- LEARN: ACCEPTED AUTH @ sso.alfaview.com: FusionAuth 1.63.0, /admin returns 404 (not exposed unauthenticated).
- LEARN: ACCEPTED MISCONFIG @ test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079, 4 platforms), no visible integrity verification.
- LEARN: ACCEPTED MISCONFIG @ app.alfaview.com: Full GraphQL admin schema in public bundle; per-resolver auth (listIdentityProviders unauth data, listComponents unauth 5
- LEARN: ACCEPTED AUTH @ app.alfaview.com/graphql: guestAuthenticate/guestJoin unauthenticated-reachable (BAD_USER_INPUT, not UNAUTHENTICATED); no accessKey in GraphQL g
- LEARN: ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — HTTP Basic auth gate (not OAuth).
- LEARN: REJECTED MISCONFIG @ dev.alfaview.com: Timeout/unreachable.
- LEARN: ACCEPTED MISCONFIG @ www.alfaview.com: 301 redirect to alfaview.com/en (no independent surface).
- LEARN: REJECTED MISCONFIG @ beta-ionoscloud-21-beta-engine-*.alfaview.com: Both engine hosts timeout (000) — internal/firewalled like alfacheck-* fleet.

## RANKED HYPOTHESES 2026-09-05 17:06:17 UTC
- [80] apis.alfaview.com/v2: Cross-tenant IDOR via UUID path params on REST user/room/permission ops (from art/lead_bigpickle.txt)
- [75] sso.alfaview.com: OAuth redirect_uri validation bypass on sso.alfaview.com leading to authorization code theft (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): HUMAN: Create one throwaway free alfaview company — now fully prepped: either (a) UI: https://app.alfaview.com signup (plan "Continue with free") with a disposa
- NEXT(hypotheses-nemotron3.txt): PROBE: GET `https://app.alfaview.com/` with browser DevTools / HAR capture → search network requests for OAuth config endpoint (client_id, authorization_endpoin
- LEARN: ACCEPTED MISCONFIG @ sso.alfaview.com: OIDC discovery exposed with issuer=acme.com (not alfaview.com), implicit flow enabled, HS256 listed but only RSA keys in 
- LEARN: ACCEPTED AUTH @ sso.alfaview.com: FusionAuth 1.63.0, /admin returns 404 (not exposed unauthenticated).
- LEARN: ACCEPTED MISCONFIG @ test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079) for 4 platforms, no visible integrity verification.
- LEARN: ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — HTTP Basic auth gate (not OAuth).
- LEARN: REJECTED MISCONFIG @ dev.alfaview.com: Timeout/unreachable.
- LEARN: ACCEPTED MISCONFIG @ www.alfaview.com: 301 redirect to alfaview.com/en (no independent surface).
- LEARN: REJECTED MISCONFIG @ beta-ionoscloud-21-beta-engine-*.alfaview.com: Both engine hosts timeout (000) — internal/firewalled like alfacheck-* fleet.
- LEARN: ACCEPTED MISCONFIG @ app.alfaview.com: Full GraphQL admin schema in public bundle; per-resolver auth (listIdentityProviders unauth data, listComponents unauth 5
- LEARN: ACCEPTED AUTH @ app.alfaview.com/graphql: guestAuthenticate/guestJoin unauthenticated-reachable (BAD_USER_INPUT, not UNAUTHENTICATED); no accessKey in GraphQL g
- LEARN: REJECTED AUTH @ apis.alfaview.com: Access tokens are opaque/base64 (distinct 401 "No base64 encoded access token was provided in the Authorization header."), no
- LEARN: ACCEPTED MISCONFIG @ alfaview.com: root 301→/en (nginx, Accept-Language vary), /en 177KB marketing page with strict CSP and matomo; no unauthenticated SSO login
- LEARN: REJECTED XSS @ alfatraining/bhc/kh-freiburg.alfaview.com: All three multi-tenant hosts serve byte-identical generic alfaview.com SPA shell (1381B, MD5 554a39). 
- LEARN: REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only, /health=204, all common paths 404. Target exha
- LEARN: REJECTED MISCONFIG @ beta-webclient.alfaview.com: Identical SPA shell to insider, no internal endpoints. Target exhausted.
- LEARN: REJECTED MISCONFIG @ demo-company.alfaview.com: SPA catch-all confirmed — /api/v1/users returns identical HTML shell as root. No unauthenticated data exposure.
- LEARN: REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Media/signaling server ("Hi Client"), not OIDC/auth infrastructure. Target exhausted.
- LEARN: REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — media server. Target exhausted.
- LEARN: REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — media server. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: UNREACHABLE — all 3 probes timed out. Internal/firewalled. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: UNREACHABLE — all 3 probes timed out. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-video.alfaview.com: UNREACHABLE — root probe timed out. Target exhausted.

## RANKED HYPOTHESES 2026-09-05 18:56:58 UTC
- [80] apis.alfaview.com/v2: Cross-tenant IDOR via UUID path params on REST user/room/permission ops (from art/lead_bigpickle.txt)
- [75] sso.alfaview.com: OAuth redirect_uri validation bypass on sso.alfaview.com leading to authorization code theft (from art/lead_nemotron3.txt)
- NEXT(hypotheses-bigpickle.txt): PROBE: GET `https://test.alfaview.com/` → extract binary download URLs for alfacheck v470079 (linux/amd64, smallest) → download the ELF binary → run `strings` o
- NEXT(hypotheses-nemotron3.txt): PROBE: GET `https://app.alfaview.com/` with browser DevTools / HAR capture → search network requests for OAuth config endpoint (client_id, authorization_endpoin
- LEARN: ACCEPTED MISCONFIG @ test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079, 4 platforms) — potential client_id source for OAuth redirect_uri
- LEARN: REJECTED AUTH @ apis.alfaview.com: Access tokens are opaque/base64 (distinct 401 "No base64 encoded access token was provided in the Authorization header."), no
- LEARN: ACCEPTED OATH @ sso.alfaview.com: /oauth2/authorize enforces client_id registration before any redirect_uri handling (`invalid_client`, reason `invalid_client_i
- LEARN: ACCEPTED MISCONFIG @ alfaview.com: root 301→/en (nginx, Accept-Language vary), /en 177KB marketing page with strict CSP and matomo; no unauthenticated SSO login
- LEARN: ACCEPTED MISCONFIG @ sso.alfaview.com: OIDC discovery exposed with issuer=acme.com (not alfaview.com), implicit flow enabled, HS256 listed but only RSA keys in 
- LEARN: ACCEPTED AUTH @ sso.alfaview.com: FusionAuth 1.63.0, /admin returns 404 (not exposed unauthenticated).
- LEARN: ACCEPTED MISCONFIG @ test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079) for 4 platforms, no visible integrity verification — POTENTIAL C
- LEARN: ACCEPTED MISCONFIG @ app.alfaview.com: Full GraphQL admin schema in public bundle; per-resolver auth (listIdentityProviders unauth data, listComponents unauth 5
- LEARN: ACCEPTED AUTH @ app.alfaview.com/graphql: guestAuthenticate/guestJoin unauthenticated-reachable (BAD_USER_INPUT, not UNAUTHENTICATED); no accessKey in GraphQL g
- LEARN: ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — HTTP Basic auth gate (not OAuth).
- LEARN: REJECTED MISCONFIG @ dev.alfaview.com: Timeout/unreachable.
- LEARN: ACCEPTED MISCONFIG @ www.alfaview.com: 301 redirect to alfaview.com/en (no independent surface).
- LEARN: REJECTED MISCONFIG @ beta-ionoscloud-21-beta-engine-*.alfaview.com: Both engine hosts timeout (000) — internal/firewalled like alfacheck-* fleet.
- LEARN: ACCEPTED MISCONFIG @ sso.alfaview.com: OIDC discovery exposed with issuer=acme.com (not alfaview.com), implicit flow enabled, HS256 listed but only RSA keys in 
- LEARN: ACCEPTED AUTH @ sso.alfaview.com: FusionAuth 1.63.0, /admin returns 404 (not exposed unauthenticated).
- LEARN: ACCEPTED MISCONFIG @ test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079) for 4 platforms, no visible integrity verification.
- LEARN: ACCEPTED AUTH @ beta-app.alfaview.com: HTTP 401 — HTTP Basic auth gate (not OAuth).
- LEARN: REJECTED MISCONFIG @ dev.alfaview.com: Timeout/unreachable.
- LEARN: ACCEPTED MISCONFIG @ www.alfaview.com: 301 redirect to alfaview.com/en (no independent surface).
- LEARN: REJECTED MISCONFIG @ beta-ionoscloud-21-beta-engine-*.alfaview.com: Both engine hosts timeout (000) — internal/firewalled like alfacheck-* fleet.
- LEARN: ACCEPTED MISCONFIG @ app.alfaview.com: Full GraphQL admin schema in public bundle; per-resolver auth (listIdentityProviders unauth data, listComponents unauth 5
- LEARN: ACCEPTED AUTH @ app.alfaview.com/graphql: guestAuthenticate/guestJoin unauthenticated-reachable (BAD_USER_INPUT, not UNAUTHENTICATED); no accessKey in GraphQL g
- LEARN: REJECTED AUTH @ apis.alfaview.com: Access tokens are opaque/base64 (distinct 401 "No base64 encoded access token was provided in the Authorization header."), no
- LEARN: ACCEPTED MISCONFIG @ alfaview.com: root 301→/en (nginx, Accept-Language vary), /en 177KB marketing page with strict CSP and matomo; no unauthenticated SSO login
- LEARN: REJECTED XSS @ alfatraining/bhc/kh-freiburg.alfaview.com: All three multi-tenant hosts serve byte-identical generic alfaview.com SPA shell (1381B, MD5 554a39). 
- LEARN: REJECTED MISCONFIG @ insider-webclient.alfaview.com: Internal admin/debug endpoints not exposed — SPA shell only, /health=204, all common paths 404. Target exha
- LEARN: REJECTED MISCONFIG @ beta-webclient.alfaview.com: Identical SPA shell to insider, no internal endpoints. Target exhausted.
- LEARN: REJECTED MISCONFIG @ demo-company.alfaview.com: SPA catch-all confirmed — /api/v1/users returns identical HTML shell as root. No unauthenticated data exposure.
- LEARN: REJECTED MISCONFIG @ beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Media/signaling server ("Hi Client"), not OIDC/auth infrastructure. Target exhausted.
- LEARN: REJECTED MISCONFIG @ beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — media server. Target exhausted.
- LEARN: REJECTED MISCONFIG @ beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — media server. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-engine.alfaview.com: UNREACHABLE — all 3 probes timed out. Internal/firewalled. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-audio.alfaview.com: UNREACHABLE — all 3 probes timed out. Target exhausted.
- LEARN: REJECTED MISCONFIG @ alfacheck-video.alfaview.com: UNREACHABLE — root probe timed out. Target exhausted.
