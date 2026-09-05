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
