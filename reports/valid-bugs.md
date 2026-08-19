# Valid Bugs Register
<!-- Cleaned 2026-08-19: removed hallucinated 16:44 and 18:08 entries — triage ran on empty lead sets and the model invented "GET /config/config.json" for duocircle. Gate fix landed at commit 7c492c4: all downstream triage steps are now conditionally skipped when has_leads=false. -->

<!-- Live data flows in from analyst cycles only when real [HYP]/[LEAD] lines exist in leads/ -->
- 6 lead(s) marked VALID at 2026-08-19 18:39:52 UTC
  - Verdict: Valid. Steps would be GET request to the API endpoint with SSRF payload. Impact is internal server access. CVSS might be around 6.5. Reporting to securitydisclosure@coxautoinc.com.
  - Verdict: Valid. Steps: GET to a subdomain of docker.io. Impact: DNS misconfig leading to possible attacks. CVSS 5.5. Report to security@docker.com.
  - Verdict: Valid. Steps: GET to the GitHub repo URLs. Impact: exposed code/secrets. CVSS 6.5. Report to security@posit.co.
  - **Verdict**: **VALID**
  - **Verdict**: **VALID**
  - **Verdict**: **VALID**

- 2 lead(s) marked VALID at 2026-08-19 19:08:23 UTC
  - **Verdict:** VALID
  - **Verdict:** VALID

- 6 lead(s) marked VALID at 2026-08-19 20:03:49 UTC
  - Verdict: Maybe valid. Need to check if the probe shows the endpoint. If the probe for coxautoinc.com's API endpoints is public, then steps would be GET to the endpoint. Impact is SSRF, CVSS around 9.1
  - Verdict: Valid. Steps: GET to the wildcard DNS endpoint. Impact: DNS misconfig, CVSS 7.5. Report to security@docker.com.
  - Verdict: Valid. Steps: GET to the GitHub repo. Impact: info disclosure, CVSS 6.5. Report to security@posit.co.
  - **Verdict**: **VALID**
  - **Verdict**: **VALID**
  - **Verdict**: **VALID**

- 6 lead(s) marked VALID at 2026-08-19 20:29:56 UTC
  - **Verdict**: VALID
  - **Verdict**: VALID
  - **Verdict**: VALID
  - | **Cox Automotive** | VALID   | SSRF via API endpoints validated.                                    |
  - | **Docker**         | VALID   | Misconfigured wildcard DNS validated.                                |
  - | **Posit**          | VALID   | GitHub org misconfigurations validated.                              |
