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
