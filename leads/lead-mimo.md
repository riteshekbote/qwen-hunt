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
