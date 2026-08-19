## 2026-08-19 19:06:10 UTC (model qwen14b)
asset: *.docker.com
class: MISCONFIG
asset: *.docker.com
confidence: 75
reasoning: Wildcard DNS records for *.docker.com may be misconfigured, allowing arbitrary subdomains to resolve. This could expose internal services or APIs if not properly restricted.
evidence_needed: DNS record verification for a non-canonical subdomain (e.g., test.docker.com).
verify_steps: PROBE DNS A record for test.docker.com.
impact: Exposed internal services/APIs (MEDIUM).
testability: PASSIVE
[FINAL]
[NEXT] PROBE: PROBE DNS A record for test.docker.com
[LEARN] REJECTED SSO-domain-discovery oracle @ docker: confirmed by Spare Labs triage 2026-08-19
[RISK] 60
