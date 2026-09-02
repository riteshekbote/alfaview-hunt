# Alfaview — deep surface corpus (alfaview.com)

_Generated 2026-09-03 | wildcard-cleaned corpus | read-only passive_

## Cohort
- live HTTPS (GET /): 41 of 41 dedicated

- `alfatraining.alfaview.com`  [HTTP 200]  185.127.28.101,185.245.100.52,195.201.44.171
- `alfaview-com-assets.alfaview.com`  [HTTP 403]  185.127.28.101,185.245.100.52,195.201.44.171
- `apis.alfaview.com`  [HTTP 200]  185.127.28.101,185.245.100.52,195.201.44.171
- `app.alfaview.com`  [HTTP 200]  185.127.28.101,185.245.100.52,195.201.44.171
- `assets.alfaview.com`  [HTTP 403]  185.127.28.101,185.245.100.52,195.201.44.171
- `beta-alfaview-assets.alfaview.com`  [HTTP 403]  185.127.28.101,185.245.100.52,195.201.44.171
- `beta-alfaview-com-assets.alfaview.com`  [HTTP 403]  185.127.28.101,185.245.100.52,195.201.44.171
- `beta-apis.alfaview.com`  [HTTP 200]  185.127.28.102,185.245.100.219,195.201.44.172
- `beta-app.alfaview.com`  [HTTP 401]  185.127.28.101,185.245.100.52,195.201.44.171
- `beta-hcloud-19-beta-audio-4xstl.alfaview.com`  [HTTP 404]  195.201.44.179
- `beta-hcloud-19-beta-audio-8xnz9.alfaview.com`  [HTTP 404]  195.201.44.184
- `beta-hcloud-19-beta-engine-9m62k.alfaview.com`  [HTTP 404]  195.201.44.181
- `beta-hcloud-19-beta-engine-vlhwd.alfaview.com`  [HTTP 404]  195.201.44.178
- `beta-hcloud-19-beta-hydra-dzwx8.alfaview.com`  [HTTP 200]  195.201.44.186
- `beta-hcloud-19-beta-video-xjl6p.alfaview.com`  [HTTP 404]  195.201.44.181
- `beta-hcloud-19-beta-video-zhjvl.alfaview.com`  [HTTP 404]  195.201.44.185
- `beta-noris-33-beta-audio-ljj9x.alfaview.com`  [HTTP 404]  185.245.100.206
- `beta-noris-33-beta-audio-xr4mw.alfaview.com`  [HTTP 404]  185.245.100.34
- `beta-noris-33-beta-engine-cr5rs.alfaview.com`  [HTTP 404]  185.245.100.243
- `beta-noris-33-beta-engine-w84qw.alfaview.com`  [HTTP 404]  185.245.100.163
- `beta-noris-33-beta-hydra-2zm7t.alfaview.com`  [HTTP 200]  185.245.100.243
- `beta-noris-33-beta-video-2f9jw.alfaview.com`  [HTTP 404]  185.245.100.75
- `beta-noris-33-beta-video-vbvn5.alfaview.com`  [HTTP 404]  185.245.100.249
- `beta-ovh-29-beta-audio-j5qgh.alfaview.com`  [HTTP 404]  185.127.28.110
- `beta-ovh-29-beta-audio-vldgf.alfaview.com`  [HTTP 404]  185.127.28.122
- `beta-ovh-29-beta-engine-dxvt4.alfaview.com`  [HTTP 404]  185.127.28.120
- `beta-ovh-29-beta-engine-k7khj.alfaview.com`  [HTTP 404]  185.127.28.113
- `beta-ovh-29-beta-hydra-z4tf8.alfaview.com`  [HTTP 200]  185.127.28.116
- `beta-ovh-29-beta-video-8zvvm.alfaview.com`  [HTTP 404]  185.127.28.117
- `beta-ovh-29-beta-video-gbbw4.alfaview.com`  [HTTP 404]  185.127.28.112
- `beta-webclient.alfaview.com`  [HTTP 200]  185.127.28.100,185.245.100.98,195.201.44.170
- `bhc.alfaview.com`  [HTTP 200]  185.127.28.102,185.245.100.219,195.201.44.172
- `client-diagnostics-ingest.alfaview.com`  [HTTP 404]  116.202.6.245,5.75.215.227
- `clone.staging-wordpress.alfaview.com`  [HTTP 303]  82.165.59.152
- `demo-company.alfaview.com`  [HTTP 200]  185.127.28.102,185.245.100.219,195.201.44.172
- `design-assets.alfaview.com`  [HTTP 404]  91.98.11.18
- `design-tokens.alfaview.com`  [HTTP 404]  91.98.11.18
- `hello-world.atlas-spike.atlas.alfaview.com`  [HTTP 404]  51.75.92.42
- `insider-webclient.alfaview.com`  [HTTP 200]  185.127.28.101,185.245.100.52,195.201.44.171
- `internal.alfaview.com`  [HTTP 401]  185.127.28.100,185.245.100.98,195.201.44.170
- `kh-freiburg.alfaview.com`  [HTTP 200]  185.127.28.102,185.245.100.219,195.201.44.172

## Technology / fingerprint notes
- All apps fronted by `edge-proxy` (reverse proxy header fingerprint).
- `internal.alfaview.com` -> 401 + `WWW-Authenticate: Basic realm="restricted"` — HTTP Basic auth boundary.
- `beta-app.alfaview.com` -> 401 + `WWW-Authenticate: Basic` — HTTP Basic auth boundary.
- Global ingress pool: 185.127.28.101, 185.245.100.52, 195.201.44.171 (app/apis/assets/internal/webclient).
- Beta global pool: 185.127.28.102, 185.245.100.219, 195.201.44.172 (beta-app, bhc, demo-company, kh-freiburg).
- Beta datacenter deployment clusters (each with audio/engine/video/hydra roles):
-   - hcloud (Hetzner) 195.201.44.17x-.18x
-   - noris (Noris/DE) 185.245.100.x
-   - ovh (OVH) 185.127.28.11x-.12x
-   Only the `-hydra-*` nodes answer 200 at root; audio/engine/video nodes return 404 (service-specific, no root route).
- `clone.staging-wordpress.alfaview.com` (303) on 82.165.59.152 (Strato) — staging WordPress clone.
- `design-assets`/`design-tokens` on 91.98.11.18 — separate static host.
- `hello-world.atlas-spike.atlas.alfaview.com` on 51.75.92.42 (OVH) — spike/experiment namespace.

> Honest framing: these are surface-map observations from passive enumeration. No vulnerability is claimed. All HTTP probes were read-only GET to `/` and common static paths. Scope must be confirmed with the program before any active testing.
