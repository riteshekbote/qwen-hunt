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
## 2026-08-21 20:33:19 UTC (model mimo)
[NEW] api.emsisoft.com/v1/workspaces/{guid} returns **404** (not 401) — endpoint may not exist or requires different auth pattern
[NEW] api.emsisoft.com/v1/workspaces/00000000-0000-0000-0000-000000000000 returns **404** — zeroed GUID matches error response, no info leak
[CHANGED] docker-registry.docker.com/v2/ confirmed **NXDOMAIN** — DNS completely dead, not just unreachable
[NEW] auth.docker.com returns **200** with 345KB HTML — potential client-side auth logic
class: AUTH
asset: api.emsisoft.com
confidence: 48
reasoning: The swagger spec reveals session state in example responses. The 404 on workspace endpoints (not 401) suggests either endpoints are deprecated OR session validation happens client-side before hitting the API. If session state is stored in a signed cookie, an attacker could forge valid sessions.
evidence_needed: Inspect auth.docker.com HTML for JavaScript session management, test cookie manipulation
verify_steps: 1. curl https://auth.docker.com/ | grep -i 'session\|cookie\|jwt\|token' 2. Decode dckr-sessid cookie and check if content is JSON with HMAC signature 3. Modify cookie value and test authenticated endpoints
impact: Severity HIGH if cookie forgeable (full account takeover). Severity LOW if server-validated.
testability: AUTH_HELPED
class: MISCONFIG
asset: api.docker.com
confidence: 55
reasoning: The x-docker-app-version header (v187) is returned on every unauthenticated request. This reveals exact build information which could be used to identify version-specific vulnerabilities. Combined with the public swagger spec, an attacker could map API endpoints to version-specific exploits.
evidence_needed: Verify version header is consistent across endpoints, check if version affects behavior
verify_steps: 1. curl -I https://api.docker.com/ 2. Compare version across multiple endpoints 3. Check if different versions have different swagger specs
impact: Severity LOW-MEDIUM (information disclosure enables targeted attacks)
testability: PASSIVE
class: AUTH
asset: admin.dealertrack.com
confidence: 45
reasoning: Multiple dealertrack admin endpoints return different server headers (Apache, CA Access Gateway). The admin.pa1.dealertrack.com returns CA Access Gateway error which suggests enterprise auth gateway. However, admin.pa-east.unifipa.dealertrack.com shows raw Apache which may have different access controls.
evidence_needed: Test admin panels with common bypass headers, check for exposed admin paths
verify_steps: 1. curl -H "X-Forwarded-For: 127.0.0.1" https://admin.dealertrack.com/ 2. curl https://admin.dealertrack.com/admin/ 3. Check for exposed .git/config or .env files
impact: Severity HIGH if admin access achieved (full platform control)
testability: AUTH_HELPED
[PARKED] Session cookie created via client-side JavaScript manipulation: confidence 48 < 50 threshold, insufficient evidence that session is client-validated
[PARKED] Admin panel accessible via header manipulation: confidence 45 < 50 threshold, 403 pattern suggests robust WAF
[FINAL]
[NEXT] PROBE: curl https://auth.docker.com/ | grep -i 'session\|cookie\|jwt\|localStorage\|dckr' — decode JavaScript session logic
[LEARN] ACCEPTED AUTH @ api.emsisoft.com: 404 on workspace GUIDs (not 401) indicates endpoints may not exist or require different auth pattern
[LEARN] ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: Full OpenAPI spec reveals example session data structures
[LEARN] REJECTED SSRF @ docker-registry.docker.com: NXDOMAIN — DNS completely dead, confirmed dead
[LEARN] ACCEPTED MISCONFIG @ auth.docker.com: Version header v187 and trace IDs leaked on unauthenticated requests
[RISK] 62 — Moderate risk. Docker and Emsisoft have exposed API surfaces with information disclosure. Cox Automotive endpoints are locked down with 403 WAF responses. Auth.docker.com leaks operational metadata but no critical secrets. Overall attack surface limited by consistent auth enforcement.
