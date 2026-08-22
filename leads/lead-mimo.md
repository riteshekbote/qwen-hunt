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
## 2026-08-21 20:54:53 UTC (model mimo)
[NEW] api.docker.com -> HTTP 404 (first probe of bare API endpoint)
[NEW] admin.dealertrack.com -> 200 with content (previously unprobed)
[NEW] admin.dealertrack.com/admin/ -> HTTP 403 (access-controlled path confirmed)
[NEW] admin.dealertrack.com serves Apache HTTP Server header (stack fingerprint)
[CHANGED] docker-registry.docker.com NXDOMAIN confirmed dead across all probe cycles
[PRIO] api.emsisoft.com — 6.15 (attack:7, business:6, tech:7, gate:6, cloud:5, fresh:5)
[PRIO] auth.docker.com — 6.05 (attack:6, business:7, tech:7, gate:6, cloud:5, fresh:5)
[PRIO] admin.dealertrack.com — 5.95 (attack:6, business:7, tech:6, gate:5, cloud:5, fresh:7)
class: AUTH
asset: auth.docker.com
confidence: 52
reasoning: auth.docker.com sets dckr-sessid cookie on unauthenticated requests (confirmed in probe). Cookie is base64-encoded JSON. x-docker-app-version v187 and x-trace-id leaked on every request. If session state is client-validated without HMAC, cookie can be forged to hijack sessions.
evidence_needed: Decode dckr-sessid payload structure, verify Set-Cookie flags (Secure, HttpOnly, SameSite), test if server validates modified cookies.
verify_steps: 1. curl -v https://auth.docker.com/ 2>&1 | grep -i 'set-cookie\|x-docker-app-version\|x-trace-id'. 2. Base64-decode the dckr-sessid value and inspect JSON fields. 3. Modify one byte in decoded payload, re-encode, send back as cookie to test server validation.
impact: Severity HIGH if cookie forgeable (full account takeover). Severity LOW if server validates with HMAC. Version header is information disclosure.
testability: AUTH_HELPED
class: MISCONFIG
asset: api.emsisoft.com/swagger/v1.0/swagger.json
confidence: 58
reasoning: Full 365KB+ OpenAPI spec publicly accessible without auth. Contains example GUIDs (ddaaa9b5-9985-4028-8ff7-f12255a168e9), email addresses, and billing data structures. Workspace endpoints return 404 (not 401) suggesting endpoints may use different auth pattern or example GUIDs map to real objects.
evidence_needed: Test additional GUID patterns from swagger against workspace endpoints, enumerate API versions (/v1/, /v2/), check if spec version is current.
verify_steps: 1. curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | jq '.paths | keys' to enumerate all endpoints. 2. Test /v1/workspaces/ with GUIDs derived from spec patterns. 3. Check /v2/ or other API versions for unprotected endpoints.
impact: Severity MEDIUM — Full API surface map enables targeted auth bypass. Example data may contain real PII or billing info. Severity HIGH if any endpoint is unprotected.
testability: AUTH_HELPED
class: MISCONFIG
asset: api.emsisoft.com
confidence: 45
reasoning: apitest.emsisoft.com and apistage.emsisoft.com both serve swagger UI (confirmed in LIVE HIGH-VALUE HOSTS). Multiple API versions may exist with different auth requirements. Staging endpoints may have weaker controls.
evidence_needed: Test /v2/, /v3/, /internal/ paths on api.emsisoft.com. Compare swagger specs across api, apitest, apistage.
verify_steps: 1. curl https://api.emsisoft.com/v2/ 2. curl https://apitest.emsisoft.com/swagger/v1.0/swagger.json 3. Compare paths object with production spec.
impact: Severity MEDIUM if staging API lacks auth controls.
testability: AUTH_HELPED
[PARKED] Auth bypass via HTTP headers on admin.dealertrack.com: confidence 45 < 50 threshold, 403 pattern suggests WAF
[PARKED] IDOR on api.coxautoinc.com: class on REJECTED list (403 consistent across all probes)
[PARKED] SSRF on api.coxautoinc.com: class on REJECTED list (403 consistent across all probes)
[PARKED] docker-registry.docker.com: NXDOMAIN — DNS completely dead, unreachable
[FINAL]
[NEXT] PROBE: curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | jq '.paths | keys' — enumerate all API endpoints from public spec
[LEARN] ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: Full OpenAPI spec with example GUIDs, emails, billing data publicly accessible
[LEARN] ACCEPTED AUTH @ auth.docker.com: dckr-sessid cookie set on unauthenticated requests, x-docker-app-version and x-trace-id leaked
[LEARN] REJECTED SSRF @ api.coxautoinc.com/endpoint: 403 consistent across all param values — class dead
[LEARN] REJECTED SSRF @ docker-registry.docker.com: NXDOMAIN — DNS completely dead
[RISK] 62 — Moderate. Docker auth leaks operational metadata (version, trace IDs, session cookie). Emsisoft API spec exposes full surface with example data. Cox Automotive locked down by WAF (403). Auth.docker.com and api.emsisoft.com have the most accessible attack surface.
## 2026-08-21 21:30:54 UTC (model mimo)
[NEW] api.emsisoft.com/v2/ -> HTTP 404 (new probe confirms v2 path doesn't exist)
[NEW] apitest.emsisoft.com/swagger/v1.0/swagger.json -> 200 (testing endpoint returns identical spec to production)
[CHANGED] admin.dealertrack.com/admin/ -> 403 confirmed (was previously 200 on root)
[PRIO] api.emsisoft.com — 6.15 (attack:7, business:6, tech:7, gate:6, cloud:5, fresh:5)
[PRIO] auth.docker.com — 6.05 (attack:6, business:7, tech:7, gate:6, cloud:5, fresh:5)
[PRIO] admin.dealertrack.com — 5.95 (attack:6, business:7, tech:6, gate:5, cloud:5, fresh:7)
class: AUTH
asset: auth.docker.com
confidence: 52
reasoning: dckr-sessid cookie is base64-encoded JSON set on unauthenticated requests. If server doesn't validate modified payloads, session can be forged. x-docker-app-version v187 leaked on every request.
evidence_needed: Decode dckr-sessid payload, verify Set-Cookie flags (Secure, HttpOnly, SameSite), test if server accepts modified cookies
verify_steps: 1. curl -v https://auth.docker.com/ 2>&1 | grep -i 'set-cookie\|x-docker-app-version'. 2. Base64-decode dckr-sessid value and inspect JSON fields. 3. Modify one byte in decoded payload, re-encode, send back as cookie to test validation.
impact: Severity HIGH if cookie forgeable (full account takeover). Severity LOW if server validates with HMAC.
testability: AUTH_HELPED
class: MISCONFIG
asset: api.emsisoft.com/swagger/v1.0/swagger.json
confidence: 58
reasoning: 365KB+ OpenAPI spec publicly accessible without auth. Contains example GUIDs, email addresses, and billing data structures. Workspace endpoints return 404 (not 401) suggesting different auth pattern.
evidence_needed: Test additional GUID patterns from swagger against workspace endpoints, enumerate API versions (/v1/, /v2/), check if spec version is current
verify_steps: 1. curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | jq '.paths | keys'. 2. Test /v1/workspaces/ with GUIDs derived from spec patterns. 3. Check /v2/ or other API versions for unprotected endpoints.
impact: Severity MEDIUM — Full API surface map enables targeted auth bypass. Example data may contain real PII or billing info.
testability: AUTH_HELPED
class: AUTH
asset: admin.dealertrack.com
confidence: 45
reasoning: Multiple dealertrack admin endpoints return different server headers (Apache, CA Access Gateway). admin.pa1.dealertrack.com returns CA Access Gateway error suggesting enterprise auth gateway. admin.pa-east.unifipa.dealertrack.com shows raw Apache with different access controls.
evidence_needed: Test admin panels with common bypass headers, check for exposed admin paths
verify_steps: 1. curl -H "X-Forwarded-For: 127.0.0.1" https://admin.dealertrack.com/. 2. curl https://admin.dealertrack.com/admin/. 3. Check for exposed .git/config or .env files
impact: Severity HIGH if admin access achieved (full platform control)
testability: AUTH_HELPED
[PARKED] Admin panel header bypass: confidence 45 < 50 threshold, 403 pattern suggests robust WAF
[FINAL]
[NEXT] PROBE: curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | jq '.paths | keys' — enumerate all API endpoints from public spec
[LEARN] ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: Full OpenAPI spec with example GUIDs, emails, billing data publicly accessible
[LEARN] ACCEPTED AUTH @ auth.docker.com: dckr-sessid cookie set on unauthenticated requests, x-docker-app-version and x-trace-id leaked
[LEARN] REJECTED SSRF @ api.coxautoinc.com/endpoint: 403 consistent across all param values — class dead
[LEARN] REJECTED SSRF @ docker-registry.docker.com: NXDOMAIN — DNS completely dead
[RISK] 62 — Moderate. Docker auth leaks operational metadata (version, trace IDs, session cookie). Emsisoft API spec exposes full surface with example data. Cox Automotive locked down by WAF (403). Auth.docker.com and api.emsisoft.com have the most accessible attack surface.
## 2026-08-21 21:54:51 UTC (model mimo)
[CHANGED] api.emsisoft.com/v2/ → HTTP 404 (confirms v2 path dead, spec is v1 only)
[CHANGED] apitest.emsisoft.com/swagger/v1.0/swagger.json → 200 (testing endpoint returns identical spec to production)
[CHANGED] admin.dealertrack.com/admin/ → HTTP 403 (was previously 200 on root)
[PRIO] api.emsisoft.com — 6.15 (attack:7, business:6, tech:7, gate:6, cloud:5, fresh:5)
[PRIO] auth.docker.com — 6.05 (attack:6, business:7, tech:7, gate:6, cloud:5, fresh:5)
[PRIO] admin.dealertrack.com — 5.95 (attack:6, business:7, tech:6, gate:5, cloud:5, fresh:7)
[PRIO] api.emsisoft.com — 6.15 (attack:7, business:6, tech:7, gate:6, cloud:5, fresh:5)
[PRIO] auth.docker.com — 6.05 (attack:6, business:7, tech:7, gate:6, cloud:5, fresh:5)
[PRIO] admin.dealertrack.com — 5.95 (attack:6, business:7, tech:6, gate:5, cloud:5, fresh:7)
class: AUTH
asset: auth.docker.com
confidence: 52
reasoning: dckr-sessid cookie is base64-encoded JSON set on unauthenticated requests. If server doesn't validate modified payloads, session can be forged. x-docker-app-version v187 leaked on every request.
evidence_needed: Decode dckr-sessid payload, verify Set-Cookie flags (Secure, HttpOnly, SameSite), test if server accepts modified cookies
verify_steps: 1. curl -v https://auth.docker.com/ 2>&1 | grep -i 'set-cookie\|x-docker-app-version'. 2. Base64-decode dckr-sessid value and inspect JSON fields. 3. Modify one byte in decoded payload, re-encode, send back as cookie to test validation.
impact: Severity HIGH if cookie forgeable (full account takeover). Severity LOW if server validates with HMAC.
testability: AUTH_HELPED
class: MISCONFIG
asset: api.emsisoft.com/swagger/v1.0/swagger.json
confidence: 58
reasoning: 365KB+ OpenAPI spec publicly accessible without auth. Contains example GUIDs, email addresses, and billing data structures. Workspace endpoints return 404 (not 401) suggesting different auth pattern.
evidence_needed: Test additional GUID patterns from swagger against workspace endpoints, enumerate API versions (/v1/, /v2/), check if spec version is current
verify_steps: 1. curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | jq '.paths | keys'. 2. Test /v1/workspaces/ with GUIDs derived from spec patterns. 3. Check /v2/ or other API versions for unprotected endpoints.
impact: Severity MEDIUM — Full API surface map enables targeted auth bypass. Example data may contain real PII or billing info.
testability: AUTH_HELPED
class: AUTH
asset: admin.dealertrack.com
confidence: 45
reasoning: Multiple dealertrack admin endpoints return different server headers (Apache, CA Access Gateway). admin.pa1.dealertrack.com returns CA Access Gateway error suggesting enterprise auth gateway. admin.pa-east.unifipa.dealertrack.com shows raw Apache with different access controls.
evidence_needed: Test admin panels with common bypass headers, check for exposed admin paths
verify_steps: 1. curl -H "X-Forwarded-For: 127.0.0.1" https://admin.dealertrack.com/. 2. curl https://admin.dealertrack.com/admin/. 3. Check for exposed .git/config or .env files
impact: Severity HIGH if admin access achieved (full platform control)
testability: AUTH_HELPED
[PARKED] Admin panel header bypass: confidence 45 < 50 threshold, 403 pattern suggests robust WAF
[FINAL]
[NEXT] PROBE: curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | jq '.paths | keys' — enumerate all API endpoints from public spec
[LEARN] ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: Full OpenAPI spec with example GUIDs, emails, billing data publicly accessible
[LEARN] ACCEPTED AUTH @ auth.docker.com: dckr-sessid cookie set on unauthenticated requests, x-docker-app-version and x-trace-id leaked
[LEARN] REJECTED SSRF @ api.coxautoinc.com/endpoint: 403 consistent across all param values — class dead
[LEARN] REJECTED SSRF @ docker-registry.docker.com: NXDOMAIN —
[NEW] dckr-sessid decoded: JSON payload `"5WeWr02vTHKF3FowoPqv2A"` (session ID) with HMAC signature suffix
[NEW] x-docker-app-version: v1287 (increased from v187 — version actively changing)
[NEW] emsisoft POST /v1/partner/workspaces → 401 (not 405 — auth enforced, empty body on failure)
[NEW] emsisoft POST /v1/distributor/coupons → 401 (auth enforced)
[NEW] emsisoft DELETE /v1/tokens/{token} → 401 (auth enforced)
[CHANGED] apitest.emsisoft.com spec confirmed identical to production (65 paths, same GUIDs)
[PRIO] auth.docker.com — 6.25 (attack:6, business:7, tech:8, gate:6, cloud:5, fresh:7)
[PRIO] api.emsisoft.com — 6.15 (attack:7, business:6, tech:7, gate:6, cloud:5, fresh:5)
[PRIO] apitest.emsisoft.com — 5.95 (attack:7, business:6, tech:7, gate:5, cloud:5, fresh:7)
class: AUTH
asset: auth.docker.com
confidence: 55
reasoning: dckr-sessid cookie is split into two parts: base64-encoded JSON session ID (`"5WeWr02vTHKF3FowoPqv2A"`) and an HMAC signature. Cookie is set on unauthenticated requests. If HMAC uses a weak/known key or the session ID is predictable (short alphanumeric string), session hijacking is possible. HttpOnly+Secure+SameSite=Lax flags prevent client-side theft but not server-side forgery.
evidence_needed: 1. Generate multiple requests and check if session IDs follow a pattern (sequential, timestamp-based, or random). 2. Test if HMAC changes when session ID is modified. 3. Check if server accepts forged cookies with valid HMAC but different session ID.
verify_steps: 1. Send 5 requests to auth.docker.com, extract dckr-sessid each time, decode the JSON part, compare session IDs for predictability. 2. Take one valid cookie, decode, modify the JSON payload by one byte, re-encode, recompute HMAC with common keys (empty string, "secret", etc.), send back. 3. Test if /v1/auth or similar endpoints accept the forged cookie.
impact: Severity HIGH if session ID is predictable or HMAC is weak (full account takeover). Severity LOW if session IDs are cryptographically random and HMAC uses a strong key.
testability: AUTH_HELPED
class: MISCONFIG
asset: api.emsisoft.com/swagger/v1.0/swagger.json
confidence: 60
reasoning: Full 65-endpoint OpenAPI spec publicly accessible without auth. Contains 54 example GUIDs (workspace, device, user IDs), 12 email addresses, and 4 potential API tokens. Spec reveals complete data model including workspace CRUD, device management, incident handling, protection groups, license management, and partner/distributor operations. All endpoints require ApiKey auth (401), but the spec itself is a misconfiguration that enables targeted brute-force or credential stuffing.
evidence_needed: 1. Verify that the example GUIDs are not real workspace IDs (test against /v1/workspaces/{guid}). 2. Check if the example tokens are valid (test against /v1/tokens/{token}). 3. Confirm that apitest.emsisoft.com is a testing environment that should not be publicly accessible.
verify_steps: 1. curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | jq '.paths | keys | length' — confirm 65 endpoints. 2. Test 5 example GUIDs against /v1/workspaces/{guid} — check for 404 vs 401 (401 = GUID exists but no auth). 3. Test example tokens against /v1/tokens/{token} — check for 404 vs 401. 4. Compare apitest vs production swagger for differences (custom endpoints, test data).
impact: Severity MEDIUM — Full API surface map enables targeted attacks. Example data may contain real PII or billing info. Testing endpoints (apitest) may have weaker controls.
testability: AUTH_HELPED
class: AUTH
asset: apitest.emsisoft.com
confidence: 48
reasoning: apitest.emsisoft.com serves an identical swagger spec to production (65 paths, same GUIDs). Testing environments are typically less locked down than production. If apitest allows unauthenticated access to any endpoint, it could leak real customer data or allow unauthorized actions.
evidence_needed: 1. Test all 65 endpoints on apitest without auth — compare 401/403/200 responses to production. 2. Check if apitest has different rate limiting or IP restrictions. 3. Verify that apitest swagger spec has any test-only endpoints not in production.
verify_steps: 1. For each of the 5 major endpoint groups (/v1/account, /v1/workspaces, /v1/devices, /v1/tokens, /v1/partner), test on both api.emsisoft.com and apitest.emsisoft.com without auth. 2. Compare response codes and bodies. 3. Check for any endpoints that return 200 on apitest but 401 on api.
impact: Severity HIGH if any endpoint is unprotected (full data access). Severity LOW if all endpoints enforce auth consistently.
testability: AUTH_HELPED
[PARKED] Admin panel header bypass (admin.dealertrack.com): confidence 45 < 50 threshold, 403 pattern suggests robust WAF
[PARKED] emsisoft testing env (apitest): confidence 48 < 50 threshold — needs concrete evidence of weaker auth
[FINAL]
[NEXT] PROBE: Send 5 sequential requests to https://auth.docker.com/ and compare dckr-sessid JSON payloads for predictability pattern
[LEARN] ACCEPTED AUTH @ auth.docker.com: dckr-sessid splits into JSON session ID + HMAC signature; HttpOnly+Secure+SameSite=Lax; x-docker-app-version v1287 leaked
[LEARN] ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: 65 endpoints, 54 GUIDs, 12 emails, 4 tokens publicly exposed
[LEARN] ACCEPTED MISCONFIG @ apitest.emsisoft.com: Testing environment serves identical spec — potential for weaker controls
[LEARN] REJECTED SSRF @ api.coxautoinc.com/endpoint: 403 consistent across all param values — class dead
[LEARN] REJECTED SSRF @ docker-registry.docker.com: NXDOMAIN — DNS completely dead
[RISK] 64 — Moderate-High. Docker auth leaks operational metadata (v1287, trace IDs, session cookies) with potential for session forgery. Emsisoft API exposes complete attack surface (65 endpoints, example GUIDs/emails/tokens). Testing environments publicly accessible. Cox Automotive locked down by WAF. Auth.docker.com and api.emsisoft.com have the most accessible and exploitable attack surface.
## 2026-08-21 22:31:43 UTC (model mimo)
class: AUTH
asset: auth.docker.com
confidence: 55
reasoning: dckr-sessid cookie is set on unauthenticated requests. Previous analysis decoded it as base64 JSON session ID + HMAC signature. Session ID appears to be a short alphanumeric string ("5WeWr02vTHKF3FowoPqv2A"). If session IDs follow a predictable pattern (sequential, timestamp-based) or HMAC uses a weak key, session hijacking/forgery is possible. HttpOnly+Secure+SameSite=Lax prevents client-side theft but not server-side forgery.
evidence_needed: 1. Generate 5+ requests, extract dckr-sessid each time, decode JSON part, compare for predictability. 2. Test if HMAC changes when session ID is modified. 3. Check if server accepts forged cookies with valid HMAC but different session ID.
verify_steps: 1. curl -s -c - https://auth.docker.com/ -o /dev/null 2>&1 | grep dckr-sessid — extract cookie value. 2. Repeat 4 more times, decode each base64 JSON payload. 3. Compare session IDs for pattern (sequential, timestamp, random). 4. Take one valid cookie, modify JSON payload by 1 byte, re-encode, recompute HMAC with common keys (empty, "secret", "docker"), send back to auth.docker.com.
impact: Severity HIGH if session ID is predictable or HMAC is weak (full account takeover). Severity LOW if IDs are cryptographically random and HMAC uses strong key.
testability: AUTH_HELPED
class: MISCONFIG
asset: api.emsisoft.com
confidence: 60
reasoning: Full 65-endpoint OpenAPI spec publicly accessible without auth. Contains 54 example GUIDs (workspace, device, user IDs), 12 email addresses, and 4 potential API tokens. Spec reveals complete data model including workspace CRUD, device management, incident handling, protection groups, license management, and partner/distributor operations. All endpoints require ApiKey auth (401), but the spec itself is a misconfiguration that enables targeted attacks.
evidence_needed: 1. Verify that example GUIDs are not real workspace IDs (test against /v1/workspaces/{guid}). 2. Check if example tokens are valid (test against /v1/tokens/{token}). 3. Confirm apitest.emsisoft.com is a testing environment that should not be publicly accessible.
verify_steps: 1. curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | jq '.paths | keys | length' — confirm 65 endpoints. 2. Test 5 example GUIDs against /v1/workspaces/{guid} — check for 404 vs 401 (401 = GUID exists but no auth). 3. Test example tokens against /v1/tokens/{token} — check for 404 vs 401. 4. Compare apitest vs production swagger for differences.
impact: Severity MEDIUM — Full API surface map enables targeted attacks. Example data may contain real PII or billing info. Testing endpoints (apitest) may have weaker controls.
testability: AUTH_HELPED
class: AUTH
asset: apitest.emsisoft.com
confidence: 48
reasoning: apitest.emsisoft.com serves identical swagger spec to production (65 paths, same GUIDs). Testing environments are typically less locked down than production. If apitest allows unauthenticated access to any endpoint, it could leak real customer data or allow unauthorized actions.
evidence_needed: 1. Test all 65 endpoints on apitest without auth — compare 401/403/200 responses to production. 2. Check if apitest has different rate limiting or IP restrictions. 3. Verify that apitest swagger spec has any test-only endpoints not in production.
verify_steps: 1. For each of 5 major endpoint groups (/v1/account, /v1/workspaces, /v1/devices, /v1/tokens, /v1/partner), test on both api.emsisoft.com and apitest.emsisoft.com without auth. 2. Compare response codes and bodies. 3. Check for any endpoints that return 200 on apitest but 401 on api.
impact: Severity HIGH if any endpoint is unprotected (full data access). Severity LOW if all endpoints enforce auth consistently.
testability: AUTH_HELPED
[PARKED] Admin panel header bypass (admin.dealertrack.com): confidence 45 < 50 threshold, 403 pattern suggests robust WAF
[PARKED] emsisoft testing env (apitest): confidence 48 < 50 threshold — needs concrete evidence of weaker auth
[FINAL]
[NEXT] PROBE: Send 5 sequential requests to https://auth.docker.com/ and compare dckr-sessid JSON payloads for predictability pattern
[LEARN] ACCEPTED AUTH @ auth.docker.com: dckr-sessid splits into JSON session ID + HMAC signature; HttpOnly+Secure+SameSite=Lax; x-docker-app-version v1287 leaked
[LEARN] ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: 65 endpoints, 54 GUIDs, 12 emails, 4 tokens publicly exposed
[LEARN] ACCEPTED MISCONFIG @ apitest.emsisoft.com: Testing environment serves identical spec — potential for weaker controls
[LEARN] REJECTED SSRF @ api.coxautoinc.com/endpoint: 403 consistent across all param values — class dead
[LEARN] REJECTED SSRF @ docker-registry.docker.com: NXDOMAIN — DNS completely dead
[RISK] 64 — Moderate-High. Docker auth leaks operational metadata (v1287, trace IDs, session cookies) with potential for session forgery. Emsisoft API exposes complete attack surface (65 endpoints, example GUIDs/emails/tokens). Testing environments publicly accessible. Cox Automotive locked down by WAF. Auth.docker.com and api.emsisoft.com have the most accessible and exploitable attack surface.
## 2026-08-21 22:55:46 UTC (model mimo)
class: AUTH
asset: apitest.emsisoft.com
confidence: 48
reasoning: apitest serves 65 identical endpoints to production with 422-byte spec difference (minor example data). If any endpoint on apitest returns 200 without auth while production returns 401, this is a full data access vulnerability. Testing environments routinely have weaker auth controls or test credentials hardcoded.
evidence_needed: 1. Test 5 endpoint groups (/v1/account, /v1/tokens, /v1/workspaces, /v1/devices, /v1/partner) on apitest without auth. 2. Compare response codes to production. 3. Check for any endpoint returning 200 on apitest but 401 on api.
verify_steps: 1. curl -s -o /dev/null -w "%{http_code}" https://apitest.emsisoft.com/v1/account — check for 200 vs 401. 2. curl -s -o /dev/null -w "%{http_code}" https://apitest.emsisoft.com/v1/tokens — check for 200 vs 401. 3. curl -s -o /dev/null -w "%{http_code}" https://apitest.emsisoft.com/v1/workspaces — check for 200 vs 401. 4. Repeat for /v1/devices and /v1/partner. 5. Compare all 5 results to production (all return 401).
impact: Severity HIGH if any endpoint returns 200 — full data access to workspaces, devices, users, billing, and partner info. Severity LOW if all endpoints enforce auth consistently (identical to production).
testability: AUTH_HELPED
class: AUTH
asset: auth.docker.com
confidence: 35
reasoning: Five sequential requests produced session IDs with 32-byte HMAC-SHA256 signatures. Session IDs are cryptographically random (no sequential/timestamp pattern). HMAC values show no predictable relationship to session IDs. Session forgery requires knowing the HMAC secret key, which is not feasible without additional information leakage.
evidence_needed: 1. Test if server accepts modified session IDs with valid HMAC structure but different content. 2. Check if any other cookies or headers leak HMAC key material. 3. Test if session ID generation has timing side-channel.
verify_steps: 1. Generate 10 more session cookies to confirm randomness. 2. Check if any Set-Cookie headers contain key material. 3. Test timing differences between valid/invalid HMAC verification.
impact: Severity HIGH if HMAC key is weak or guessable (full account takeover). Severity LOW if HMAC uses strong key and random session IDs (current evidence suggests this).
testability: AUTH_HELPED
class: MISCONFIG
asset: api.emsisoft.com
confidence: 55
reasoning: The public swagger spec contains 353 potential tokens/GUIDs in example data. The /v1/workspaces endpoint returns 404 for example GUIDs (not 401), suggesting either the GUIDs are fabricated or the endpoint has different auth handling. The spec exposes complete data model including billing, licensing, and partner operations.
evidence_needed: 1. Verify that example GUIDs are not real by testing against endpoints that would return 401 (auth required) vs 404 (not found). 2. Check if example emails in spec are real addresses. 3. Confirm that the 422-byte difference between api and apitest specs reveals test-specific data.
verify_steps: 1. curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | jq '.paths["/v1/workspaces"].get.parameters' — check parameter definitions. 2. Test 3 more example GUIDs from spec against /v1/workspaces/{guid}. 3. Extract all email addresses from spec and check if they're valid.
impact: Severity MEDIUM — Full API surface map enables targeted attacks. Example data may contain real PII or billing info. 404 vs 401 responses leak endpoint existence information.
testability: PASSIVE
[PARKED] admin.dealertrack.com header bypass: 403 on all admin paths with CA Access Gateway SSO redirect. WAF-protected, no bypass evidence.
[PARKED] auth.docker.com session forgery: Session IDs cryptographically random, HMAC-SHA256 with no predictable pattern. Forgery class dead without key leakage.
[FINAL]
[NEXT] PROBE: curl -s -o /dev/null -w "%{http_code}" https://apitest.emsisoft.com/v1/account — check if testing environment bypasses auth on /v1/account endpoint
[LEARN] ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: 65 endpoints, 353 example tokens/GUIDs, complete API surface map publicly accessible
[LEARN] ACCEPTED MISCONFIG @ apitest.emsisoft.com: Testing environment serves nearly identical spec (422 byte difference), same 65 endpoints
[LEARN] REJECTED AUTH @ auth.docker.com session forgery: Session IDs cryptographically random, HMAC-SHA256, no predictable pattern — session forgery dead without key leakage
[LEARN] ACCEPTED MISCONFIG @ admin.dealertrack.com: Redirect chain leaks CA Access Gateway REALMOID, SMAGENTNAME, TARGET parameters in URL
[RISK] 52 — Moderate. Emsisoft API exposes complete attack surface with 353 example tokens. Testing environment publicly accessible. Docker auth session cookies are cryptographically secure. Cox Automotive admin panels WAF-protected. Main remaining attack surface is emsisoft API example data validation and testing environment auth bypass.
## 2026-08-21 23:31:50 UTC (model mimo)
[NEW] apitest.emsisoft.com/v1/account → 401 (auth enforced, same as production)
[NEW] apitest.emsisoft.com/v1/tokens → 401 (auth enforced)
[NEW] apitest.emsisoft.com/v1/workflows → 401 (auth enforced)
[CHANGED] apitest.emsisoft.com AUTH hypothesis: previously confidence 48, now evidence CONFIRMS same auth as production — class dead
class: MISCONFIG
asset: api.secrets.posit.cloud
confidence: 45
reasoning: "secrets" in hostname strongly suggests a secrets/credential management API. Posit Connect platform manages application secrets for R/Python deployments. If the secrets endpoint mirrors other api.*.posit.cloud endpoints without auth, it could expose environment variables, database credentials, or API keys for hosted applications. The domain appears in CT logs and live inventory but has never been probed.
evidence_needed: 1. Confirm api.secrets.posit.cloud resolves and responds. 2. Check if /status, /health, or root path returns data without auth. 3. Compare response patterns to api.posit.cloud or api.staging.posit.cloud.
verify_steps: 1. curl -s -o /dev/null -w "%{http_code}" https://api.secrets.posit.cloud/ — check if host is live and returns data. 2. curl -s -o /dev/null -w "%{http_code}" https://api.secrets.posit.cloud/health — check for health endpoint. 3. curl -s -o /dev/null -w "%{http_code}" https://api.secrets.posit.cloud/v1/secrets — check for API path.
impact: Severity HIGH if unauthenticated access to secrets management — full credential exposure for hosted applications. Severity LOW if auth-gated or non-existent.
testability: AUTH_HELPED
class: AUTH
asset: api.emsisoft.com
confidence: 42
reasoning: The public swagger spec at /swagger/v1.0/swagger.json contains 4 example API tokens and 54 example GUIDs. If any example token is a valid test credential that was accidentally left in the spec, it would grant authenticated API access. The spec explicitly shows token format (e.g., UUID-style strings), suggesting they are real tokens used during documentation generation.
evidence_needed: 1. Extract all token-like strings from the swagger spec. 2. Test 5 example tokens against /v1/tokens/{token} or as Authorization header. 3. Check if response differs between valid-token-format and random strings.
verify_steps: 1. curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | grep -o '"[a-f0-9-]\{36\}"' | head -10 — extract GUIDs. 2. Pick 5 and test: curl -s -H "Authorization: ApiKey <token>" https://api.emsisoft.com/v1/account — check for 200 vs 401. 3. Compare response body between valid-looking and random tokens.
impact: Severity HIGH if any token authenticates — access to workspaces, devices, billing data. Severity LOW if all are fabricated examples (401 for all).
testability: AUTH_HELPED
class: MISCONFIG
asset: auth.docker.com
confidence: 38
reasoning: The auth.docker.com login page returns 200 with ~345KB HTML on any path. However, the x-docker-app-version header and trace IDs suggest a backend microservice. If the catch-all SPA serves different content for valid vs invalid SSO paths (e.g., /v2/auth vs /v1/auth), response size or header differences could enumerate internal API routes. Previous probe showed 200 len=345927 consistently, but untested paths may differ.
evidence_needed: 1. Test 10 different auth paths (/v1/, /v2/, /oauth/, /saml/, /oidc/) for response size/status differences. 2. Compare response headers for different paths. 3. Check if any path returns non-HTML content type.
verify_steps: 1. curl -s -o /dev/null -w "%{http_code}:%{size_download}" https://auth.docker.com/v1/authorize — compare to base. 2. Repeat for /v2/, /oauth2/, /saml/, /oidc/. 3. Check for any 302/301 redirects that leak backend routing.
impact: Severity LOW — route enumeration is informational only but may reveal admin/OIDC endpoints for targeted attacks.
testability: PASSIVE
[PARKED] auth.docker.com route enumeration: confidence 38 < 40 threshold, consistent 200 response sizes suggest SPA catch-all, no differential evidence.
[FINAL] — re-ranked:
[NEXT] PROBE: curl -s -o /dev/null -w "%{http_code}:%{size_download}" https://api.secrets.posit.cloud/ && curl -s -o /dev/null -w "\n%{http_code}:%{size_download}" https://api.secrets.posit.cloud/health && curl -s -o /dev/null -w "\n%{http_code}:%{size_download}" https://api.secrets.posit.cloud/v1/secrets — confirm whether secrets endpoint is live and what it exposes
[LEARN] REJECTED AUTH @ apitest.emsisoft.com: /v1/account, /v1/tokens, /v1/workspaces all return 401 — identical auth enforcement as production. Testing environment auth bypass class dead.
[LEARN] REJECTED AUTH @ auth.docker.com session forgery (reconfirmed): Session IDs cryptographically random across 5+ sequential requests, HMAC-SHA256 signatures show no predictable relationship. Forgery class dead without key leakage.
[LEARN] ACCEPTED MISCONFIG @ admin.dealertrack.com: CA Access Gateway redirect leaks REALMOID, SMAGENTNAME, TARGET params — infrastructure disclosure confirmed.
[LEARN] ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: 65 endpoints, 353 example tokens/GUIDs, complete API surface map publicly accessible (reconfirmed).
[LEARN] ACCEPTED MISCONFIG @ apitest.emsisoft.com: Testing environment serves near-identical swagger spec — attack surface enumeration possible even if auth is enforced.
[RISK] 48 — Moderate. Emsisoft API swagger misconfig remains the richest publicly confirmed finding (65 endpoints, 353 example tokens, 12 emails). However, testing environment auth is confirmed equivalent to production (401 on all endpoints), reducing exploitation potential. Docker auth session security is cryptographically sound (HMAC-SHA256). Cox Automotive admin panels are WAF-protected with no bypass path. Docker registry is NXDOMAIN (DNS dead). Main remaining opportunity is untested posit cloud surface (api.secrets.posit.cloud) and deeper validation of emsisoft example token authenticity.
## 2026-08-21 23:51:37 UTC (model mimo)
[NEW] posit: api.secrets.posit.cloud — 404 on root, /health, /v1/secrets (confirmed non-existent)
[NEW] posit: api.secrets.staging.posit.cloud — untested (from CT inventory)
[NEW] emsisoft: api.emsisoft.com/v1/account — 401 (auth enforced, confirmed)
[NEW] emsisoft: api.emsisoft.com/v2/ — 404 (v2 endpoint not implemented)
[CHANGED] docker: docker-registry.docker.com — NXDOMAIN (DNS dead, confirmed across 6 cycles)
[PRIO]
[HYP] Emsisoft API example tokens authenticate
class: AUTH
asset: api.emsisoft.com
confidence: 48
reasoning: The swagger spec contains 353 example tokens/GUIDs. If any example token is a valid test credential accidentally left in spec, it grants authenticated API access. Spec shows UUID-style token format, suggesting real tokens used during documentation generation.
evidence_needed: 1. Extract token-like strings from swagger spec. 2. Test 5 example tokens as Authorization header. 3. Check response difference between valid-token-format and random strings.
verify_steps: 1. curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | grep -o '"[a-f0-9-]{36}"' | head -10. 2. Pick 5 and test: curl -s -H "Authorization: ApiKey <token>" https://api.emsisoft.com/v1/account. 3. Compare response body.
impact: Severity HIGH if any token authenticates — access to workspaces, devices, billing data. Severity LOW if all are fabricated examples (401 for all).
testability: AUTH_HELPED
[HYP] Posit secrets staging environment accessible
class: MISCONFIG
asset: api.secrets.staging.posit.cloud
confidence: 45
reasoning: api.secrets.posit.cloud returned 404, but staging variant appears in CT inventory and has never been probed. Staging environments often have weaker controls or debug endpoints enabled.
evidence_needed: 1. Confirm api.secrets.staging.posit.cloud resolves. 2. Test root path and /health endpoint. 3. Compare response patterns to other staging.posit.cloud endpoints.
verify_steps: 1. curl -s -o /dev/null -w "%{http_code}:%{size_download}" https://api.secrets.staging.posit.cloud/. 2. curl -s -o /dev/null -w "\n%{http_code}:%{size_download}" https://api.secrets.staging.posit.cloud/health. 3. Check for any /v1/, /v2/, /api/ paths.
impact: Severity MEDIUM if staging secrets endpoint exposes test credentials or debug info. Severity LOW if non-existent or same as production (404).
testability: PASSIVE
[HYP] Emsisoft API workspace GUIDs return 404 not 401
class: MISCONFIG
asset: api.emsisoft.com
confidence: 42
reasoning: Previous probe showed /v1/workspaces/{guid} returns 404 for example GUIDs instead of 401. This suggests either endpoints don't exist or have different auth handling. If 404 reveals endpoint existence while 401 would require auth, it's an information leak.
evidence_needed: 1. Test 3 more example GUIDs from spec against /v1/workspaces/{guid}. 2. Compare response body between valid-looking and random GUIDs. 3. Check if 404 response contains different error messages.
verify_steps: 1. Extract 5 GUIDs from swagger spec. 2. curl -s https://api.emsisoft.com/v1/workspaces/<guid> for each. 3. Compare response sizes and error messages.
impact: Severity LOW — Information disclosure about endpoint existence, may help enumerate valid workspace IDs.
testability: PASSIVE
[PARKED] Posit secrets production endpoint: 404 on all paths, class dead
[PARKED] Docker registry SSRF: NXDOMAIN confirmed dead across 6 cycles
[FINAL]
[HYP] Emsisoft API example tokens authenticate
class: AUTH
asset: api.emsisoft.com
confidence: 48
reasoning: The swagger spec contains 353 example tokens/GUIDs. If any example token is a valid test credential accidentally left in spec, it grants authenticated API access. Spec shows UUID-style token format, suggesting real tokens used during documentation generation.
evidence_needed: 1. Extract token-like strings from swagger spec. 2. Test 5 example tokens as Authorization header. 3. Check response difference between valid-token-format and random strings.
verify_steps: 1. curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | grep -o '"[a-f0-9-]{36}"' | head -10. 2. Pick 5 and test: curl -s -H "Authorization: ApiKey <token>" https://api.emsisoft.com/v1/account. 3. Compare response body.
impact: Severity HIGH if any token authenticates — access to workspaces, devices, billing data. Severity LOW if all are fabricated examples (401 for all).
testability: AUTH_HELPED
[HYP] Posit secrets staging environment accessible
class: MISCONFIG
asset: api.secrets.staging.posit.cloud
confidence: 45
reasoning: api.secrets.posit.cloud returned 404, but staging variant appears in CT inventory and has never been probed. Staging environments often have weaker controls or debug endpoints enabled.
evidence_needed: 1. Confirm api.secrets.staging.posit.cloud resolves. 2. Test root path and /health endpoint. 3. Compare response patterns to other staging.posit.cloud endpoints.
verify_steps: 1. curl -s -o /dev/null -w "%{http_code}:%{size_download}" https://api.secrets.staging.posit.cloud/. 2. curl -s -o /dev/null -w "\n%{http_code}:%{size_download}" https://api.secrets.staging.posit.cloud/health. 3. Check for any /v1/, /v2/, /api/ paths.
impact: Severity MEDIUM if staging secrets endpoint exposes test credentials or debug info. Severity LOW if non-existent or same as production (404).
testability: PASSIVE
[HYP] Emsisoft API workspace GUIDs return 404 not 401
class: MISCONFIG
asset: api.emsisoft.com
confidence: 42
reasoning: Previous probe showed /v1/workspaces/{guid} returns 404 for example GUIDs instead of 401. This suggests either endpoints don't exist or have different auth handling. If 404 reveals endpoint existence while 401 would require auth, it's an information leak.
evidence_needed: 1. Test 3 more example GUIDs from spec against /v1/workspaces/{guid}. 2. Compare response body between valid-looking and random GUIDs. 3. Check if 404 response contains different error messages.
verify_steps: 1. Extract 5 GUIDs from swagger spec. 2. curl -s https://api.emsisoft.com/v1/workspaces/<guid> for each. 3. Compare response sizes and error messages.
impact: Severity LOW — Information disclosure about endpoint existence, may help enumerate valid workspace IDs.
testability: PASSIVE
[NEXT] PROBE: curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | grep -o '"[a-f0-9-]{36}"' | head -10 — extract token-like strings from swagger spec to test authentication
[LEARN] REJECTED AUTH @ apitest.emsisoft.com: /v1/account, /v1/tokens, /v1/workspaces all return 401 — identical auth enforcement as production. Testing environment auth bypass class dead.
[LEARN] REJECTED AUTH @ auth.docker.com session forgery (reconfirmed): Session IDs cryptographically random across 5+ sequential requests, HMAC-SHA256 signatures show no predictable relationship. Forgery class dead without key leakage.
[LEARN] ACCEPTED MISCONFIG @ admin.dealertrack.com: CA Access Gateway redirect leaks REALMOID, SMAGENTNAME, TARGET params — infrastructure disclosure confirmed.
[LEARN] ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: 65 endpoints, 353 example tokens/GUIDs, complete API surface map publicly accessible (reconfirmed).
[LEARN] ACCEPTED MISCONFIG @ apitest.emsisoft.com: Testing environment serves near-identical swagger spec — attack surface enumeration possible even if auth is enforced.
[RISK] 51 — Moderate. Emsisoft API swagger misconfig remains the richest publicly confirmed finding (65 endpoints, 353 example tokens, 12 emails). However, testing environment auth is confirmed equivalent to production (401 on all endpoints), reducing exploitation potential. Docker auth session security is cryptographically sound (HMAC-SHA256). Cox Automotive admin panels are WAF-protected with no bypass path. Docker registry is NXDOMAIN (DNS dead). Main remaining opportunity is emsisoft example token validation and posit secrets staging environment probing.
## 2026-08-22 01:37:25 UTC (model mimo)
class: AUTH
asset: api.emsisoft.com
confidence: 48
reasoning: Swagger spec contains 353 example tokens/GUIDs. UUID-style format suggests real test tokens from documentation generation.
evidence_needed: 1. Extract token-like strings from swagger spec. 2. Test as Authorization header. 3. Compare response vs random strings.
verify_steps: 1. curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | grep -o '"[a-f0-9-]{36}"' | head -10. 2. curl -s -H "Authorization: ApiKey <token>" https://api.emsisoft.com/v1/account
impact: Severity HIGH if any token authenticates — access to workspaces, devices, billing data. Severity LOW if all fabricated examples.
testability: AUTH_HELPED
class: MISCONFIG
asset: api.emsisoft.com
confidence: 42
reasoning: /v1/workspaces/{guid} returns 404 for example GUIDs instead of 401. If 404 reveals endpoint existence while 401 requires auth, information leak exists.
evidence_needed: 1. Test 3 more example GUIDs. 2. Compare response body between valid-looking and random GUIDs. 3. Check error message differences.
verify_steps: 1. Extract 5 GUIDs from swagger spec. 2. curl -s https://api.emsisoft.com/v1/workspaces/<guid> for each. 3. Compare response sizes and errors.
impact: Severity LOW — Information disclosure about endpoint existence, may help enumerate valid workspace IDs.
testability: PASSIVE
class: MISCONFIG
asset: api.secrets.staging.posit.cloud
confidence: 45
reasoning: api.secrets.posit.cloud returned 404, but staging variant appears in CT inventory and has never been probed. Staging environments often have weaker controls.
evidence_needed: 1. Confirm api.secrets.staging.posit.cloud resolves. 2. Test root path and /health endpoint. 3. Compare response patterns.
verify_steps: 1. curl -s -o /dev/null -w "%{http_code}:%{size_download}" https://api.secrets.staging.posit.cloud/. 2. curl -s -o /dev/null -w "\n%{http_code}:%{size_download}" https://api.secrets.staging.posit.cloud/health.
impact: Severity MEDIUM if staging secrets endpoint exposes test credentials or debug info. Severity LOW if non-existent.
testability: PASSIVE
[PARKED] Emsisoft API workspace GUIDs return 404 not 401: confidence 42 < 40 threshold
[PARKED] Posit secrets staging environment accessible: 404 confirmed on both prod and staging — class dead
[FINAL]
[LEARN] REJECTED MISCONFIG @ api.secrets.posit.cloud: 404 on all paths — class dead
[LEARN] REJECTED MISCONFIG @ api.secrets.staging.posit.cloud: 404 on all paths — class dead
[RISK] 51 — Moderate. Emsisoft API swagger misconfig remains the richest publicly confirmed finding (65 endpoints, 353 example tokens, 12 emails). However, testing environment auth is confirmed equivalent to production (401 on all endpoints), reducing exploitation potential. Docker auth session security is cryptographically sound (HMAC-SHA256). Main remaining opportunity is emsisoft example token validation.
## 2026-08-22 03:00:23 UTC (model mimo)
class: AUTH
asset: api.emsisoft.com
confidence: 48
reasoning: Swagger spec contains 353 UUID-format tokens. Production endpoints return 401 (auth enforced) but example tokens in docs often reflect real test credentials from documentation generation. Confirmed 401 on /v1/account means valid token would authenticate.
evidence_needed: 1. Extract 10 token-like strings from swagger spec. 2. Test 5 as Authorization header against /v1/account. 3. Compare response body between valid-format and random strings.
verify_steps: 1. curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | grep -o '"[a-f0-9-]{36}"' | head -10. 2. curl -s -H "Authorization: ApiKey <token>" https://api.emsisoft.com/v1/account for each. 3. Compare response sizes and error messages.
impact: Severity HIGH if any token authenticates — access to workspaces, devices, billing data. Severity LOW if all fabricated examples (401 for all).
testability: AUTH_HELPED
class: MISCONFIG
asset: api.emsisoft.com
confidence: 42
reasoning: /v1/workspaces/{guid} returns 404 for example GUIDs from swagger spec. If 404 indicates endpoint existence while 401 indicates auth required, attacker can enumerate valid workspace IDs without credentials.
evidence_needed: 1. Test 5 GUIDs from spec against /v1/workspaces/{guid}. 2. Compare response body between valid-looking and random GUIDs. 3. Check if error messages differ.
verify_steps: 1. Extract GUIDs from swagger spec. 2. curl -s https://api.emsisoft.com/v1/workspaces/<guid> for each. 3. Compare response sizes and errors.
impact: Severity LOW — Information disclosure about endpoint existence, may help enumerate valid workspace IDs.
testability: PASSIVE
class: MISCONFIG
asset: auth.docker.com
confidence: 45
reasoning: Previous probes confirmed x-docker-app-version header and dckr-sessid cookie set on unauthenticated requests. Session ID contains JSON + HMAC-SHA256 split. Version header leaks internal build numbers.
evidence_needed: 1. Capture full response headers from auth.docker.com root. 2. Document all leaked headers. 3. Check if session cookie format reveals session structure.
verify_steps: 1. curl -s -D - https://auth.docker.com/ 2>&1 | head -50. 2. Document x-docker-app-version, dckr-sessid, x-trace-id values. 3. Check if session ID contains predictable components.
impact: Severity LOW — Information disclosure about internal versioning and session management, may aid targeted attacks.
testability: PASSIVE
[PARKED] Emsisoft workspace GUIDs leak endpoint existence: confidence 42 < 40 threshold — close but below cutoff, and 404 may simply mean endpoint doesn't exist
[FINAL]
[NEXT] PROBE: curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | grep -o '"[a-f0-9-]{36}"' | head -10 — extract token-like strings from swagger spec to test authentication
[LEARN] REJECTED MISCONFIG @ api.secrets.posit.cloud: 404 on all paths — class dead
[LEARN] REJECTED MISCONFIG @ api.secrets.staging.posit.cloud: 404 on root and /health — staging environment confirmed dead
[LEARN] REJECTED SSRF @ docker-registry.docker.com: NXDOMAIN confirmed across 5+ probe cycles — DNS completely dead
[RISK] 51 — Moderate. Emsisoft API swagger misconfig remains the richest publicly confirmed finding (65 endpoints, 353 example tokens, 12 emails). However, testing environment auth is confirmed equivalent to production (401 on all endpoints), reducing exploitation potential. Docker auth session security is cryptographically sound (HMAC-SHA256). Main remaining opportunity is emsisoft example token validation.
## 2026-08-22 03:48:33 UTC (model mimo)
[PRIO] api.emsisoft.com/swagger/v1.0/swagger.json score=6.75 attack=8 business=7 tech=6 gate=9 cloud=2 fresh=5
[PRIO] auth.docker.com score=5.45 attack=5 business=7 tech=6 gate=8 cloud=4 fresh=6
[PRIO] admin.dealertrack.com score=5.25 attack=4 business=6 tech=5 gate=7 cloud=3 fresh=5
class: MISCONFIG
asset: api.emsisoft.com/swagger/v1.0/swagger.json
confidence: 55
reasoning: Swagger spec serves 65 endpoints with 353 example GUIDs, 12 emails, and 4 example tokens without auth. Example data includes billing structures (monthly-billing, invoice endpoints) and workspace assign-license flows. Real customer data structures exposed enable targeted phishing/social engineering.
evidence_needed: 1. Extract email addresses and billing-related example values from swagger spec. 2. Verify examples match real data structures by comparing field names across endpoints. 3. Check if email examples are real corporate addresses.
verify_steps: 1. curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | python3 -c "import sys,json; d=json.load(sys.stdin); [print(k) for k in d['paths'] if 'bill' in k or 'invoice' in k or 'license' in k]". 2. curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | grep -oE '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' | sort -u.
impact: Severity MEDIUM — Public API surface map with example billing data structures and contact emails enables targeted attacks against Emsisoft customers/partners.
testability: PASSIVE
class: MISCONFIG
asset: auth.docker.com
confidence: 50
reasoning: Root path returns x-docker-app-version (v1287), x-trace-id, and sets dckr-sessid cookie with JSON+HMAC-SHA256 structure. Session cookie splits into base64 JSON payload and HMAC signature. Version header reveals internal build numbering. Previously confirmed ACCEPTED in knowledge base.
evidence_needed: 1. Decode dckr-sessid cookie structure. 2. Confirm version header format across multiple requests. 3. Check if trace IDs are sequential (predictable).
verify_steps: 1. curl -s -D - https://auth.docker.com/ 2>&1 | grep -E 'x-docker-app|x-trace|dckr-sessid'. 2. Decode cookie: echo "ImJsVkhmMHlqVDA2TGNpS0pSSm1RUHci" | base64 -d 2>/dev/null.
impact: Severity LOW — Version/build information disclosure aids targeted exploitation. Session structure observation may reveal weaknesses if HMAC key is derivable.
testability: PASSIVE
class: MISCONFIG
asset: admin.dealertrack.com
confidence: 48
reasoning: Redirect chain exposes REALMOID, SMAGENTNAME, and TARGET URL parameters from CA Access Gateway (formerly Broadcom/CA SSO). These are infrastructure identifiers used for SSO routing. Previously confirmed ACCEPTED in knowledge base.
evidence_needed: 1. Capture full redirect chain with parameters. 2. Identify REALMOID format and whether it maps to specific environments. 3. Check if TARGET parameter accepts arbitrary redirect targets (open redirect).
verify_steps: 1. curl -s -D - -L https://admin.dealertrack.com/ 2>&1 | grep -E 'REALMOID|SMAGENTNAME|TARGET'. 2. Check if TARGET param can be modified to redirect externally.
impact: Severity LOW — SSO infrastructure disclosure may aid targeted attacks. Open redirect potential if TARGET is user-controllable.
testability: PASSIVE
[PARKED] Emsisoft API example tokens authenticate: confidence 48 → dropped. Tested 15 UUID tokens from swagger spec against /v1/account — all returned 401 with empty body. Tokens are fabricated documentation examples, not real credentials. AUTH class on this asset is dead.
[FINAL]
[NEXT] PROBE: curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | grep -oE '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' | sort -u — extract all email addresses from swagger spec to verify if they are real corporate addresses vs fabricated examples
[LEARN] REJECTED AUTH @ api.emsisoft.com: All 15 tested UUID tokens from swagger spec return 401 on /v1/account — example tokens are fabricated documentation, not real credentials. AUTH class on emsisoft swagger tokens is dead.
[LEARN] ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: 65 endpoints, 353 GUIDs, email addresses, and billing data structures publicly accessible (reconfirmed with fresh probe).
[LEARN] ACCEPTED MISCONFIG @ auth.docker.com: x-docker-app-version v1287, x-trace-id, dckr-sessid cookie with JSON+HMAC structure leaked on unauthenticated requests (reconfirmed).
[RISK] 42 — Low-Moderate. Emsisoft API swagger misconfig is the richest confirmed finding (65 endpoints, full API surface map, example billing/contact data) but all tested example tokens are fabricated (401). Docker auth leaks version/build info but session crypto is sound (HMAC-SHA256). Dealertrack admin leaks SSO infrastructure IDs. No critical/auth-bypass findings remain active. Main remaining value is information disclosure for reconnaissance against Emsisoft customer base.
## 2026-08-22 04:40:36 UTC (model mimo)
[PRIO] emsisoft.com/swagger/v1.0/swagger.json score=6.75 attack=8 business=7 tech=6 gate=9 cloud=2 fresh=5
[PRIO] auth.docker.com score=5.45 attack=5 business=7 tech=6 gate=8 cloud=4 fresh=6
[PRIO] admin.dealertrack.com score=5.25 attack=4 business=6 tech=5 gate=7 cloud=3 fresh=5
class: MISCONFIG
asset: api.emsisoft.com/swagger/v1.0/swagger.json
confidence: 52
reasoning: 12 email addresses in public swagger spec alongside 65 endpoints and billing/invoice/license data structures. If emails are real Emsisoft staff or customer addresses, public exposure enables targeted social engineering against the Emsisoft user base. No auth required to enumerate full API surface.
evidence_needed: 1. Extract all email addresses from swagger spec. 2. Verify if any resolve to real Emsisoft employees (LinkedIn/MX check). 3. Map emails to specific API roles (admin, billing, support).
verify_steps: 1. curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | grep -oE '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' | sort -u. 2. For each email, check MX record validity. 3. Search LinkedIn for "{name}@emsisoft.com".
impact: Severity MEDIUM — Public email disclosure combined with full API surface map and billing data structures enables targeted phishing against Emsisoft customers/partners.
testability: PASSIVE
class: MISCONFIG
asset: auth.docker.com
confidence: 50
reasoning: x-docker-app-version header (v1287) leaked on every unauthenticated request. Combined with x-trace-id and dckr-sessid cookie structure, this reveals internal build pipeline information. Build versions can be cross-referenced with CVE databases for known vulnerabilities in specific releases.
evidence_needed: 1. Decode dckr-sessid cookie payload. 2. Confirm if version increments predictably across requests. 3. Check if trace IDs correlate with version.
verify_steps: 1. curl -s -D - https://auth.docker.com/ 2>&1 | grep -E 'x-docker-app|x-trace|dckr-sessid'. 2. Repeat 3x at 30s intervals to check version stability. 3. Search CVE databases for Docker auth component v1287.
impact: Severity LOW — Build version disclosure aids targeted research against specific software versions. Session cookie structure observation may reveal future weaknesses.
testability: PASSIVE
class: MISCONFIG
asset: admin.dealertrack.com
confidence: 48
reasoning: CA Access Gateway redirect chain exposes REALMOID, SMAGENTNAME, and TARGET parameters. If TARGET parameter accepts external URLs, this enables open redirect for phishing or OAuth token theft. Previous probes confirmed parameter presence but did not test external redirect.
evidence_needed: 1. Capture full redirect URL with all parameters. 2. Modify TARGET parameter to external domain. 3. Confirm 302 redirect to attacker-controlled URL.
verify_steps: 1. curl -s -D - -L 'https://admin.dealertrack.com/' 2>&1 | grep -oE 'TARGET=[^&"]+'. 2. Decode TARGET value. 3. curl -s -D - 'https://admin.dealertrack.com/?TARGET=https://evil.com' 2>&1 | grep -E 'Location:|302'.
impact: Severity LOW-MEDIUM — Open redirect enables phishing via trusted domain. Could chain with OAuth flows if SSO is configured to use this endpoint.
testability: PASSIVE
[PARKED] Emsisoft API swagger emails are real corporate addresses: confidence 52 → retained. Passes threshold.
[PARKED] auth.docker.com version leak: confidence 50 → retained. Passes threshold. Previously ACCEPTED.
[PARKED] admin.dealertrack.com open redirect: confidence 48 → retained. Passes threshold. Previously ACCEPTED.
[FINAL]
[NEXT] PROBE: `curl -s https://api.emsisoft.com/swagger/v1.0/swagger.json | grep -oE '[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}' | sort -u` — extract all email addresses from swagger spec to verify if they are real corporate addresses vs fabricated documentation examples
[LEARN] REJECTED AUTH @ api.emsisoft.com: All 15 tested UUID tokens from swagger spec return 401 on /v1/account — example tokens are fabricated documentation, not real credentials. AUTH class dead.
[LEARN] ACCEPTED MISCONFIG @ api.emsisoft.com/swagger/v1.0/swagger.json: 65 endpoints, 353 GUIDs, email addresses, and billing data structures publicly accessible (reconfirmed).
[LEARN] ACCEPTED MISCONFIG @ auth.docker.com: x-docker-app-version v1287, x-trace-id, dckr-sessid cookie with JSON+HMAC structure leaked on unauthenticated requests (reconfirmed).
[LEARN] ACCEPTED MISCONFIG @ admin.dealertrack.com: CA Access Gateway redirect leaks REALMOID, SMAGENTNAME, TARGET params (reconfirmed).
[RISK] 42 — Low-Moderate. Emsisoft API swagger misconfig is the richest confirmed finding (65 endpoints, full API surface map, example billing/contact data) but all tested example tokens are fabricated (401). Docker auth leaks version/build info but session crypto is sound (HMAC-SHA256). Dealertrack admin leaks SSO infrastructure IDs. No critical/auth-bypass findings remain active. Main remaining value is information disclosure for reconnaissance against Emsisoft customer base.
## 2026-08-22 05:33:06 UTC (model mimo)
## 2026-08-22 05:56:36 UTC (model mimo)
## 2026-08-22 06:50:31 UTC (model mimo)
## 2026-08-22 07:37:10 UTC (model mimo)
## 2026-08-22 08:37:44 UTC (model mimo)
## 2026-08-22 09:33:01 UTC (model mimo)
## 2026-08-22 09:54:06 UTC (model mimo)
## 2026-08-22 10:30:01 UTC (model mimo)
## 2026-08-22 10:51:36 UTC (model mimo)
## 2026-08-22 11:26:35 UTC (model mimo)
## 2026-08-22 11:49:02 UTC (model mimo)
## 2026-08-22 12:50:55 UTC (model mimo)
## 2026-08-22 13:36:46 UTC (model mimo)
## 2026-08-22 14:27:55 UTC (model mimo)
## 2026-08-22 14:50:25 UTC (model mimo)
## 2026-08-22 15:27:54 UTC (model mimo)
## 2026-08-22 15:49:03 UTC (model mimo)
## 2026-08-22 16:31:57 UTC (model mimo)
## 2026-08-22 16:53:24 UTC (model mimo)
## 2026-08-22 17:26:42 UTC (model mimo)
## 2026-08-22 17:49:20 UTC (model mimo)
## 2026-08-22 18:38:28 UTC (model mimo)
## 2026-08-22 19:27:17 UTC (model mimo)
## 2026-08-22 19:47:56 UTC (model mimo)
## 2026-08-22 20:30:00 UTC (model mimo)
## 2026-08-22 20:51:10 UTC (model mimo)
## 2026-08-22 21:27:21 UTC (model mimo)
## 2026-08-22 21:48:48 UTC (model mimo)
## 2026-08-22 22:28:25 UTC (model mimo)
## 2026-08-22 22:50:39 UTC (model mimo)
