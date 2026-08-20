# Strategic Triage — 2026-08-20 (deep recon pass)

Status: PASSIVE RECON ONLY (subfinder CT/passive sources + status-code probes). No exploitation, no fuzzing.

## Context
- Pipeline was generating hallucinated hypotheses (111 rubber-stamp VALIDs, all on 3 generic guesses).
- Root cause identified: analyst has NO real data — invents URLs from scope alone, dedup + evidence gate now kill the garbage.
- Fix path: feed REAL discovered assets into analyst context. First real inventory committed 2026-08-20 (b48d7d0/789d0f7).

## Inventory built
- 10,814 real subdomains (subfinder passive) across: manheim 7123, dealertrack 1008, autotrader 864, kbb 655, docker 400, posit.cloud 368, emsisoft 237, duocircle 85, posit.co 74.
- 1,010 high-value candidates (api/admin/dev/staging/uat/git/grafana/etc patterns), 183 live.

## Findings (probed read-only)

### 1. Manheim (Cox Auto) — dev-retool-api.awslogsvcsnp.manheim.com [MEDIUM-LOW]
- Full Retool on-prem instance, publicly reachable, version 4.0.9-f99e619 (Build 5345) disclosed in HTML.
- API auth-gated (401 on /api/*, /api/system, /api/user) — NOT directly exploitable.
- Version 4.0.9 is NOT in known-CVE ranges (host-header CVE-2025-47424 affects <3.196; SAML CVE-2025-29774/29775 needs SAML; CVE-2024-42056 needs authenticated attacker; CSRF CVE-2025-49017 needs app-editor permission).
- Verdict: low/weakened value alone. Internal tooling exposed to internet + version disclosure = possible info-level report, but email-only program bar is higher. HOLD unless login flow shows more.

### 2. Emsisoft — access.infra.emsisoft.com [MEDIUM]
- BeyondTrust Remote Support (Bomgar-style) appliance, community edition, publicly reachable, proxied to internet.
- /web/config.js discloses: local auth enabled, OTP 2FA configured, edition=community, proxyCluster=access, canJoinSessions=true, no OIDC/SAML.
- grafana-analytics.access.infra.emsisoft.com exists in CT logs — an internal infra hostname, reachable as a launch target.
- /web/launch/<host> accepts ANY hostname (200 for localhost/169.254.169.254) but serves the same static SPA — NO SSRF/proxy demonstrated (content identical for bogus host).
- CVE-2024-12356 (BeyondTrust RCE, actively exploited Dec 2024) — could NOT verify version from public surface (app.js obfuscated, /web/version is SPA).
- Verdict: appliance exposed + internal hostnames leaked via CT + community edition. Medium-value lead: check version via authenticated login screen, check launch flow for auth bypass. Worth a human look.

### 3. Docker — api.staging.offload.docker.com / api.offload.docker.com / registry-stage.hub.docker.com [LOW]
- Staging + production offload APIs, registry-stage: all 401 Bearer auth (Docker registry auth). No anonymous token flow. No exposed data.
- Verdict: LOW. Existence of staging endpoints is not a finding. HOLD.

### 4. Posit — sso.staging.posit.cloud / login.staging.posit.cloud [LOW]
- Staging SSO environment publicly reachable, redirects to staging login. Auth-gated.
- Verdict: LOW. HOLD.

### 5. Cox/Autotrader/KBB/Manheim dev/uat/int5 APIs [LOW]
- api-dev/qa/pp/pp2 autotrader, kbb qa-bycapi, manheim dev-*-api, invitation-codes-*/pricing-admin-*/question-engine-*: ALL 401/403 (auth-gated).
- Verdict: LOW. Hygiene is good; no unauth access found. HOLD.

## Strategic recommendations
1. Do NOT report any of the above as-is to email-only programs — all auth-gated, no demonstrated impact.
2. The real value now: 10,814 real hosts + 183 live high-value = feed into pipeline analyst context so it stops guessing. Pipeline hypotheses will now reference REAL endpoints.
3. Next deep-dive candidates (in priority order):
   a. Emsisoft BeyondTrust appliance — login flow, version fingerprint, launch endpoint behavior (highest value if CVE-2024-12356 applies).
   b. DuoCircle (85 subdomains, email-security vendor — likely smaller attack surface, weaker hygiene).
   c. posit.cloud app surface (368 subdomains) — SaaS with auth; look for exposed app/API endpoints in JS bundles.
   d. Docker wildcard-dns hosts beyond docker.com (docker.io/dhi.io) — check for dangling/expired.
4. Wire inventory into analyst prompt (next pipeline change).