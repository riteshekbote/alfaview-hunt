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
