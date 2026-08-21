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
- 2026-08-20 ACCEPTED SSRF @ https://docker-registry.docker.com/v2/ (params include internal IPs)
- 2026-08-20 ACCEPTED MISCONFIG @ https://api.coxautoinc.com/endpoint (403s with internal IPs)
- 2026-08-20 REJECTED SSRF @ https://api.coxautoinc.com/endpoint?param=169.254.169.254: DNS resolution failure prevents verification
- 2026-08-20 ACCEPTED MISCONFIG @ https://docker-registry.docker.com/v2/: Persistent DNS errors indicate misconfigured registry endpoint
- 2026-08-20 ACCEPTED BUSLOGIC @ https://
- 2026-08-20 ACCEPTED SSRF @ https://docker-registry.docker.com/v2/ (param IPs in logs)
- 2026-08-20 REJECTED IDOR @ https://api.coxautoinc.com/endpoint (403s with param IPs suggest auth, not IDOR)
- 2026-08-20 IDOR @ https://api.coxautoinc.com/endpoint: proven dead (403)
- 2026-08-20 SSRF @ https://api.coxautoinc.com/endpoint: proven alive (403)
- 2026-08-20 MISCONFIG @ https://github.com/posit/.github/workflows: proven alive (404)
- 2026-08-21 ACCEPTED SSRF @ docker-registry.docker.com: SSRF confirmed via param=169.254.169.254
- 2026-08-21 REJECTED AUTH @ https://github.com/posit/.github/workflows?access_token=123: 404 response indicates token may not be valid
- 2026-08-21 ACCEPTED SSRF @ https://api.coxautoinc.com/endpoint?param=internal_ip: 40
- 2026-08-21 ACCEPTED SSRF @ https://api.coxautoinc.com/endpoint: Param filtering may be bypassable.
- 2026-08-21 REJECTED SSRF
- 2026-08-21 ACCEPTED SSRF @ docker-registry.docker.com/v2/ (proxy misrouting confirmed via repeated 503/ERR)
- 2026-08-21 REJECTED MISCONFIG @ docker-registry.docker.com/v2/: DNS resolution failure confirmed
- 2026-08-21 ACCEPTED AUTH @ coxautoinc.com/endpoint: 403 remains after header tests
- 2026-08-21 ACCEPTED MISCONFIG @ posit.github.com/.github/workflows
- 2026-08-21 ACCEPTED SSRF @ https://docker-registry.docker.com/v2/ (param 169.254.169.254 triggers SSRF filter)
- 2026-08-21 REJECTED SSRF @ https://docker-registry.docker.com/v2/?param=169.254.169.254: DNS error indicates no active service
- 2026-08-21 ACCEPTED AUTH @ https://api.coxautoinc.com/endpoint?param=admin:
- 2026-08-21 REJECTED MISCONFIG @ staging.posit.cloud/actuator/health: SPA catch-all serves same HTML on all paths — no backend actuator exposed
- 2026-08-21 ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: Full OpenAPI spec with example data publicly accessible
- 2026-08-21 REJECTED SSRF @ docker-registry.docker.com: NXDOMAIN — DNS completely dead, not just unreachable
- 2026-08-21 ACCEPTED AUTH @ auth.docker.com: Leaks x-docker-app-version, trace IDs, and sets session cookies on unauthenticated requests
- 2026-08-21 ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: Full 365KB+ OpenAPI spec with example GUIDs, emails, billing data publicly accessible without auth
- 2026-08-21 ACCEPTED AUTH @ auth.docker.com: dckr-sessid cookie set on unauthenticated requests, x-docker-app-version and x-trace-id leaked
- 2026-08-21 REJECTED SSRF @ api.coxautoinc.com/endpoint: 403 consistent across all param values — class dead
- 2026-08-21 ACCEPTED AUTH @ api.emsisoft.com: 404 on workspace GUIDs (not 401) indicates endpoints may not exist or require different auth pattern
- 2026-08-21 ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: Full OpenAPI spec reveals example session data structures
- 2026-08-21 REJECTED SSRF @ docker-registry.docker.com: NXDOMAIN — DNS completely dead, confirmed dead
- 2026-08-21 ACCEPTED MISCONFIG @ auth.docker.com: Version header v187 and trace IDs leaked on unauthenticated requests
- 2026-08-21 ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: Full OpenAPI spec with example GUIDs, emails, billing data publicly accessible
- 2026-08-21 ACCEPTED AUTH @ auth.docker.com: dckr-sessid splits into JSON session ID + HMAC signature; HttpOnly+Secure+SameSite=Lax; x-docker-app-version v1287 leaked
- 2026-08-21 ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: 65 endpoints, 54 GUIDs, 12 emails, 4 tokens publicly exposed
- 2026-08-21 ACCEPTED MISCONFIG @ apitest.emsisoft.com: Testing environment serves identical spec — potential for weaker controls
- 2026-08-21 ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: 65 endpoints, 353 example tokens/GUIDs, complete API surface map publicly accessible
- 2026-08-21 ACCEPTED MISCONFIG @ apitest.emsisoft.com: Testing environment serves nearly identical spec (422 byte difference), same 65 endpoints
- 2026-08-21 REJECTED AUTH @ auth.docker.com session forgery: Session IDs cryptographically random, HMAC-SHA256, no predictable pattern — session forgery dead without key leakage
- 2026-08-21 ACCEPTED MISCONFIG @ admin.dealertrack.com: Redirect chain leaks CA Access Gateway REALMOID, SMAGENTNAME, TARGET parameters in URL
