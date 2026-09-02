# Maicher Bug Bounty — Scope-Confirmation Request Drafts

_Generated 2026-09-03 | pre-submission scope clarifications_
_Gate: confirm asset ownership/in-scope BEFORE active testing. These are drafts to paste into the hub Report form (or email Oliver) to get written authorization for the specific hosts discovered during passive recon. No active probing performed on any of these until confirmed._

---

## Why these requests exist

My passive recon on the Maicher hub identified genuine dedicated hosts for several programs. Most programs list **descriptive** (not enumerated) scope, e.g. "all company-owned infrastructure..." — so these hosts are *probably* in scope, but scope ownership should be confirmed in writing before any active testing, because the targets include large enterprises (Daimler Truck, BASF) and privacy-sensitive services (banking/finance is separate). The two below are the highest-value, wildcard-clean surfaces.

Key principle: **enumeration ≠ authorization.** Getting a written confirmation protects the researcher and keeps testing lawful.

---

## Draft 1 — Daimler Truck Holding AG (program: `daimler-truck`)

**Program name:** daimler-truck

**Subject:** Scope clarification request — `*.api.daimlertruck.com` developer/API surface

**Details:**

Hello Oliver,

During authorized passive reconnaissance on the **Daimler Truck Holding AG** program (scope: "All infrastructure, digital services, brands and subsidiaries operated by Daimler Truck Holding AG"), I identified a genuine, wildcard-clean set of API/developer hosts under `daimlertruck.com`. I only performed read-only DNS + HTTP(S)-GET fingerpronting (no active testing, no fuzzing, no auth attempts). Before I invest further effort, I'd like written confirmation that the following hosts are in scope and authorized for security testing:

**API gateways (Azure API Management):**
- `as.api.daimlertruck.com` (40.81.218.168)
- `eu.api.daimlertruck.com` (20.76.15.251)
- `dev.na.api.daimlertruck.com` (20.80.145.22)

**Authorization services:**
- `authz.as.api.daimlertruck.com` / `authz.eu.api.daimlertruck.com` / `authz.na.api.daimlertruck.com`
- plus tst/dev tier variants (`authz.tst.as/eu/na.api`)

**Developer portals (Next.js, login-gated):**
- `developer.as/eu/na.api.daimlertruck.com` (+ `developer.dev.na`, `developer.tst.eu/na`)
- Candidate hosts seen in passive DNS but not yet confirmed: `docs.api.daimlertruck.com`, `fria.api.daimlertruck.com`, `management.*.api.daimlertruck.com`, `beta.dev.na.api.daimlertruck.com`

**Questions:**
1. Are these hosts owned/operated by Daimler Truck and in scope?
2. Are the `tst`/`dev`-tier and `authz` services explicitly in scope, or should I limit activity to prod API gateways only?
3. Is testing against the Azure APIM gateways (which require valid subscription keys) acceptable, given unauthenticated access is largely blocked?
4. Any restrictions on the developer portals (they require an account/login)?

I will not test anything until I have your written confirmation. Happy to share the full host list privately.

**Contact:** [paste your email / Telegram handle here]

---

## Draft 2 — alfaview gmbh (program: `alfaview`)

**Program name:** alfaview

**Subject:** Scope clarification request — alfaview.com subdomain/API surface

**Details:**

Hello Oliver,

During authorized passive reconnaissance on the **alfaview gmbh** program (scope: "all company-operated subdomains" of alfaview.com), I identified genuine dedicated hosts. The program scope already states alfaview.com **and company-operated subdomains** are included — I want to confirm the following specific hosts/namespaces are in scope before any active testing:

**Global app/API (edge-proxy fronted):**
- `app.alfaview.com`, `apis.alfaview.com`, `assets.alfaview.com`, `webclient.alfaview.com`, `insider-webclient.alfaview.com`
- `internal.alfaview.com` (HTTP Basic auth, `WWW-Authenticate: Basic realm="restricted"`)
- `beta-app.alfaview.com` (HTTP Basic auth), `beta-apis.alfaview.com`, `beta-webclient.alfaview.com`
- `bhc.alfaview.com`, `demo-company.alfaview.com`, `kh-freiburg.alfaview.com`

**Beta datacenter deployment namespaces (Hetzner/Noris/OVH):**
- `beta-hcloud-19-beta-{audio,engine,video,hydra}-*.alfaview.com`
- `beta-noris-33-beta-{...}-*.alfaview.com`
- `beta-ovh-29-beta-{...}-*.alfaview.com`
- `hello-world.atlas-spike.atlas.alfaview.com` (spike/experiment)

**Staging / misc:**
- `clone.staging-wordpress.alfaview.com` (303)
- `client-diagnostics-ingest.alfaview.com`
- `design-assets.alfaview.com` / `design-tokens.alfaview.com`

**Questions:**
1. Do the beta/atlas/experiment and staging namespaces fall under "company-operated subdomains", or are they explicitly out of scope / sensitive?
2. Is testing the HTTP-Basic-auth-gated hosts (`internal`, `beta-app`) acceptable, or are those restricted surfaces?
3. Any prohibitions on testing the beta media/audio/video/engine nodes (live media infrastructure)?

Alfaview's published scope is very relevant (real-time/video infrastructure, customer meetings explicitly out of scope) — I will honor the "no conference interception / no live-session impact" rule. Awaiting written confirmation before any active testing.

**Contact:** [paste your email / Telegram handle here]

---

## Notes on submission method
- The hub's **Report** button opens the Bitrix24 submission form (program name, vulnerability details, PoC, contact, payment). For scope questions, put the program name, choose "scope confirmation / pre-submission inquiry" in details, and include your contact.
- These are *inquiry* drafts, not bug reports — the hub focuses on impactful bugs, so flag clearly that this is a pre-testing scope-clearing message, not a finding.
- Do **not** include findings or sensitive host data beyond what's needed; share the fuller host list privately/encrypted if requested.

---
_Honest framing: no vulnerability claimed; these are surface observations. Written scope confirmation is required before active testing on any of these live production assets._
