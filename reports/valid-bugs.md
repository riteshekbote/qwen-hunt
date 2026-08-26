# Valid Bugs Register
<!-- Register reset 2026-08-20. All prior entries (111 rubber-stamped VALIDs on 3 generic hypotheses) were hallucinated by the 14B triager — the same Cox SSRF / Docker wildcard DNS / Posit CI-CD guesses were re-validated every cycle with zero probe evidence. Fixes shipped: (1) aggregate dedups hypotheses by hash register, (2) triage evidence gate — VALID requires the lead URL to have a live probe response AND the verdict to cite that URL, (3) unproven generic hypotheses default to HOLD. Only evidence-backed findings land here now. -->
<!-- Cleaned 2026-08-20 17:10 UTC: removed 6 hallucinated VALIDs from pre-gate triage run 2b06d8b6 (14:22). Post-gate runs (15:03, 15:53, 16:56) added 0 entries. -->
<!-- Corrected 2026-08-26 21:30 UTC: Retracted DuoCircle client_id (2x2 confounded, Informative per triager), corrected Emsisoft BeyondTrust→Teleport 17.5.2 HOLD (banner without CVE), added manual F1/F7 with evidence gate PASS. -->

## Cox Automotive — WP REST Employee PII + 741 Campaign Records — MEDIUM — VALID
- **Date:** 2026-08-22 (re-checked 2026-08-26: still 200)
- **Target:** coxautomotive
- **URL:** `https://cs-camp-stage.kbb.com/wp-json/wp/v2/users` (also `test.autotrader.com/collections/wp/cs-camp/`, origin `cs-camp.go-vip.net`)
- **Severity:** MEDIUM `AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:N/A:N` 7.5 → cap Medium (employee + partner-confidential, no customer PII)
- **Status:** REPORTED 2026-08-22 to securitydisclosure@coxautoinc.com (PGP 5D21EF090407CEAD) — awaiting ack, still unpatched 2026-08-26 21:10 UTC
- **Evidence:** `GET /wp-json/wp/v2/users → 200 8208B application/json` (not 401/403), 10 users `mbala@kbb.com`; `x-wp-total: 741` across 7 types; `deploy/v1/health → 200 {"status":"ok","deployed_at":"2026-08-24T03:31:16Z"}`; `likely_environment null`
- **Report:** `/tmp/opencode/deepx2/cox-f1-detailed-report.md` + `BB/2/cox-f1-email.txt.asc` (7.0K)
- **Probe:** `curl -s https://cs-camp-stage.kbb.com/wp-json/wp/v2/users | python3 -m json.tool`

## Docker — Staging Hub Pre-release DHI Catalog — LOW — VALID
- **Date:** 2026-08-22 (re-checked 2026-08-26: still 162)
- **Target:** docker
- **URL:** `https://hub-stage.docker.com/v2/repositories/dhi/?page_size=100` (also `/hardened-images/catalog` 49 products)
- **Severity:** LOW `AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N` 5.3 — business roadmap, staging, no customer data
- **Status:** REPORTED 2026-08-22 to security@docker.com (+ addendum) — still 162 vs prod 3
- **Evidence:** `GET /v2/repositories/dhi → 200 count:162` vs prod `hub.docker.com → 3` (`policies/sandbox-template`); `dhi/crossplane/tags → 80` digests `sha256:27901c8f...` `creator 3623022`; `dhi/bash → 404` (planned lineup leak)
- **Report:** `/tmp/opencode/deepx2/docker-f7-detailed-report.md` + `BB/2/docker-f7-email.txt`

## Emsisoft — Teleport CE 17.5.2 config.js — HOLD (was BeyondTrust)
- **Date:** 2026-08-20
- **Target:** emsisoft
- **URL:** `https://access.infra.emsisoft.com/web/config.js` (`GRV_CONFIG`, `proxyVersion 17.5.2`)
- **Severity:** Info — banner/version disclosure without working CVE (GHSA-8cqv-pj7f-pwpc patched in 17.5.2) — per never-submit, HOLD. Not sent.

## DuoCircle — Auth Portal Client ID Enumeration — RETRACTED
- **Date:** 2026-08-20 → RETRACTED 2026-08-23
- **Reason:** Confounded variable — `redirect_uri` (`https://account.duocircle.com/callback` vs `https://test.com`) caused 21KB delta, not `client_id`. Triager 2x2: any `client_id` + registered redirect → identical 67KB with `DuoCircle` branding. `client_id` public per RFC6749 §2.2, WorkOS hosted. WorkOS security@workos.com is correct venue if genuine platform defect. Valid-bugs gate `has_evidence` strict URL check fails — no report.
- **Lesson:** Enforce single-variable isolation; verify parameter is not public-by-design.
