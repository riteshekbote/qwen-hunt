## 2026-08-21 19:32:06 UTC (model mimo)
[CHANGED] docker-registry.docker.com — NXDOMAIN confirmed (DNS彻底死亡, not just unreachable)  
[CHANGED] auth.docker.com — reveals x-docker-app-version: v1287, x-trace-id, rate-limit headers, session cookies on every request  
[NEW] api.hub.docker.com — empty response body (no error, no content — potential ghost endpoint)  
[CHANGED] api.offload.docker.com — Cloudflare-fronted, empty response (no longer returning content)
class: IDOR  
asset: api.emsisoft.com/swagger/v1.0/swagger.json  
confidence: 55  
reasoning: The public swagger.json exposes example response schemas with workspace GUIDs, user emails, and permission arrays. If the API accepts GUIDs in path parameters without verifying caller ownership, an attacker could enumerate workspaces via /v1/workspaces/{guid}. The example shows "permissions": ["owner","admin"] — if this is reflected in real responses, privilege escalation paths may exist.  
evidence_needed: Confirm whether /v1/workspaces/{any-guid} returns data without a valid API key, or whether 401 is returned consistently.  
verify_steps:  
impact: If unauthenticated access succeeds → read any customer's workspace data (devices, alerts, seat counts, emails). Severity: High. If blocked → no issue.  
testability: PASSIVE  
class: AUTH  
asset: auth.docker.com  
confidence: 45  
reasoning: The auth.docker.com endpoint sets a dckr-sessid cookie on every unauthenticated request. The cookie value is base64-encoded JSON containing what appears to be session state. If the cookie is not server-validated (e.g., client-side session), an attacker could forge session tokens. The x-docker-app-version header (v187) could reveal version-specific behavior.  
evidence_needed: Decode the dckr-sessid cookie payload, test if modifying the cookie grants access to authenticated endpoints.  
verify_steps:  
impact: If cookie is client-validated → session hijack. If server-validated → no issue. Severity: Medium-High if exploitable.  
testability: AUTH_HELPED  
class: AUTH  
asset: api.coxautoinc.com/endpoint  
confidence: 40  
reasoning: All probes return 403 consistently. However, the knowledge base has ACCEPTED entries for AUTH @ coxautoinc.com/endpoint with 403 remaining after header tests. This suggests the auth gate is solid but the class is not fully proven dead. Common bypasses include X-Forwarded-For, X-Real-IP, Authorization: Bearer null, or custom headers.  
evidence_needed: Test with common auth-bypass headers (X-Forwarded-For: 127.0.0.1, Authorization: Bearer null, X-Original-URL: /admin).  
verify_steps:  
impact: If any header bypass works → access internal API endpoints. Severity: High. If all return 403 → class confirmed dead.  
testability: PASSIVE  
[FINAL]  
[LEARN] REJECTED MISCONFIG @ staging.posit.cloud/actuator/health: SPA catch-all serves same HTML on all paths — no backend actuator exposed  
[LEARN] ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: Full OpenAPI spec with example data publicly accessible  
[LEARN] REJECTED SSRF @ docker-registry.docker.com: NXDOMAIN — DNS completely dead, not just unreachable  
[LEARN] ACCEPTED AUTH @ auth.docker.com: Leaks x-docker-app-version, trace IDs, and sets session cookies on unauthenticated requests  
[RISK] 55  
