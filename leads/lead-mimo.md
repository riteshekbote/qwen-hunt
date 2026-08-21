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
## 2026-08-21 19:53:32 UTC (model mimo)
[PRIO] auth.docker.com, 7.20, attack:7, business:8, tech:7, gate:6, cloud:7, fresh:8
[PRIO] api.emsisoft.com, 6.55, attack:6, business:6, tech:8, gate:7, cloud:5, fresh:8
[PRIO] api.coxautoinc.com, 6.40, attack:7, business:7, tech:6, gate:4, cloud:6, fresh:8
[HYP] Public OpenAPI spec leaks example workspace GUIDs, user emails, and billing metadata
class: MISCONFIG
asset: api.emsisoft.com/swagger/v1.0/swagger.json
confidence: 72
reasoning: The swagger.json endpoint returns the full 365KB+ OpenAPI spec without authentication. It contains example response schemas with workspace GUIDs (ddaaa9b5-9985-4028-8ff7-f12255a168e9), user emails (example@emsisoft.com), permission arrays (owner, admin), billing seat counts (25 total, 19 active), install tokens, and partner IDs. While example data may be fabricated, the spec itself reveals the complete API surface including /v1/workspaces/{wsGuid}/logs, /v1/workspaces/{wsGuid}/users, and /v1/workspaces/{wsGuid}/reports — all of which return 401 without auth. If any example GUID matches a real workspace, the API structure enables targeted IDOR attempts once authenticated.
evidence_needed: Confirm whether the example GUIDs are real by attempting unauthenticated access to /v1/workspaces/{example-guid}. If 401 is returned consistently, the issue is limited to information disclosure of the API surface.
verify_steps: 1. GET https://api.emsisoft.com/v1/workspaces/ddaaa9b5-9985-4028-8ff7-f12255a168e9 (expect 401). 2. GET https://api.emsisoft.com/v1/workspaces/00000000-0000-0000-0000-000000000000 (test if zeroed GUID returns different error). 3. Diff 401 response body for both to detect information leakage.
impact: Severity MEDIUM. Public API surface disclosure reveals all endpoint paths, parameter schemas, and example data structures. Enables attacker to enumerate workspaces once they obtain any valid API key. The example billing data (seat counts, license types) could assist social engineering.
testability: PASSIVE
[HYP] Session cookie set without Secure flag on unauthenticated requests
class: AUTH
asset: auth.docker.com
confidence: 62
reasoning: auth.docker.com sets a dckr-sessid cookie on every unauthenticated request. The cookie value is base64-encoded JSON. The endpoint also leaks x-docker-app-version (v1287/v187), x-trace-id, and rate-limit headers on every request. If the cookie lacks the Secure flag, it could be intercepted over HTTP. The version header reveals exact build information.
evidence_needed: Decode the dckr-sessid cookie payload to determine if it contains session state, HMAC signature, or is purely client-side. Check Set-Cookie flags (Secure, HttpOnly, SameSite).
verify_steps: 1. curl -v https://auth.docker.com/ 2>&1 | grep -i 'set-cookie\|x-docker-app-version\|x-trace-id'. 2. Base64-decode the dckr-sessid value. 3. Modify one character in the decoded payload, re-encode, and send back as cookie to test if server validates.
impact: Severity MEDIUM-HIGH if cookie is forgeable (session hijack). Severity LOW if server-validated with HMAC. The version leak is information disclosure only.
testability: AUTH_HELPED
[HYP] Auth bypass via HTTP headers on protected endpoints
class: AUTH
asset: api.coxautoinc.com/endpoint
confidence: 42
reasoning: All probes return 403 consistently (admin, token, internal IPs). The knowledge base has ACCEPTED entries for AUTH @ coxautoinc.com with 403 remaining after header tests. Common bypasses (X-Forwarded-For, Authorization: Bearer null, X-Original-URL) have not been systematically tested. The 403 pattern suggests a WAF or API gateway blocking unauthorized requests rather than a simple 401/403 application response.
evidence_needed: Test with X-Forwarded-For: 127.0.0.1, Authorization: Bearer null, X-Original-URL: /admin, X-Rewrite-URL: /admin. If all return 403, class is proven dead.
verify_steps: 1. curl -H "X-Forwarded-For: 127.0.0.1" https://api.coxautoinc.com/endpoint. 2. curl -H "Authorization: Bearer null" https://api.coxautoinc.com/endpoint. 3. curl -H "X-Original-URL: /admin" https://api.coxautoinc.com/endpoint.
impact: Severity HIGH if any header bypass works (access to internal API). Severity NONE if all return 403.
testability: AUTH_HELPED
[PARKED] SSRF via parameter manipulation @ api.coxautoinc.com: confidence 55 but class on REJECTED list per knowledge base "2026-08-20 REJECTED SSRF @ https://api.coxautoinc.com/endpoint: 403 responses for internal IPs may be normal network segmentation"
[FINAL]
[NEXT] PROBE: GET https://api.emsisoft.com/v1/workspaces/ddaaa9b5-9985-4028-8ff7-f12255a168e9 — test if the example GUID from swagger spec returns data or 401
[LEARN] ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: Full 365KB+ OpenAPI spec with example GUIDs, emails, billing data publicly accessible without auth
[LEARN] ACCEPTED AUTH @ auth.docker.com: dckr-sessid cookie set on unauthenticated requests, x-docker-app-version and x-trace-id leaked
[LEARN] REJECTED SSRF @ api.coxautoinc.com/endpoint: 403 consistent across all param values — class dead
[RISK] 58
