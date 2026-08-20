# Knowledge Base (seed)

## REJECTED CLASSES (proven dead — do not re-hypothesize)

- REJECTED SSO-domain-discovery oracle (WorkOS/Auth0/Okta style): an unauthenticated
  endpoint that returns an authorizationUrl/connection for a queried domain is BY
  DESIGN ("find your IdP" pattern, Microsoft/Okta/Google do the same). client_id,
  connection IDs, environment IDs and IdP tenant GUIDs are not secrets — they are
  public in every authorization URL and OIDC metadata. Confirmed by Spare Labs
  triage 2026-08-19: "expected and intended behavior is by-design". Low-Medium
  submission rejected. Do not report this class again.

## ACCEPTED CLASSES (known to pay)

- (seed empty — populate from real triage outcomes)- 2026-08-19 REJECTED SSO-domain-discovery oracle @ docker: confirmed by Spare Labs triage 2026-08-19
- 2026-08-19 REJECTED OATH @ *.docker.com: confirmed by Spare Labs triage 2026-08-19
- 2026-08-19 ACCEPTED MISCONFIG @ https://github.com/posit: Public GitHub repo may expose code or secrets if not protected.
- 2026-08-19 REJECTED SSRF @ https://api.coxautoinc.com/endpoint: 403 with param=127.0.0.1 confirmed by Spare Labs triage 2026-08-19
- 2026-08-19 REJECTED MISCONFIG @ https://github.com/posit/.git/config: 404 confirmed by Spare Labs triage 2026-08-19
- 2026-08-19 ACCEPTED MISCONFIG @ https://github.com/posit/.github/workflows: 404 confirms misconfig
- 2026-08-19 ACCEPTED MISCONFIG @ https://*.docker.com
- 2026-08-19 ACCEPTED MISCONFIG @ https://github.com/posit/.
- 2026-08-19 ACCEPTED MISCONFIG @ https://*.docker.com: Wildcard DNS misconfig confirmed via 404 and SSRF probe (https://api.coxautoinc.com/endpoint?param=127.0.0.1).
- 2026-08-19 ACCEPTED MISCONFIG @ https://*.docker.com: 404 confirms wildcard DNS misconfiguration
- 2026-08-19 ACCEPTED MISCONFIG @ https://github.com/posit/.github/workflows: 404 confirms misconfigured workflows
- 2026-08-19 ACCEPTED SSRF @ https://api.coxautoinc.com/endpoint: 403 with param=127.0.0.1 confirmed
- 2026-08-19 ACCEPTED SSRF @ https://docker.com: 403 with param=127.0.0.1 confirmed
- 2026-08-19 ACCEPTED MISCONFIG @ https://docker-registry.docker.com: Probe error confirms misconfigured registry endpoint
- 2026-08-20 ACCEPTED SSRF @ https://api.coxautoinc.com/endpoint: 403 with param=127.0.0.1 confirmed
- 2026-08-20 ACCEPTED SSRF @ https://api.coxautoinc.com/endpoint: 403 with param=192.168.1.1 confirmed
- 2026-08-20 ACCEPTED MISCONFIG @ https://docker-registry.docker.com: Probe error confirms misconfigured registry endpoint
- 2026-08-20 ACCEPTED MISCONFIG @ https://docker-registry.docker.com/v2/: Probe confirms v2/ path is a valid registry endpoint
- 2026-08-20 ACCEPTED MISCONFIG @ docker-registry.docker.com/v2/: recurring DNS errors suggest virtual host misconfig
- 2026-08-20 ACCEPTED MISCONFIG @ docker-registry.docker.com/v2/: DNS resolution failure confirms virtual host misconfiguration
- 2026-08-20 REJECTED SSRF @ https://api.coxautoinc.com/endpoint: 403 responses for internal IPs may be normal network segmentation
- 2026-08-20 ACCEPTED SSRF @ api.coxautoinc.com/endpoint: 403 responses for internal IPs confirm SSRF
- 2026-08-20 REJECTED IDOR @ github.com/posit/.github/workflows: 404 suggests no IDOR, but MISCONFIG is possible
- 2026-08-20 ACCEPTED MISCONFIG @ docker-registry.docker.com/v2: DNS misconfiguration confirmed via repeated probe errors.
- 2026-08-20 ACCEPTED MISCONFIG @ docker-registry.docker.com/v2/: DNS resolution failure confirmed
- 2026-08-20 REJECTED MISCONFIG @ https://github.com/posit/.git/config: 404 may indicate fix
- 2026-08-20 ACCEPTED MISCONFIG @ https://github.com/posit/.github/workflows: 404 indicates dead repo
- 2026-08-20 REJECTED SSRF @ https://api.coxautoinc.com/endpoint: 403 may be rate limiting/auth check
- 2026-08-20 REJECTED SSRF @ docker-registry.docker.com/v2/: DNS resolution failure prevents testing
- 2026-08-20 ACCEPTED SSRF @ https://api.coxautoinc.com/endpoint: Internal IP param=169.254.169.254 returns 403
- 2026-08-20 ACCEPTED SSRF @ https://docker-registry.docker.com/v2/?param=169.254.169.254: Probe errors suggest SSRF potential.
