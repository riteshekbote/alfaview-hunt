# alfaview gmbh inventory (discovery seed 2026-09-02)
# NOTE: hosts below are discovery candidates from passive DNS/CT; confirm in-scope vs program scope before active testing.
alfaview.com
app.alfaview.com
dev.alfaview.com
sso.alfaview.com
staging.alfaview.com
support.alfaview.com
test.alfaview.com
www.alfaview.com

## PASSIVE RECON 2026-09-02 (read-only, non-intrusive)

> Recon observations only. These are NOT confirmed vulnerabilities; ownership/in-scope of each host must be confirmed against the program scope before any active testing. Hosts resolve + serve HTTP — investigation requires scoped authorization.

**Probed:** 8 hosts | **Live HTTP:** 2

| Host | Status | Server/Tech |
|---|---|---|
| `support.alfaview.com` | 301 | Server: myracloud -> https://support.alfaview.com/en/ |
| `staging.alfaview.com` | 301 | Server: myracloud -> https://staging.alfaview.com/en |

**CNAME review signals (2):**
- `support.alfaview.com` -> `support-alfaview-com.ax4z.com`
- `staging.alfaview.com` -> `staging-alfaview-com.ax4z.com`

## DEEP SERVICE SCAN 2026-09-02 (read-only connect+banner)
**Host:** `staging.alfaview.com` | **Ports:** [80, 443]
**Web surface only:** [80, 443]

## DEEP SERVICE SCAN 2026-09-02 (read-only connect+banner)
**Host:** `support.alfaview.com` | **Ports:** [80, 443]
**Web surface only:** [80, 443]

## DEEP ENUM (wildcard-cleaned) 2026-09-03
**Root zone:** `alfaview.com` | **dedicated hosts after wildcard-filter: 55**
> Audit: brute+passive subfinder produced 10,083 resolving hostnames; zone-wildcard + IP-fingerprint filtering dropped 9,973 (98.9%) DNS-wildcard noise (random labels resolving to shared wildcard IPs e.g. account.cineplex.de, a.hypofriend.de, account.live-manager.de, docker.jtl-software.de, *.ggamdom.com, *.dev.alfaview.com). Only genuine dedicated hosts listed below. These are surface-map observations; live HTTP status captured read-only (GET / via curl). No findings claimed; scope must be confirmed with the program.
- `alfacheck-audio.alfaview.com`  [HTTP unprobed]
- `alfacheck-engine.alfaview.com`  [HTTP unprobed]
- `alfacheck-video.alfaview.com`  [HTTP unprobed]
- `alfatraining.alfaview.com`  [HTTP 200]
- `alfaview-com-assets.alfaview.com`  [HTTP 403]
- `apis.alfaview.com`  [HTTP 200]
- `app.alfaview.com`  [HTTP 200]
- `appstats.alfaview.com`  [HTTP unprobed]
- `assets.alfaview.com`  [HTTP 403]
- `beta-alfaview-assets.alfaview.com`  [HTTP 403]
- `beta-alfaview-com-assets.alfaview.com`  [HTTP 403]
- `beta-apis.alfaview.com`  [HTTP 200]
- `beta-app.alfaview.com`  [HTTP 401]
- `beta-hcloud-19-beta-audio-4xstl.alfaview.com`  [HTTP 404]
- `beta-hcloud-19-beta-audio-8xnz9.alfaview.com`  [HTTP 404]
- `beta-hcloud-19-beta-engine-9m62k.alfaview.com`  [HTTP 404]
- `beta-hcloud-19-beta-engine-vlhwd.alfaview.com`  [HTTP 404]
- `beta-hcloud-19-beta-hydra-dzwx8.alfaview.com`  [HTTP 200]
- `beta-hcloud-19-beta-video-xjl6p.alfaview.com`  [HTTP 404]
- `beta-hcloud-19-beta-video-zhjvl.alfaview.com`  [HTTP 404]
- `beta-ionoscloud-21-beta-audio-65st7.alfaview.com`  [HTTP unprobed]
- `beta-ionoscloud-21-beta-audio-bdtmf.alfaview.com`  [HTTP unprobed]
- `beta-ionoscloud-21-beta-engine-gw4qw.alfaview.com`  [HTTP unprobed]
- `beta-ionoscloud-21-beta-engine-kzmvv.alfaview.com`  [HTTP unprobed]
- `beta-ionoscloud-21-beta-hydra-7x5d5.alfaview.com`  [HTTP unprobed]
- `beta-ionoscloud-21-beta-video-6pp2m.alfaview.com`  [HTTP unprobed]
- `beta-ionoscloud-21-beta-video-l5mbv.alfaview.com`  [HTTP unprobed]
- `beta-noris-33-beta-audio-ljj9x.alfaview.com`  [HTTP 404]
- `beta-noris-33-beta-audio-xr4mw.alfaview.com`  [HTTP 404]
- `beta-noris-33-beta-engine-cr5rs.alfaview.com`  [HTTP 404]
- `beta-noris-33-beta-engine-w84qw.alfaview.com`  [HTTP 404]
- `beta-noris-33-beta-hydra-2zm7t.alfaview.com`  [HTTP 200]
- `beta-noris-33-beta-video-2f9jw.alfaview.com`  [HTTP 404]
- `beta-noris-33-beta-video-vbvn5.alfaview.com`  [HTTP 404]
- `beta-ovh-29-beta-audio-j5qgh.alfaview.com`  [HTTP 404]
- `beta-ovh-29-beta-audio-vldgf.alfaview.com`  [HTTP 404]
- `beta-ovh-29-beta-engine-dxvt4.alfaview.com`  [HTTP 404]
- `beta-ovh-29-beta-engine-k7khj.alfaview.com`  [HTTP 404]
- `beta-ovh-29-beta-hydra-z4tf8.alfaview.com`  [HTTP 200]
- `beta-ovh-29-beta-video-8zvvm.alfaview.com`  [HTTP 404]
- `beta-ovh-29-beta-video-gbbw4.alfaview.com`  [HTTP 404]
- `beta-webclient.alfaview.com`  [HTTP 200]
- `bhc.alfaview.com`  [HTTP 200]
- `client-diagnostics-ingest.alfaview.com`  [HTTP 404]
- `clone.staging-wordpress.alfaview.com`  [HTTP 303]
- `consul-monitoring.alfaview.com`  [HTTP unprobed]
- `demo-company.alfaview.com`  [HTTP 200]
- `design-assets.alfaview.com`  [HTTP 404]
- `design-tokens.alfaview.com`  [HTTP 404]
- `equipment.alfaview.com`  [HTTP unprobed]
- `hello-world.atlas-spike.atlas.alfaview.com`  [HTTP 404]
- `insider-webclient.alfaview.com`  [HTTP 200]
- `internal.alfaview.com`  [HTTP 401]
- `ip-185-245-101-240.alfaview.com`  [HTTP unprobed]
- `kh-freiburg.alfaview.com`  [HTTP 200]

## 2026-09-03 11:40:30 UTC

## 2026-09-03 14:23:21 UTC

## 2026-09-03 15:21:33 UTC
- NEW 55 dedicated hosts confirmed after wildcard filtering (was 8 in initial recon) — inventory/alfaview.md:35-92
- NEW Probe result: `GET https://beta-apis.alfaview.com/v2/languages` (no auth) → HTTP 401; `GET https://apis.alfaview.com/v2/languages` → HTTP 404 — probe-results.md:6-8
- CHANGED Beta API weaker auth hypothesis **disproven** — beta returns 401 (endpoint exists, auth required), production returns 404 (endpoint missing) — version drift confirmed
- CHANGED Production API lacks `/v2/languages` endpoint present in beta — API version divergence
- CHANGED beta-apis.alfaview.com: Auth response identical to production (401 + same error body). Beta weaker auth hypothesis disconfirmed.
- NEW beta-webclient.alfaview.com (HTTP 200): High-value web client surface, untested.
- NEW insider-webclient.alfaview.com (HTTP 200): Internal tooling potentially exposed.

## 2026-09-03 18:48:37 UTC
- NEW OpenAPI specs at `https://apis.alfaview.com/v2/docs/openapi.json` and `https://beta-apis.alfaview.com/v2/docs/openapi.json` are **identical** (same endpoints, schemas, auth requirements) — confirms ve
- NEW `GET https://apis.alfaview.com/v2/languages` returns 404 (endpoint absent in production); `GET https://beta-apis.alfaview.com/v2/languages` returns 401 (endpoint exists, auth enforced) — confirmed API
- NEW OpenAPI spec exposes `DELETE /v2/users/{id}` and `PATCH/DELETE /v2/rooms/{roomId}/permissions/{userId}` with UUID path params — direct evidence for IDOR hypothesis
- NEW `demo-company.alfaview.com/api/v1/users` returns 302 redirect to `/` — no unauthenticated user enumeration
- CHANGED Beta API weaker auth hypothesis **fully disproven** — OpenAPI specs identical, both enforce auth identically
- CHANGED API version drift scope narrowed: only `/v2/languages` endpoint differs (beta has it, prod doesn't)

## 2026-09-03 21:22:00 UTC

## 2026-09-03 23:24:23 UTC
- CHANGED Production API `apis.alfaview.com/v2/languages` now returns **401** (was 404) — endpoint added to production, aligns with beta; OpenAPI specs now identical including `/v2/languages` path
- CHANGED Beta API `beta-apis.alfaview.com/v2/languages` returns **401** (consistent) — both environments now enforce auth identically on this endpoint
- NEW `insider-webclient.alfaview.com` and `beta-webclient.alfaview.com` both serve identical SPA shells (4396 bytes, same HTML structure, `/health`=204, `/api|/admin|/debug|/internal|/v2|/docs`=404) — no i
- NEW `demo-company.alfaview.com` serves SPA (HTTP 200) — unauthenticated web surface confirmed
- NEW 55 dedicated hosts confirmed after wildcard filtering; 48 remain HTTP-unprobed (e.g., `alfacheck-*`, `beta-hcloud-*`, `beta-ionoscloud-*`, `beta-noris-*`, `beta-ovh-*`, `consul-monitoring`, `equipment

## 2026-09-04 01:12:26 UTC
- NEW Production API `apis.alfaview.com/v2/languages` now returns **401** (was 404) — endpoint added to production, aligns with beta; OpenAPI specs now identical including `/v2/languages` path
- NEW `insider-webclient.alfaview.com` and `beta-webclient.alfaview.com` both serve identical SPA shells (4396 bytes, same HTML structure, `/health`=204, `/api|/admin|/debug|/internal|/v2|/docs`=404) — no i
- NEW `demo-company.alfaview.com` serves SPA (HTTP 200) — unauthenticated web surface confirmed
- NEW 55 dedicated hosts confirmed after wildcard filtering; 48 remain HTTP-unprobed (e.g., `alfacheck-*`, `beta-hcloud-*`, `beta-ionoscloud-*`, `beta-noris-*`, `beta-ovh-*`, `consul-monitoring`, `equipment
- NEW Live HTTP 200 on previously unprobed: `alfatraining.alfaview.com`, `bhc.alfaview.com`, `kh-freiburg.alfaview.com`, `beta-hcloud-19-beta-hydra-dzwx8.alfaview.com`, `beta-noris-33-beta-hydra-2zm7t.alfav
- NEW `beta-app.alfaview.com` and `internal.alfaview.com` both return HTTP 401 (auth-gated)
- NEW `appstats.alfaview.com`, `consul-monitoring.alfaview.com`, `equipment.alfaview.com`, `ip-185-245-101-240.alfaview.com` timeout/unreachable
- CHANGED Beta API weaker auth hypothesis **fully disproven** — OpenAPI specs identical, both enforce auth identically (401 on `/v2/languages`)
- CHANGED API version drift **resolved** — both beta and production now expose `/v2/languages` with identical auth enforcement
- CHANGED Cross-tenant IDOR on room permissions and user deletion via UUID path params confirmed as highest-priority authenticated target (confidence 80)

## 2026-09-04 06:00:56 UTC
- CHANGED alfacheck-engine.alfaview.com: Was "HTTP unprobed" → now confirmed UNREACHABLE (3 timeout probes: root, /health, /status)
- CHANGED alfacheck-audio.alfaview.com: Was "HTTP unprobed" → now confirmed UNREACHABLE (3 timeout probes: root, /media, /recordings)
- CHANGED alfacheck-video.alfaview.com: Was "HTTP unprobed" → now confirmed UNREACHABLE (1 timeout probe: root)
- NEW beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: HTTP 200 with 9-byte body; trailing slash returns HTTP 400 — minimal surface, likely health-check endpoint

## 2026-09-04 10:12:15 UTC

## 2026-09-04 14:25:07 UTC
- CHANGED beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: Was ACCEPTED MISCONFIG (health-check surface) → now REJECTED after probing — "Hi Client" body on all paths confirms media/signaling server, not OIDC/auth 
- CHANGED beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — media server, REJECTED.
- CHANGED beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — media server, REJECTED.
- CHANGED All 3 alfacheck-* hosts: Confirmed UNREACHABLE via timeout (root, /health, /status, /media, /recordings). Internal/firewalled. REJECTED.
- CHANGED RISK score dropped from 58 → 55 — all hydra/alfacheck surfaces resolved (rejected). Remaining surface is auth-gated.
- CHANGED beta-hcloud-19-beta-hydra-dzwx8.alfaview.com: ACCEPTED MISCONFIG → REJECTED — media/signaling server ("Hi Client"), not OIDC.
- CHANGED beta-noris-33-beta-hydra-2zm7t.alfaview.com: Same — REJECTED.
- CHANGED beta-ovh-29-beta-hydra-z4tf8.alfaview.com: Same — REJECTED.
- CHANGED alfacheck-engine.alfaview.com: UNREACHABLE confirmed (3 timeout probes).
- CHANGED alfacheck-audio.alfaview.com: UNREACHABLE confirmed (3 timeout probes).
- CHANGED alfacheck-video.alfaview.com: UNREACHABLE confirmed (1 timeout probe).
- NEW Production API `apis.alfaview.com/v2/languages` now returns 401 (was 404) — endpoint added, aligns with beta; OpenAPI specs identical
- NEW `beta-hcloud-19-beta-hydra-dzwx8.alfaview.com`: HTTP 200 with 9-byte body ("Hi Client"); trailing slash returns HTTP 400 — minimal health-check surface
- NEW Guest link auth flow updated to 4-field combo (companyId+roomId+accessKey+displayName) — rate-limit status unknown
- CHANGED API version drift **resolved** — both beta and production expose `/v2/languages` with identical auth enforcement (401)
- CHANGED `alfacheck-engine/audio/video.alfaview.com`: confirmed UNREACHABLE (timeout probes) — target exhausted
- CHANGED `beta-hcloud-19-beta-hydra-dzwx8.alfaview.com` et al: confirmed media/signaling servers ("Hi Client") — no admin endpoints

## 2026-09-04 17:51:04 UTC

## 2026-09-04 20:04:20 UTC
- NEW beta-ionoscloud-21-* fleet (7 hosts): `beta-ionoscloud-21-beta-audio-65st7`, `beta-ionoscloud-21-beta-audio-bdtmf`, `beta-ionoscloud-21-beta-engine-gw4qw`, `beta-ionoscloud-21-beta-engine-kzmvv`, `bet
- NEW Main domain set from initial recon (6 hosts): `alfaview.com`, `app.alfaview.com`, `dev.alfaview.com`, `sso.alfaview.com`, `test.alfaview.com`, `www.alfaview.com` — only `support`/`staging` probed (301
- CHANGED `apis.alfaview.com/v2/languages` now 401 (was 404) — endpoint added, aligns with beta; OpenAPI specs identical
- CHANGED `alfatraining`/`bhc`/`kh-freiburg` XSS hypothesis REJECTED — byte-identical SPA shells (1381B, MD5 554a39), no tenant-specific rendering

## 2026-09-04 22:09:53 UTC
- NEW beta-ionoscloud-21-beta-hydra-7x5d5.alfaview.com: HTTP timeout (root + trailing slash) — unlike hcloud/noris/ovh hydra hosts which returned "Hi Client"
- NEW alfaview.com: HTTP 200 — main marketing/auth entry point now confirmed live
- NEW sso.alfaview.com: HTTP 200 len=0 — SSO endpoint live, empty body (likely redirects or SPA shell)
- CHANGED beta-ionoscloud-21-* fleet (7 hosts): Previously "HTTP unprobed" → now 1/7 probed (hydra=timeout), 6 remain unprobed
- CHANGED Main domain set (6 hosts): Previously only support/staging probed (301) → now alfaview.com + sso.alfaview.com confirmed HTTP 200, 4 remain unprobed (app, dev, test, www)

## 2026-09-05 00:15:07 UTC

## 2026-09-05 04:34:13 UTC
- NEW sso.alfaview.com: FusionAuth 1.63.0 OIDC discovery live (issuer=acme.com, implicit flow, HS256 in supported algs but RSA-only JWKS) — confirmed 2026-09-05 00:15
- NEW test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079, 4 platforms) — no integrity verification visible
- NEW alfaview.com: Marketing page live (301→/en, 177KB, strict CSP, matomo) — no SSO login links on marketing domain
- CHANGED beta-ionoscloud-21-beta-engine-* (2 hosts): Confirmed timeout (000) — internal/firewalled like alfacheck-* fleet
- CHANGED alfaview.com: Root now redirects to /en with nginx + Accept-Language vary header
- CHANGED www.alfaview.com: 301→alfaview.com/en (no independent surface)

## 2026-09-05 08:50:59 UTC
- NEW app.alfaview.com/js/app.min.5b3949112f0cf682adc8.js (1.09MB) leaks FULL admin/company GraphQL schema: 60+ mutations + 45+ queries with exact args. Not just SDL strings — includes CreateCompany($compan
- NEW GraphQL live at app.alfaview.com/graphql: __typename OK unauth; introspection disabled; sensitive ops return UNAUTHENTICATED. Resolver auth is INCONSISTENT: listIdentityProviders returns data unauth (
- NEW CRITICAL DELTA: GraphQL guest path guestAuthenticate(userId,companyId,roomId) and guestJoin(userId,companyId,roomId,displayName) take NO accessKey — while REST guest-link flow requires 4-field combo i
- NEW Hosts: webclient.alfaview.com=200/4396B(insider family); staging-webclient.alfaview.com=401 Basic realm=Protected (/health=204); plausible.alfaview.com=204; production-alfaview-assets/staging-alfaview
- NEW sso.alfaview.com: FusionAuth 1.63.0 OIDC discovery confirmed with issuer=acme.com (misconfiguration), implicit flow enabled, HS256/HS384/HS512 in id_token_signing_alg_values_supported but JWKS contain
- NEW test.alfaview.com: Unauthenticated binary distribution confirmed (alfacheck v470079, 4 platforms: linux/amd64, windows/amd64, mac/amd64, mac/arm64) — statically linked ELF, no integrity hashes/signatu
- NEW app.alfaview.com: SPA loads from alfaview-com-assets.alfaview.com; no client_id discoverable in HTML/JS bundles; common client_id patterns (alfaview, app, web, client, spa, alfaview.com, app.alfaview.
- CHANGED beta-app.alfaview.com: HTTP 401 with WWW-Authenticate: Basic (not OAuth) — different auth mechanism than main app
- CHANGED alfaview.com: Marketing page now 301→/en with nginx + Accept-Language vary, 177KB, strict CSP, matomo analytics — no SSO login links
- CHANGED www.alfaview.com: 301→alfaview.com/en (no independent surface)

## 2026-09-05 12:20:10 UTC

## 2026-09-05 15:02:37 UTC

## 2026-09-05 17:06:17 UTC
- NEW app.alfaview.com/js/AppSignup.min.3329eeac503c038b44b8.js (48KB lazy chunk) recovered: SIGNUP IS UNAUTHENTICATED — Signup action sends NO token header (vs CreateCompany which sends headers:{token:s}).
- NEW app.alfaview.com/js/AppFinishSignup.min.c05a2146b0620d1167ae.js recovered: activation is EMAIL-GATED — finishSignup({companyId, username, activationToken, password}) pulled from URL route /finish-sign
- NEW GraphQL client posts to /graphql with credentials:"include" (Apollo), authenticated ops use a token header (not Authorization/Bearer); Signup/FinishSignup send NO token → anonymous reachable. No CSRF 
- NEW sso.alfaview.com: FusionAuth 1.63.0 OIDC discovery confirmed — issuer=acme.com (misconfiguration vs alfaview.com), implicit flow enabled, HS256/HS384/HS512 in id_token_signing_alg_values_supported but
- NEW app.alfaview.com/graphql: Full admin GraphQL schema leaked in public JS bundle (1.09MB) — 60+ mutations, 45+ queries with exact args; introspection disabled but resolver auth INCONSISTENT: listIdentit
- NEW app.alfaview.com/graphql: guestAuthenticate(userId,companyId,roomId) and guestJoin(userId,companyId,roomId,displayName) mutations reachable unauthenticated (BAD_USER_INPUT not UNAUTHENTICATED) — NO ac
- NEW test.alfaview.com: Unauthenticated binary distribution confirmed — alfacheck v470079 for 4 platforms (linux/amd64, windows/amd64, mac/amd64, mac/arm64), statically linked ELF, no integrity hashes/sign
- NEW alfaview.com: Marketing page now 301→/en (nginx, Accept-Language vary), 177KB, strict CSP, matomo analytics — no unauthenticated SSO login links on marketing domain
- NEW www.alfaview.com: 301→alfaview.com/en (no independent surface)
- NEW beta-ionoscloud-21-beta-engine-* (2 hosts): Confirmed timeout (000) — internal/firewalled like alfacheck-* fleet
- NEW beta-ionoscloud-21 fleet: 7 hosts total, only 1/7 probed (hydra=timeout), 6 remain unprobed
- CHANGED apis.alfaview.com/v2/languages: Now returns 401 (was 404) — endpoint added to production, aligns with beta; OpenAPI specs now identical including /v2/languages
- CHANGED beta-app.alfaview.com: HTTP 401 with WWW-Authenticate: Basic realm — different auth mechanism than main app (OAuth)
- CHANGED alfacheck-engine/audio/video.alfaview.com: Confirmed UNREACHABLE via timeout probes — target exhausted
- CHANGED beta-hcloud-19-beta-hydra-dzwx8 / beta-noris-33-beta-hydra-2zm7t / beta-ovh-29-beta-hydra-z4tf8: All confirmed media/signaling servers ("Hi Client" on all paths) — not OIDC/auth infrastructure, target
- CHANGED alfatraining/bhc/kh-freiburg.alfaview.com: XSS hypothesis REJECTED — all three serve byte-identical generic SPA shell (1381B, MD5 554a39), no tenant-specific rendering, no reflections
- CHANGED insider-webclient.alfaview.com / beta-webclient.alfaview.com: SPA shell only (4396B), /health=204, all admin/debug paths 404 — targets exhausted
- CHANGED demo-company.alfaview.com: SPA catch-all confirmed — /api/v1/users returns identical HTML shell as root, no unauthenticated data exposure

## 2026-09-05 18:56:58 UTC
- NEW app.alfaview.com: Signup mutation unauthenticated (AppSignup.min.js lazy chunk) — sends NO token header; activation email-gated via finishSignup({companyId,username,activationToken,password}) at /fini
- NEW app.alfaview.com/graphql: Apollo client uses credentials:"include" + custom token header (not Authorization/Bearer); Signup/FinishSignup send NO token → anonymous reachable; no CSRF token observed
- NEW sso.alfaview.com: FusionAuth 1.63.0 OIDC discovery confirmed — issuer=acme.com (misconfiguration vs alfaview.com), implicit flow enabled, HS256/HS384/HS512 in id_token_signing_alg_values_supported but
- NEW app.alfaview.com/graphql: Full admin GraphQL schema leaked in public JS bundle (1.09MB) — 60+ mutations, 45+ queries with exact args; introspection disabled but resolver auth INCONSISTENT (listIdentit
- NEW app.alfaview.com/graphql: guestAuthenticate(userId,companyId,roomId) and guestJoin(userId,companyId,roomId,displayName) mutations reachable unauthenticated (BAD_USER_INPUT not UNAUTHENTICATED) — NO ac
- NEW test.alfaview.com: Unauthenticated binary distribution confirmed — alfacheck v470079 for 4 platforms (linux/amd64, windows/amd64, mac/amd64, mac/arm64), statically linked ELF, no integrity hashes/sign
- CHANGED beta-ionoscloud-21-* fleet: 7 hosts total, only 1/7 probed (beta-ionoscloud-21-beta-hydra-7x5d5 = timeout); beta-engine-* (2 hosts) confirmed timeout (000) — internal/firewalled like alfacheck-* fleet
- CHANGED alfaview.com: Root 301→/en (nginx, Accept-Language vary), /en 177KB marketing page with strict CSP and matomo; no unauthenticated SSO login links on marketing domain
- CHANGED www.alfaview.com: 301→alfaview.com/en (no independent surface)
- CHANGED apis.alfaview.com/v2/languages: Now returns 401 (was 404) — endpoint added to production, aligns with beta; OpenAPI specs now identical including /v2/languages
- CHANGED beta-app.alfaview.com: HTTP 401 with WWW-Authenticate: Basic realm — different auth mechanism than main app (OAuth)
- CHANGED alfacheck-engine/audio/video.alfaview.com: Confirmed UNREACHABLE via timeout probes — target exhausted
- CHANGED beta-hcloud-19-beta-hydra-dzwx8 / beta-noris-33-beta-hydra-2zm7t / beta-ovh-29-beta-hydra-z4tf8: All confirmed media/signaling servers ("Hi Client" on all paths) — not OIDC/auth infrastructure, target
- CHANGED alfatraining/bhc/kh-freiburg.alfaview.com: XSS hypothesis REJECTED — all three serve byte-identical generic SPA shell (1381B, MD5 554a39), no tenant-specific rendering, no reflections
- CHANGED insider-webclient.alfaview.com / beta-webclient.alfaview.com: SPA shell only (4396B), /health=204, all admin/debug paths 404 — targets exhausted
- CHANGED demo-company.alfaview.com: SPA catch-all confirmed — /api/v1/users returns identical HTML shell as root, no unauthenticated data exposure

## 2026-09-05 20:53:46 UTC
- NEW app.alfaview.com/js/app.min.5b3949112f0cf682adc8.js (1.09MB) leaks FULL admin/company GraphQL schema: 60+ mutations + 45+ queries with exact args. Not just SDL strings — includes CreateCompany($compan
- NEW GraphQL live at app.alfaview.com/graphql: __typename OK unauth; introspection disabled; sensitive ops return UNAUTHENTICATED. Resolver auth is INCONSISTENT: listIdentityProviders returns data unauth (
- NEW CRITICAL DELTA: GraphQL guest path guestAuthenticate(userId,companyId,roomId) and guestJoin(userId,companyId,roomId,displayName) take NO accessKey — while REST guest-link flow requires 4-field combo i
- NEW Hosts: webclient.alfaview.com=200/4396B(insider family); staging-webclient.alfaview.com=401 Basic realm=Protected (/health=204); plausible.alfaview.com=204; production-alfaview-assets/staging-alfaview
- NEW app.alfaview.com/js/AppSignup.min.3329eeac503c038b44b8.js (48KB lazy chunk) recovered: SIGNUP IS UNAUTHENTICATED — Signup action sends NO token header (vs CreateCompany which sends headers:{token:s}).
- NEW app.alfaview.com/js/AppFinishSignup.min.c05a2146b0620d1167ae.js recovered: activation is EMAIL-GATED — finishSignup({companyId, username, activationToken, password}) pulled from URL route /finish-sign
- NEW GraphQL client posts to /graphql with credentials:"include" (Apollo), authenticated ops use a token header (not Authorization/Bearer); Signup/FinishSignup send NO token → anonymous reachable. No CSRF 
- NEW sso.alfaview.com: FusionAuth 1.63.0 OIDC discovery live (issuer=acme.com misconfiguration, implicit flow enabled, HS256/HS384/HS512 in supported algs but RSA-only JWKS) — 2026-09-05 00:15
- NEW test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079, 4 platforms: linux/amd64, windows/amd64, mac/amd64, mac/arm64), statically linked ELF, no integrity hashes/signatures visible
- NEW app.alfaview.com: Full admin GraphQL schema leaked in public JS bundle (1.09MB, 60+ mutations, 45+ queries); resolver auth INCONSISTENT (listIdentityProviders returns data unauth, listComponents unaut
- NEW app.alfaview.com: Signup mutation unauthenticated (AppSignup.min.js lazy chunk) — sends NO token header; activation email-gated via finishSignup at /finish-signup route; Apollo client uses credentials
- NEW alfaview.com: Marketing page live (301→/en, nginx, Accept-Language vary, 177KB, strict CSP, matomo) — no unauthenticated SSO login links — 2026-09-05 04:34
- NEW www.alfaview.com: 301→alfaview.com/en (no independent surface) — 2026-09-05 04:34
- NEW beta-ionoscloud-21-beta-engine-* (2 hosts): Confirmed timeout (000) — internal/firewalled like alfacheck-* fleet; beta-ionoscloud-21 fleet 7 hosts total, only 1/7 probed (hydra=timeout), 6 remain unpr
- CHANGED apis.alfaview.com/v2/languages: Now returns 401 (was 404) — endpoint added to production, aligns with beta; OpenAPI specs now identical including /v2/languages — 2026-09-05 04:34
- CHANGED beta-app.alfaview.com: HTTP 401 with WWW-Authenticate: Basic realm — different auth mechanism than main app (OAuth) — 2026-09-05 04:34
- CHANGED alfatraining/bhc/kh-freiburg.alfaview.com: XSS hypothesis REJECTED — all three serve byte-identical generic SPA shell (1381B, MD5 554a39), no tenant-specific rendering, no reflections — targets exhaus

## 2026-09-05 22:28:21 UTC

## 2026-09-06 00:15:48 UTC

## 2026-09-06 04:41:59 UTC

## 2026-09-06 08:57:09 UTC

## 2026-09-06 12:28:54 UTC

## 2026-09-06 16:02:35 UTC
- NEW beta-ionoscloud-21 fleet (7/7) fully probed: beta-ionoscloud-21-beta-audio-65st7/bdtmf and -beta-video-6pp2m/l5mbv all timeout (000, 12s) — resolves to real distinct IPs (185.127.30.215/.225). Last un
- CHANGED Inventory now 100% probed: all 55 dedicated hosts have an HTTP verdict; zero genuinely-unprobed hosts remain.

## 2026-09-06 18:09:34 UTC

## 2026-09-06 19:58:58 UTC
- NEW beta-ionoscloud-21 fleet (7/7) fully probed — all 4 audio/video + 2 engine + 1 hydra hosts timeout (000, 12s), resolves to real IPs 185.127.30.215/.225; firewalled like alfacheck-* fleet. Inventory no
- CHANGED app.alfaview.com/graphql: anonymous resolver slice confirmed closed — listIdentityProviders returns `[]`, listComponents errors 500, searchCompanies/generateFileDownloadURL return UNAUTHENTICATED; no 
- CHANGED app.alfaview.com/graphql: guestAuthenticate/guestJoin reconfirmed anonymous-reachable (BAD_USER_INPUT) — accessKey-less GraphQL guest path diverging from REST 4-field combo remains highest-structural 
- CHANGED app.alfaview.com/graphql: field-oracle enumeration discloses reply-type field names (providers [JSONObject], http_download_url, GetPendingUserAccount(userId)) — broader schema-surface mapping.
- CHANGED sso.alfaview.com: OIDC discovery unchanged — issuer=acme.com (misconfiguration), implicit flow enabled, HS256/384/512 in supported algs but JWKS contains ONLY RSA keys (7 RS256 keys, zero symmetric). 
- CHANGED test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079, 4 platforms) still live, no integrity verification visible.
- CHANGED apis.alfaview.com: Access tokens confirmed opaque/base64 (distinct 401 "No base64 encoded access token was provided") — JWT alg-confusion against API gateway closed.

## 2026-09-06 21:51:20 UTC
- CHANGED sso.alfaview.com: OIDC discovery re-fetched today — no `registration_endpoint` advertised → FusionAuth dynamic client registration OFF; GET `/oauth2/register` = 6.2KB generic "Login | FusionAuth" them
- CHANGED app.alfaview.com/graphql: guestAuthenticate reconfirmed anonymous-reachable (~1 rps) — mutation processed, my field guess returned `GRAPHQL_VALIDATION_FAILED` ("Did you mean `role`?") not UNAUTHENTICA

## 2026-09-06 23:21:54 UTC
- NEW sso.alfaview.com: OIDC discovery re-fetched — no `registration_endpoint` advertised → FusionAuth dynamic client registration OFF; GET `/oauth2/register` returns 6.2KB generic "Login | FusionAuth" them
- NEW app.alfaview.com/graphql: guestAuthenticate reconfirmed anonymous-reachable (~1 rps) — mutation processed, field guess returned `GRAPHQL_VALIDATION_FAILED` ("Did you mean `role`?") not `UNAUTHENTICATE
- CHANGED Inventory 100% probed: all 55 dedicated hosts have HTTP verdict; zero genuinely-unprobed hosts remain (beta-ionoscloud-21 fleet fully timeout)
- CHANGED app.alfaview.com/graphql: anonymous resolver slice confirmed closed — `listIdentityProviders` returns `[]`, `listComponents` errors 500, `searchCompanies`/`generateFileDownloadURL` return `UNAUTHENTIC
- CHANGED sso.alfaview.com: OIDC discovery unchanged — issuer=acme.com (misconfiguration), implicit flow enabled, HS256/384/512 in supported algs but JWKS contains ONLY RSA keys (7 RS256 keys, zero symmetric)
- CHANGED test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079, 4 platforms) still live, no integrity verification visible
- CHANGED apis.alfaview.com: Access tokens confirmed opaque/base64 (distinct 401 "No base64 encoded access token was provided") — JWT alg-confusion against API gateway closed

## 2026-09-07 01:08:39 UTC
- NEW sso.alfaview.com/oauth2/authorize now returns HTTP 404 on root path (was 200 len=0) — endpoint behavior changed, may indicate deployment update
- NEW apis.alfaview.com/v2/users/{foreign-uuid} returns HTTP 405 (Method Not Allowed) — DELETE method not allowed on user endpoint without auth; PATCH /v2/rooms/{roomId}/permissions/{userId} returns 401
- CHANGED sso.alfaview.com OIDC discovery unchanged — issuer=acme.com, implicit flow enabled, HS256/384/512 in supported algs but JWKS contains ONLY 7 RSA keys (zero symmetric)
- CHANGED app.alfaview.com/graphql guestAuthenticate/guestJoin reconfirmed anonymous-reachable (returns GRAPHQL_VALIDATION_FAILED "Did you mean `role`?" not UNAUTHENTICATED) — accessKey-less GraphQL guest path 
- CHANGED Inventory 100% probed: all 55 dedicated hosts have HTTP verdict; zero unprobed hosts remain (beta-ionoscloud-21 fleet fully timeout)
- CHANGED apis.alfaview.com access tokens confirmed opaque/base64 (distinct 401 "No base64 encoded access token was provided") — JWT alg-confusion against API gateway closed
- CHANGED test.alfaview.com unauthenticated binary distribution (alfacheck v470079, 4 platforms) still live, no integrity verification visible

## 2026-09-07 06:09:48 UTC

## 2026-09-07 12:48:25 UTC
- NEW beta-ionoscloud-21 fleet (7/7) fully probed: beta-ionoscloud-21-beta-audio-65st7/bdtmf and -beta-video-6pp2m/l5mbv all timeout (000, 12s) — resolves to real distinct IPs (185.127.30.215/.225). Last un
- CHANGED Inventory now 100% probed: all 55 dedicated hosts have an HTTP verdict; zero genuinely-unprobed hosts remain.
- CHANGED sso.alfaview.com: OIDC discovery re-fetched today — no `registration_endpoint` advertised → FusionAuth dynamic client registration OFF; GET `/oauth2/register` = 6.2KB generic "Login | FusionAuth" them
- CHANGED app.alfaview.com/graphql: guestAuthenticate reconfirmed anonymous-reachable (~1 rps) — mutation processed, my field guess returned `GRAPHQL_VALIDATION_FAILED` ("Did you mean `role`?") not UNAUTHENTICA
- CHANGED sso.alfaview.com/oauth2/authorize: Now returns HTTP 200 (was 404 on root path) — endpoint behavior changed, may indicate deployment update; OIDC discovery unchanged (issuer=acme.com, implicit flow, HS
- CHANGED apis.alfaview.com/v2/users/{foreign-uuid}: Returns HTTP 405 (Method Not Allowed) — DELETE not allowed without auth; PATCH /v2/rooms/{roomId}/permissions/{userId} returns 401.
- CHANGED Inventory: 100% probed — all 55 dedicated hosts have HTTP verdict; zero unprobed hosts remain (beta-ionoscloud-21 fleet fully timeout).
- CHANGED sso.alfaview.com/oauth2/authorize: Now returns HTTP 200 (was 404 on root path) — endpoint behavior changed, may indicate deployment update; OIDC discovery unchanged (issuer=acme.com, implicit flow, HS
- CHANGED apis.alfaview.com/v2/users/{foreign-uuid}: Returns HTTP 405 (Method Not Allowed) — DELETE not allowed without auth; PATCH /v2/rooms/{roomId}/permissions/{userId} returns 401.
- CHANGED Inventory: 100% probed — all 55 dedicated hosts have HTTP verdict; zero unprobed hosts remain (beta-ionoscloud-21 fleet fully timeout).

## 2026-09-07 18:00:22 UTC
- CHANGED sso.alfaview.com/oauth2/authorize: Now returns HTTP 200 (was 404 on root path) — endpoint behavior changed, may indicate deployment update; OIDC discovery unchanged (issuer=acme.com, implicit flow ena
- CHANGED apis.alfaview.com/v2/users/{foreign-uuid}: Returns HTTP 405 (Method Not Allowed) — DELETE not allowed without auth; PATCH /v2/rooms/{roomId}/permissions/{userId} returns 401.
- CHANGED Inventory: 100% probed — all 55 dedicated hosts have HTTP verdict; zero unprobed hosts remain (beta-ionoscloud-21 fleet fully timeout).

## 2026-09-07 21:00:34 UTC
- CHANGED sso.alfaview.com/oauth2/authorize: Now returns HTTP 200 with FusionAuth login page HTML (was 404/empty then invalid_client error) — endpoint behavior changed, now renders login page even for unregiste
- CHANGED apis.alfaview.com/v2/users/{uuid}: Returns HTTP 405 (Allow: DELETE) — DELETE method exists but requires auth (401 on PATCH permissions without token)

## 2026-09-07 23:07:47 UTC
- CHANGED sso.alfaview.com/oauth2/authorize: Now returns HTTP 200 with FusionAuth login page HTML for unregistered client_id (was 404 → invalid_client error) — endpoint behavior changed, now renders login page 
- CHANGED apis.alfaview.com/v2/users/{uuid}: Returns HTTP 405 (Allow: DELETE) — DELETE method exists but requires auth (401 on PATCH permissions without token); OpenAPI spec confirmed identical beta/prod

## 2026-09-08 01:17:28 UTC

## 2026-09-08 06:04:11 UTC
- NEW sso.alfaview.com/oauth2/authorize behavioral change: now returns HTTP 200 FusionAuth login page (~6189B) for unregistered client_id (was 404 → invalid_client error) — validation timing shifted, may in
- NEW sso.alfaview.com OIDC discovery reconfirmed: issuer=acme.com (not alfaview.com), implicit flow enabled, HS256/384/512 in id_token_signing_alg_values_supported but JWKS contains ONLY 7 RSA keys (zero s
- CHANGED app.alfaview.com/graphql: guestAuthenticate/guestJoin reconfirmed anonymous-reachable (BAD_USER_INPUT, not UNAUTHENTICATED) — accessKey-less GraphQL guest path diverging from REST 4-field combo remain
- CHANGED apis.alfaview.com/v2: Cross-tenant IDOR via UUID path params (DELETE /v2/users/{id}, PATCH /v2/rooms/{roomId}/permissions/{userId}) confirmed in identical beta/prod OpenAPI — requires authenticated ac
- CHANGED test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079, 4 platforms) still live, no integrity verification visible — supply chain risk
- CHANGED Inventory 100% probed: all 55 dedicated hosts have HTTP verdict; zero genuinely-unprobed hosts remain (beta-ionoscloud-21 fleet fully timeout)
- CHANGED apis.alfaview.com: Access tokens confirmed opaque/base64 (distinct 401 "No base64 encoded access token was provided") — JWT alg-confusion against API gateway closed

## 2026-09-08 10:36:18 UTC
- NEW sso.alfaview.com/oauth2/authorize now returns HTTP 200 FusionAuth login page (~6189B) for unregistered client_id (was 404 → invalid_client error) — validation timing shifted, may indicate FusionAuth c
- NEW sso.alfaview.com OIDC discovery reconfirmed: issuer=acme.com (not alfaview.com), implicit flow enabled, HS256/384/512 in id_token_signing_alg_values_supported but JWKS contains ONLY 7 RSA keys (zero s
- CHANGED app.alfaview.com/graphql: guestAuthenticate/guestJoin reconfirmed anonymous-reachable (BAD_USER_INPUT, not UNAUTHENTICATED) — accessKey-less GraphQL guest path diverging from REST 4-field combo remain
- CHANGED apis.alfaview.com/v2: Cross-tenant IDOR via UUID path params (DELETE /v2/users/{id}, PATCH /v2/rooms/{roomId}/permissions/{userId}) confirmed in identical beta/prod OpenAPI — requires authenticated ac
- CHANGED test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079, 4 platforms) still live, no integrity verification visible — supply chain risk
- CHANGED Inventory 100% probed: all 55 dedicated hosts have HTTP verdict; zero genuinely-unprobed hosts remain (beta-ionoscloud-21 fleet fully timeout)
- CHANGED apis.alfaview.com: Access tokens confirmed opaque/base64 (distinct 401 "No base64 encoded access token was provided") — JWT alg-confusion against API gateway closed

## 2026-09-08 14:53:43 UTC

## 2026-09-08 18:17:59 UTC
- NEW sso.alfaview.com/oauth2/authorize: Now returns HTTP 400 for unregistered client_id (was 200 login page) — validation timing still client_id-gated; redirect_uri matrix remains blocked without registere
- NEW apis.alfaview.com/v2/docs/openapi.json: Still public and byte-identical prod/beta (37 paths, MD5 357b94d3) — schema surface fully stable
- CHANGED sso.alfaview.com OIDC: issuer=acme.com misconfiguration persists; implicit flow + HS256/384/512 in supported algs but JWKS contains ONLY 7 RSA keys (zero symmetric) — alg confusion vector unchanged
- CHANGED app.alfaview.com/graphql: guestAuthenticate/guestJoin reconfirmed anonymous-reachable (BAD_USER_INPUT) — accessKey-less GraphQL guest path diverging from REST 4-field combo remains highest-structural 
- CHANGED apis.alfaview.com/v2: Cross-tenant IDOR via UUID path params (DELETE /v2/users/{id}, PATCH /v2/rooms/{roomId}/permissions/{userId}) confirmed in OpenAPI — requires authenticated account
- CHANGED test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079, 4 platforms) still live, no integrity verification — supply chain risk

## 2026-09-08 21:14:43 UTC
- NEW sso.alfaview.com/oauth2/authorize: Now returns HTTP 400 for unregistered client_id (was 200 login page) — validation timing still client_id-gated; redirect_uri matrix remains blocked without registere
- NEW apis.alfaview.com/v2/docs/openapi.json: Still public and byte-identical prod/beta (37 paths, MD5 357b94d3) — schema surface fully stable
- CHANGED sso.alfaview.com OIDC: issuer=acme.com misconfiguration persists; implicit flow + HS256/384/512 in supported algs but JWKS contains ONLY 7 RSA keys (zero symmetric) — alg confusion vector unchanged
- CHANGED app.alfaview.com/graphql: guestAuthenticate/guestJoin reconfirmed anonymous-reachable (BAD_USER_INPUT) — accessKey-less GraphQL guest path diverging from REST 4-field combo remains highest-structural 
- CHANGED apis.alfaview.com/v2: Cross-tenant IDOR via UUID path params (DELETE /v2/users/{id}, PATCH /v2/rooms/{roomId}/permissions/{userId}) confirmed in OpenAPI — requires authenticated account
- CHANGED test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079, 4 platforms) still live, no integrity verification — supply chain risk

## 2026-09-08 23:25:31 UTC
- NEW sso.alfaview.com/oauth2/authorize: Now returns HTTP 400 for unregistered client_id (was 200 login page) — validation timing still client_id-gated; redirect_uri matrix remains blocked without registere
- NEW apis.alfaview.com/v2/docs/openapi.json: Still public and byte-identical prod/beta (37 paths, MD5 357b94d3) — schema surface fully stable
- CHANGED sso.alfaview.com OIDC: issuer=acme.com misconfiguration persists; implicit flow + HS256/384/512 in supported algs but JWKS contains ONLY 7 RSA keys (zero symmetric) — alg confusion vector unchanged
- CHANGED app.alfaview.com/graphql: guestAuthenticate/guestJoin reconfirmed anonymous-reachable (BAD_USER_INPUT) — accessKey-less GraphQL guest path diverging from REST 4-field combo remains highest-structural 
- CHANGED apis.alfaview.com/v2: Cross-tenant IDOR via UUID path params (DELETE /v2/users/{id}, PATCH /v2/rooms/{roomId}/permissions/{userId}) confirmed in OpenAPI — requires authenticated account
- CHANGED test.alfaview.com: Unauthenticated binary distribution (alfacheck v470079, 4 platforms) still live, no integrity verification — supply chain risk

## 2026-09-09 01:31:29 UTC

## 2026-09-09 06:50:32 UTC
- CHANGED `/oauth2/introspect` — now confirmed broken client authentication: accepts ANY `client_id` value without validation (200 `{"active":false}` with `client_id=does-not-exist-12345`). Without `client_id` 
- CHANGED `/oauth2/token` with `client_credentials` grant → 400 `not_licensed` — FusionAuth Community edition, Entity Management feature not available. Confirms license tier.
- CHANGED `/v2/auth/password` REST endpoint — POST with `{username, password}` confirmed: 422 validates input schema, 401 `invalid credentials` for bad creds. Generic error (no username enumeration). Now confir
- NEW `apis.alfaview.com/v2/docs/openapi.json` still public and byte-identical prod/beta (37 paths, MD5 357b94d3) — schema surface fully stable this cycle
- NEW `sso.alfaview.com/oauth2/authorize` returns HTTP 400 for unregistered client_id (not 200 login page) — validation timing still client_id-gated; redirect_uri matrix remains blocked without registered c
- NEW `app.alfaview.com/graphql` guestAuthenticate/guestJoin reconfirmed anonymous-reachable (BAD_USER_INPUT) — accessKey-less GraphQL guest path diverging from REST 4-field combo remains highest-structural
- NEW `app.alfaview.com/graphql` field-oracle enumeration discloses reply-type field names (`providers` `[JSONObject]`, `http_download_url`, `GetPendingUserAccount(userId)`) — broader schema-surface mapping
- CHANGED `apis.alfaview.com/v2/users/{uuid}` returns HTTP 405 (Allow: DELETE) — DELETE method exists but requires auth (401 on PATCH permissions without token)
- CHANGED `sso.alfaview.com` OIDC discovery unchanged — issuer=acme.com, implicit flow enabled, HS256/384/512 in supported algs but JWKS contains ONLY 7 RSA keys (zero symmetric)
- CHANGED `test.alfaview.com` unauthenticated binary distribution (alfacheck v470079, 4 platforms) still live, no integrity verification visible
- CHANGED Inventory 100% probed: all 55 dedicated hosts have HTTP verdict; zero genuinely-unprobed hosts remain (beta-ionoscloud-21 fleet fully timeout)

## 2026-09-09 12:00:41 UTC
- CHANGED `app.alfaview.com/graphql`: `guestAuthenticate` with UUID-format args returns `FORBIDDEN` (not `BAD_USER_INPUT`) — mutation processes UUIDs and hits authorization, not type validation. Null args retur
- CHANGED `app.alfaview.com/graphql`: `GuestJoinReply` field oracle reveals `expiry` field (error: "Did you mean `expiry`?"). All other tested fields absent: accessToken, refreshToken, role, userId, companyId, 
- CHANGED `apis.alfaview.com/v2/auth/guest-link`: 3-field combo `{accessKey,companyId,roomId}` returns 422 `ACTION_INVALID` (same as 4-field with displayName) — `displayName` is **not** a required field per ser
- CHANGED `sso.alfaview.com/oauth2/authorize` now returns HTTP 200 FusionAuth login page (~6189B) for unregistered `client_id` (was 400) — validation timing shifted again overnight
- CHANGED `sso.alfaview.com/oauth2/introspect` accepts any `client_id` value without validation (200 `{"active":false}` with `client_id=does-not-exist-12345`) — broken client authentication on token introspecti
- CHANGED `sso.alfaview.com/oauth2/token` with `client_credentials` grant returns 400 `not_licensed` — FusionAuth Community edition (v1.63.0) confirmed, Entity Management not licensed

## 2026-09-09 15:42:12 UTC
- CHANGED `sso.alfaview.com/oauth2/authorize` returns HTTP 200 FusionAuth login page (~6189B) for unregistered `client_id` (was 400) — validation timing shifted again overnight
- CHANGED `sso.alfaview.com/oauth2/introspect` now requires `token` parameter (400 `missing_token` without it) — previously accepted any `client_id` without validation
- CHANGED `apis.alfaview.com/v2/auth/guest-link` REST endpoint confirms 3-field combo (`accessKey`+`companyId`+`roomId`) returns 422 `ACTION_INVALID` — `displayName` not required
- CHANGED `app.alfaview.com/graphql` `guestAuthenticate`/`guestJoin` reconfirmed anonymous-reachable (BAD_USER_INPUT with zero UUIDs) — accessKey-less GraphQL guest path diverges from REST 3-field combo

## 2026-09-09 18:53:03 UTC
- CHANGED `sso.alfaview.com/oauth2/authorize` returns HTTP 200 FusionAuth login page (~6160B) for unregistered `client_id` (was 400) — validation timing shifted again overnight
- CHANGED `sso.alfaview.com/oauth2/introspect` now requires `token` parameter (400 `missing_token` without it) — previously accepted any `client_id` without validation
- CHANGED `apis.alfaview.com/v2/auth/guest-link` REST endpoint confirms 3-field combo (`accessKey`+`companyId`+`roomId`) returns 422 `ACTION_INVALID` — `displayName` not required
- CHANGED `app.alfaview.com/graphql` `guestAuthenticate`/`guestJoin` reconfirmed anonymous-reachable (`BAD_USER_INPUT` with zero UUIDs) — accessKey-less GraphQL guest path diverges from REST 3-field combo

## 2026-09-09 21:21:16 UTC
- CHANGED `sso.alfaview.com/oauth2/authorize` returns HTTP 200 FusionAuth login page for unregistered `client_id` (was 400) — validation timing shifted again overnight
- CHANGED `sso.alfaview.com/oauth2/introspect` now requires `token` parameter (400 `missing_token` without it) — previously accepted any `client_id` without validation
- CHANGED `apis.alfaview.com/v2/auth/guest-link` REST endpoint confirms 3-field combo (`accessKey`+`companyId`+`roomId`) returns 422 `ACTION_INVALID` — `displayName` not required
- CHANGED `app.alfaview.com/graphql` `guestAuthenticate`/`guestJoin` reconfirmed anonymous-reachable (`BAD_USER_INPUT` with zero UUIDs) — accessKey-less GraphQL guest path diverges from REST 3-field combo

## 2026-09-09 23:20:30 UTC

## 2026-09-10 01:10:03 UTC
- CHANGED `sso.alfaview.com/oauth2/authorize` returns HTTP 200 FusionAuth login page for unregistered `client_id` (was 400) — validation timing shifted again overnight
- CHANGED `sso.alfaview.com/oauth2/introspect` now requires `token` parameter (400 `missing_token` without it) — previously accepted any `client_id` without validation
- CHANGED `apis.alfaview.com/v2/auth/guest-link` REST endpoint confirms 3-field combo (`accessKey`+`companyId`+`roomId`) returns 422 `ACTION_INVALID` — `displayName` not required
- CHANGED `app.alfaview.com/graphql` `guestAuthenticate`/`guestJoin` reconfirmed anonymous-reachable (`BAD_USER_INPUT` with zero UUIDs) — accessKey-less GraphQL guest path diverges from REST 3-field combo
- CHANGED OpenAPI specs prod/beta remain byte-identical (37 paths, MD5 357b94d367909a40b9299b543d23712b) — schema surface fully stable
- CHANGED Inventory 100% probed: all 55 dedicated hosts have HTTP verdict; zero unprobed hosts remain

## 2026-09-10 06:04:15 UTC
- CHANGED `sso.alfaview.com/oauth2/authorize` returns HTTP 200 FusionAuth login page for unregistered `client_id` (was 400) — validation timing shifted again overnight
- CHANGED `sso.alfaview.com/oauth2/introspect` now requires `token` parameter (400 `missing_token` without it) — previously accepted any `client_id` without validation
- CHANGED `apis.alfaview.com/v2/auth/guest-link` REST endpoint confirms 3-field combo (`accessKey`+`companyId`+`roomId`) returns 422 `ACTION_INVALID` — `displayName` not required
- CHANGED `app.alfaview.com/graphql` `guestAuthenticate`/`guestJoin` reconfirmed anonymous-reachable (`BAD_USER_INPUT` with zero UUIDs) — accessKey-less GraphQL guest path diverges from REST 3-field combo
- CHANGED OpenAPI specs prod/beta remain byte-identical (37 paths, MD5 357b94d367909a40b9299b543d23712b) — schema surface fully stable
- CHANGED Inventory 100% probed: all 55 dedicated hosts have HTTP verdict; zero unprobed hosts remain

## 2026-09-10 10:40:25 UTC
- CHANGED sso.alfaview.com/oauth2/introspect: HTTP Basic auth path accepts **fabricated client_id + any secret** → `{"active":false}` with no client validation; POST body `client_id=` → `invalid_client`. Client
- NEW sso.alfaview.com/.well-known/openid-configuration: `token_endpoint_auth_methods_supported=['client_secret_basic','client_secret_post','none']` — Basic scheme is an advertised auth method, making the i
- CHANGED `sso.alfaview.com/oauth2/authorize` returns HTTP 200 FusionAuth login page for unregistered `client_id` (was 400) — validation timing shifted again overnight
- CHANGED `sso.alfaview.com/oauth2/introspect` now requires `token` parameter (400 `missing_token` without it) — previously accepted any `client_id` without validation
- CHANGED `apis.alfaview.com/v2/auth/guest-link` REST endpoint confirms 3-field combo (`accessKey`+`companyId`+`roomId`) returns 422 `ACTION_INVALID` — `displayName` not required
- CHANGED `app.alfaview.com/graphql` `guestAuthenticate`/`guestJoin` reconfirmed anonymous-reachable (`BAD_USER_INPUT` with zero UUIDs) — accessKey-less GraphQL guest path diverges from REST 3-field combo
- CHANGED OpenAPI specs prod/beta remain byte-identical (37 paths, MD5 357b94d367909a40b9299b543d23712b) — schema surface fully stable
- CHANGED Inventory 100% probed: all 55 dedicated hosts have HTTP verdict; zero unprobed hosts remain

## 2026-09-10 14:51:53 UTC
- CHANGED `sso.alfaview.com/oauth2/authorize` returns HTTP 200 FusionAuth login page (6174B) for unregistered `client_id` — validation timing shifted again (was 400 `invalid_client`); redirect_uri matrix still 
- CHANGED `sso.alfaview.com/oauth2/introspect` HTTP Basic auth path accepts fabricated `client_id` + any secret → `200 {"active":false}`; POST body `client_id` validated → `400 invalid_client` — client authenti
- CHANGED `apis.alfaview.com/v2/auth/guest-link` REST endpoint confirms 3-field combo (`accessKey`+`companyId`+`roomId`) returns 422 `ACTION_INVALID` — `displayName` NOT required (prior 4-field claim incorrect)
- CHANGED `app.alfaview.com/graphql` `guestAuthenticate`/`guestJoin` reconfirmed anonymous-reachable (`BAD_USER_INPUT` with zero UUIDs) — accessKey-less GraphQL guest path diverges from REST 3-field combo
- CHANGED OpenAPI specs prod/beta remain byte-identical (37 paths, MD5 357b94d367909a40b9299b543d23712b) — schema surface fully stable 4th consecutive cycle
- CHANGED Inventory 100% probed: all 55 dedicated hosts have HTTP verdict; zero unprobed hosts remain

## 2026-09-10 18:00:00 UTC

## 2026-09-10 20:11:21 UTC
- CHANGED sso.alfaview.com/oauth2/introspect: POST-body `client_id` validation removed — fabricated client_id → 200 `{"active":false}` (was 400 `invalid_client`); Basic and POST-body channels now accept any cli
- CHANGED sso.alfaview.com OIDC discovery: now lists ES256/384/512 among `id_token_signing_alg_values_supported` (previous cycles: RSA+HS only); JWKS remains RSA-only (7 RS256 keys, zero symmetric/ECDSA)

## 2026-09-10 22:38:18 UTC
- NEW sso.alfaview.com/oauth2/introspect: POST-body `client_id` validation removed — fabricated client_id → 200 `{"active":false}` (was 400 `invalid_client`); Basic and POST-body channels now accept any cli
- NEW sso.alfaview.com OIDC discovery: now lists ES256/384/512 among `id_token_signing_alg_values_supported` (previous cycles: RSA+HS only); JWKS remains RSA-only (7 RS256 keys, zero symmetric/ECDSA)

## 2026-09-11 00:31:54 UTC

## 2026-09-11 05:14:39 UTC
- CHANGED beta-apis.alfaview.com: Auth response identical to production (401 + same error body). Beta weaker auth hypothesis disconfirmed.
- NEW beta-webclient.alfaview.com (HTTP 200): High-value web client surface, untested.
- NEW insider-webclient.alfaview.com (HTTP 200): Internal tooling potentially exposed.

## 2026-09-11 09:46:07 UTC

## 2026-09-11 14:02:27 UTC
- NEW sso.alfaview.com/oauth2/introspect: POST-body `client_id` validation removed — fabricated client_id → 200 `{"active":false}` (was 400 `invalid_client`); Basic and POST-body channels now accept any cli
- NEW sso.alfaview.com OIDC discovery: now lists ES256/384/512 among `id_token_signing_alg_values_supported` (previous cycles: RSA+HS only); JWKS remains RSA-only (7 RS256 keys, zero symmetric/ECDSA)

## 2026-09-11 17:24:48 UTC

## 2026-09-11 20:01:24 UTC

## 2026-09-11 22:20:38 UTC
- NEW NO_DELTA: all probes (OpenAPI MD5 357b94d3, OIDC discovery issuer=acme.com + ES256/HS256/RS256 algs, introspect Basic/POST-body both accept fake client_id, authorize 200 login page, GraphQL guestAuthe

## 2026-09-12 00:22:37 UTC

## 2026-09-12 04:46:37 UTC

## 2026-09-12 09:01:14 UTC
- NEW sso.alfaview.com/oauth2/introspect: POST-body `client_id` validation fully removed — fabricated client_id → 200 `{"active":false}` (was 400 `invalid_client`); HTTP Basic auth also accepts any client_i
- NEW sso.alfaview.com OIDC discovery: `id_token_signing_alg_values_supported` now lists ES256/384/512 alongside RSA+HS; JWKS unchanged (7 RS256 keys, zero symmetric/ECDSA) — alg confusion surface expanded 
- NEW sso.alfaview.com/oauth2/authorize: Returns HTTP 200 FusionAuth login page (~6173B) for unregistered client_id — validation timing shifted again (was 400); redirect_uri matrix still client_id-gated but

## 2026-09-12 12:31:51 UTC
- NEW sso.alfaview.com/oauth2/introspect: POST-body `client_id` validation fully removed — fabricated client_id → 200 `{"active":false}` (was 400 `invalid_client`); HTTP Basic auth also accepts any client_i
- NEW sso.alfaview.com OIDC discovery: `id_token_signing_alg_values_supported` now lists ES256/384/512 alongside RSA+HS; JWKS unchanged (7 RS256 keys, zero symmetric/ECDSA) — alg confusion surface expanded 
- NEW sso.alfaview.com/oauth2/authorize: Returns HTTP 200 FusionAuth login page (~6173B) for unregistered client_id — validation timing shifted again (was 400); redirect_uri matrix still client_id-gated but
- NEW client-diagnostics-ingest.alfaview.com: `/health`=200 `{"status":"ok"}` with strict headers (CSP default-src 'none', frame-ancestors 'none', JSON-only, edge-proxy) while all other GET paths return 39B
- NEW test.alfaview.com: alfacheck release bumped v470079→v483102 (4 platforms); index page still carries no sha256/signatures — supply-chain hardening absent across successive releases
- CHANGED apis.alfaview.com/v2/docs/openapi.json: Still public and byte-identical prod/beta (37 paths, MD5 357b94d367909a40b9299b543d23712b) — 5th+ consecutive stable cycle
- CHANGED app.alfaview.com/graphql: guestAuthenticate/guestJoin reconfirmed anonymous-reachable (BAD_USER_INPUT with zero UUIDs; GRAPHQL_VALIDATION_FAILED if displayName omitted) — accessKey-less GraphQL guest 
- CHANGED apis.alfaview.com: REST `/v2/auth/guest-link` requires only 3 fields (`accessKey`, `companyId`, `roomId`) — `displayName` NOT required (prior 4-field claim incorrect)
- CHANGED Inventory 100% probed: all 55 dedicated hosts have HTTP verdict; zero genuinely-unprobed hosts remain (beta-ionoscloud-21 fleet fully timeout)

## 2026-09-12 15:58:13 UTC
- CHANGED sso.alfaview.com/oauth2/jwks: Now returns 404 FusionAuth error page (was accessible with 7 RSA keys) — JWKS endpoint broken/removed
- CHANGED test.alfaview.com: alfacheck release bumped v470079→v483102 confirmed on index page (4 platforms); still no sha256/signatures
- CHANGED sso.alfaview.com/.well-known/openid-configuration: id_token_signing_alg_values_supported lists ES256/384/512 + HS256/384/512 + RS256/384/512; JWKS now 404 — alg confusion surface expanded in metadata 
- CHANGED sso.alfaview.com/oauth2/introspect: Both POST-body and HTTP Basic auth accept ANY client_id (fabricated) → 200 {"active":false}; only residual check is Basic-vs-body client_id_mismatch (401) — client 
- NEW client-diagnostics-ingest.alfaview.com/health: 200 {"status":"ok"} with strict CSP (default-src 'none', frame-ancestors 'none'), all other GET paths 39B JSON 404 — minimal POST-only ingest confirmed

## 2026-09-12 18:02:37 UTC
- NEW sso.alfaview.com/oauth2/jwks: Now returns 404 FusionAuth error page (was accessible with 7 RSA keys) — JWKS endpoint broken/removed
- NEW test.alfaview.com: alfacheck release bumped v470079→v483102 confirmed on index page (4 platforms); still no sha256/signatures
- CHANGED sso.alfaview.com/.well-known/openid-configuration: id_token_signing_alg_values_supported lists ES256/384/512 + HS256/384/512 + RS256/384/512; JWKS now 404 — alg confusion surface expanded in metadata 
- CHANGED sso.alfaview.com/oauth2/introspect: Both POST-body and HTTP Basic auth accept ANY client_id (fabricated) → 200 {"active":false}; only residual check is Basic-vs-body client_id_mismatch (401) — client 
- NEW client-diagnostics-ingest.alfaview.com/health: 200 {"status":"ok"} with strict CSP (default-src 'none', frame-ancestors 'none'), all other GET paths 39B JSON 404 — minimal POST-only ingest confirmed
