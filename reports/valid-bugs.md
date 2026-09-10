# Validated findings (running count 0)

- 11 lead(s) marked VALID at 2026-09-05 16:10:05 UTC
  - **Verdict: VALID**
  - | Q4 Provable non-invasively? | PARTIAL | Needs valid companyId+roomId pair + 10+ rapid requests to test rate limiting |
  - | Q7 Reasonable triager accept? | YES | If rate limiting confirmed absent, this is a valid auth bypass; accessKey entropy matters |
  - **Verdict: HOLD** — Blocked on obtaining valid companyId+roomId pair. Once obtained, re-triage after rate-limit probe.
  - | Q4 Provable non-invasively? | NO | Needs valid client_id + registered redirect_uri to test bypass. Web bundle carries no client_id. Desktop installer is only source (S3 buckets 403). |
  - **Verdict: VALID (low severity)** — Fully provable passively, genuine misconfiguration, but low direct impact.
  - | Q4 Provable non-invasively? | PARTIAL | OIDC/JWKS metadata is public (passive proof of misconfiguration). Actual exploit needs a valid token sample to test alg confusion. |
  - **Verdict: HOLD** — Needs valid token to test. Misconfiguration is provable passively but exploit requires AUTH_HELPED step.
  - | 1 | Cross-tenant IDOR (REST UUID paths) | **VALID** | 7.5 HIGH | Ready to test with free account |
  - | 4 | FusionAuth issuer=acme.com drift | **VALID** | 5.3 MED | Low severity, fully passive |
  - | 6 | JWT alg confusion (HS256+RSA JWKS) | **HOLD** | 9.8 CRIT | Needs valid token sample |

- 2 lead(s) marked VALID at 2026-09-05 18:21:43 UTC
  - | 1 | Cross-tenant IDOR (REST UUID paths) | **VALID** |
  - | 4 | FusionAuth issuer=acme.com drift | **VALID** |

- 8 lead(s) marked VALID at 2026-09-07 14:28:07 UTC
  - | Q2 | Attacker reachable? | **YES** — requires valid company-scoped token (two free-tenant signup path mapped) |
  - | Q7 | Reasonable triager accept? | **YES** — clear misconfiguration with concrete evidence; however, exploitability requires valid token to test alg-confusion on consumer |
  - **Verdict: VALID** (misconfig finding); alg-confusion exploit remains HOLD (needs token)
  - **Verdict: VALID**
  - | Q7 | Reasonable triager accept? | **MARGINAL** — supply chain argument is valid but requires storage compromise assumption |
  - **Verdict: VALID** (weak but valid)
  - | Q2 | Attacker reachable? | **YES** — with valid token |
  - **Verdict: VALID** (informational/low) — inconsistent resolver auth is a valid finding, but no data leaked in observed responses.

- 4 lead(s) marked VALID at 2026-09-09 00:20:51 UTC
  - **Verdict: HOLD** — issuer=acme.com might be intentional for a specific integration. Need to confirm it's not a dev/test artifact. The implicit flow + HS256 in supported algs (while JWKS is RSA-only) 
  - | Q3 Impact? | **Yes** — `guestAuthenticate(userId,companyId,roomId)` and `guestJoin(userId,companyId,roomId,displayName)` mutations are processed without accessKey, while the REST guest-link flow req
  - **Verdict: VALID**
  - | 2 | GraphQL guest mutations without accessKey | **VALID** | 7.5 | **REPORT** |

- 6 lead(s) marked VALID at 2026-09-10 09:49:26 UTC
  - **Verdict: VALID**
  - **Verdict: VALID**
  - | Q4 Provable | NO — requires valid companyId+roomId seed to test rate limiting |
  - | 1 | FusionAuth issuer=acme.com drift | **VALID** | None — passive proof complete |
  - | 2 | Unauthenticated binary distribution (test.alfaview.com) | **VALID** | None — passive proof complete |
  - | 7 | JWT algorithm confusion (HS256/RSA) | **HOLD** | Needs valid token sample |
