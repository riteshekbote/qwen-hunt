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
## 2026-08-19 19:51:33 UTC (model qwen14b)
[NEW] https://github.com/posit (probed 200, previously not in surface inventory)
asset: https://github.com/posit  
class: MISCONFIG  
asset: https://github.com/posit  
confidence: 50  
reasoning: The GitHub repo for Posit was probed and returned a 200, indicating public access. A public repo may expose code, secrets, or configuration files if not properly protected.  
evidence_needed: Verification of exposed files (e.g., `.git/config`, secrets, or sensitive code) in the repo.  
verify_steps: PROBE GET https://github.com/posit/.git/config  
impact: Exposed code or secrets (MEDIUM)  
testability: PASSIVE
[FINAL]  
class: MISCONFIG  
asset: https://github.com/posit  
confidence: 50  
reasoning: The GitHub repo for Posit was probed and returned a 200, indicating public access. A public repo may expose code, secrets, or configuration files if not properly protected.  
evidence_needed: Verification of exposed files (e.g., `.git/config`, secrets, or sensitive code) in the repo.  
verify_steps: PROBE GET https://github.com/posit/.git/config  
impact: Exposed code or secrets (MEDIUM)  
testability: PASSIVE
[NEXT] PROBE: GET https://github.com/posit/.git/config
[LEARN] ACCEPTED MISCONFIG @ https://github.com/posit: Public GitHub repo may expose code or secrets if not protected.
[RISK] 65  
