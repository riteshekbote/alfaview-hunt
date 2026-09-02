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
