# Deep Recon Consolidated Report — 2026-08-20/21

Status: PASSIVE RECON COMPLETE. All techniques non-destructive, read-only.

## Recon Pipeline
1. subfinder passive CT → 10,814 subdomains
2. dnsx A/CNAME → 7,846 resolved hosts
3. httpx live probe → 183 high-value live hosts
4. nuclei tech/misconfig scan → Apache WAF, reCAPTCHA on DuoCircle
5. katana crawl → DuoCircle WHMCS endpoints, Docker Auth0 flows
6. jsluice JS analysis → WHMCS clientarea/serverstatus/submitticket/cart endpoints
7. gau/waybackurls → rate-limited, no results
8. dnstake/subzy takeover check → no dangling CNAMEs found

## Inventory
- `inventory/real-subdomains.txt` — 10,814 subdomains across all 5 targets
- `inventory/live-highvalue.txt` — 183 live high-value hosts with status codes
- `inventory/highvalue-candidates.txt` — 1,010 candidates (api/admin/dev/staging patterns)
- `dnsx-all.txt` — 7,846 DNS-resolved hosts (A + CNAME records)

## Target-by-Target Findings

### DuoCircle (email security vendor) — HIGHEST PRIORITY
- **WHMCS billing portal** at `portal.duocircle.com`
  - PHP stack, Apache WAF (`apachegeneric`), reCAPTCHA on clientarea
  - Admin panel `/admin` returns 403 (IP-restricted — good hygiene)
  - Payment gateway callbacks live: PayPal callback returns 406 (Not Acceptable), others 404
  - Password reset flow: `/pwreset.php` present with token-based reset
  - Login: `/dologin.php` returns 302 redirect (standard WHMCS behavior)
  - `configuration.php` returns 200 but empty (PHP executed, no disclosure)
  - WHMCS version NOT disclosed (no meta generator, no footer version)
  - **No nuclei CVE hits** — instance likely patched or templates not matching
  - `sentry.ops.duocircle.com` — Sentry error tracking, auth-gated (401)
  - `halonapi.us.duocircle.com` — Halon mail gateway API, root 200 (empty)
  - `release.duocircle.com` — Quarantined Message Release (customer-facing)
  - Email posture: SPF `-all`, DMARC `p=reject` — solid
- **Human investigation needed**: WHMCS auth bypass attempts, password reset user enumeration, payment callback validation bypass

### Emsisoft (antivirus vendor) — MEDIUM PRIORITY
- **BeyondTrust Remote Support appliance** at `access.infra.emsisoft.com`
  - Community edition, local auth + OTP 2FA
  - `/web/config.js` leaks: `edition=community`, `canJoinSessions=true`, `proxyCluster=access`
  - `grafana-analytics.access.infra.emsisoft.com` — internal hostname in CT logs
  - `/web/launch/<host>` accepts arbitrary hosts but returns identical SPA (no SSRF)
  - Version fingerprint blocked: app.js (3.4MB) obfuscated, no version strings
  - CVE-2024-12356 (BeyondTrust RCE) applicability UNVERIFIED (needs version)
  - Email posture: SPF `-all`, DMARC `p=quarantine` with **`sp=none`** — subdomains NOT DMARC-enforced (spoofable)
- **Human investigation needed**: version fingerprint via authenticated login, CVE-2024-12356 check, subdomain email spoofing demonstration

### Manheim/Cox Automotive — LOW-MEDIUM PRIORITY
- **Retool on-prem** at `dev-retool-api.awslogsvcsnp.manheim.com`
  - Version 4.0.9-f99e619 (Build 5345) disclosed in HTML
  - API auth-gated (401 on /api/*, /api/system, /api/user)
  - `DISABLE_USER_PASS_LOGIN=true`, `SAML_ENABLED=false`
  - Version 4.0.9 NOT in known CVE ranges (host-header CVE-2025-47424 affects <3.196)
  - Dev/uat/int5 environments for pricing-admin, invitation-codes, question-engine — all 403
  - `account.manheim.com` → elasticbeanstalk (live, SSO-authed)
- **Verdict**: LOW — current version, auth-gated, no demonstrated impact

### Docker — LOW PRIORITY
- `api.staging.offload.docker.com` — 401 Bearer auth (Docker registry auth)
- `api.offload.docker.com` — 401 Bearer auth
- `registry-stage.hub.docker.com` — 401 (registry auth)
- `api.build-cloud-stage.docker.com` — 404
- Auth0 OIDC flow with PKCE for hub.docker.com and app.docker.com
- `app.docker.com/admin` — admin panel, requires auth
- **Verdict**: LOW — all auth-gated, staging endpoints not accessible

### Posit — LOW PRIORITY
- `sso.staging.posit.cloud` → `login.staging.posit.cloud` (SSO redirect)
- `*.share.connect.posit.cloud` — thousands of UUID-based user content apps (Quarto/Shiny docs)
  - All public (by-design share links)
  - Amazon S3 + Varnish CDN backend
  - Examples: "City of Arapahoe Zoning Regulations", "Data Donations Tutorial"
- **Verdict**: LOW — by-design public shares, no cross-tenant data exposure evident

## Key Metrics
- Total subdomains: 10,814
- DNS-resolved: 7,846
- Live high-value: 183
- Auth-gated (401/403): all discovered high-value hosts
- File leaks (.env/.git): 0 (all false positives — SPA fallbacks)
- Subdomain takeovers: 0
- Nuclei CVE hits: 0
- Unauthenticated data access: 0

## Strategic Assessment
All five targets have **decent perimeter hygiene**. No unauthenticated data access, no file leaks, no takeovers found. The strongest leads for human investigation:

1. **DuoCircle WHMCS** — auth bypass attempts, password reset user enumeration, payment callback validation
2. **Emsisoft BeyondTrust** — version fingerprint (CVE-2024-12356), community edition exposure, subdomain email spoofing
3. **Posit share apps** — cross-tenant data exposure (need account to test)

None of the above clear the bar for an email-only program submission without active exploitation or demonstrated impact. The pipeline should now focus on:
- Feeding real inventory into analyst context (DONE)
- Monitoring for new subdomains (reposcan handles this)
- Human investigation of the WHMCS and BeyondTrust surfaces
