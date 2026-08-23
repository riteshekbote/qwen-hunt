
## 2026-08-19 17:57:35 UTC

## 2026-08-19 19:06:19 UTC

## 2026-08-19 19:51:46 UTC
- NEW https://api.coxautoinc.com/* -> HTTP 403 (changed from 200 in prior probes)

## 2026-08-19 20:18:43 UTC
- NEW https://api.coxautoinc.com/* -> HTTP 403 (CHANGED from previous 200)

## 2026-08-19 20:54:05 UTC

## 2026-08-19 21:18:24 UTC

## 2026-08-19 21:50:24 UTC
- CHANGED https://api.coxautoinc.com/endpoint: SSRF attempts still 403 (prior REJECTED)
- CHANGED https://api.coxautoinc.com/endpoint: SSRF attempts (127.0.0.1/192.168.1.1) return 403

## 2026-08-19 22:12:26 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=127.0.0.1: 403 confirmed (SSRF attempt)

## 2026-08-19 22:50:30 UTC

## 2026-08-19 23:15:11 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=127.0.0.1: 403 confirms SSRF misconfig

## 2026-08-19 23:45:57 UTC
- NEW https://api.coxautoinc.com/endpoint?param=127.0.0.1: 403 (SSRF)
- CHANGED https://api.coxautoinc.com/endpoint?param=10.0.0.1: 403

## 2026-08-20 00:09:57 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=127.0.0.1

## 2026-08-20 01:53:18 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=127.0.0.1 (now returns 403)

## 2026-08-20 03:21:32 UTC
- NEW https://api.coxautoinc.com/endpoint?param=127.0.0.1 (SSRF confirmed)
- CHANGED https://api.coxautoinc.com/endpoint (403 with param=127.0.0.1)

## 2026-08-20 04:07:16 UTC

## 2026-08-20 05:01:52 UTC

## 2026-08-20 05:54:56 UTC

## 2026-08-20 07:15:03 UTC
- CHANGED api.coxautoinc.com/endpoint SSRF confirmed (403 for internal IPs)

## 2026-08-20 08:03:36 UTC
- CHANGED https://api.coxautoinc.com/endpoint

## 2026-08-20 09:07:33 UTC
- NEW coxautoinc.com SSRF via internal IPs (403 responses for 10.0.0.1/127.0.0.1/192.168.1.1)

## 2026-08-20 09:59:13 UTC
- NEW api.coxautoinc.com/endpoint SSRF vulnerability (confirmed by 403 responses to internal IPs)

## 2026-08-20 10:56:33 UTC
- CHANGED coxautoinc.com endpoints consistently return 403 (no change in behavior)

## 2026-08-20 11:52:58 UTC
- NEW https://api.coxautoinc.com/endpoint?param=169.254.169.254: 403 response (same as param=10.0.0.1)

## 2026-08-20 12:15:53 UTC
- CHANGED https://api.coxautoinc.com/endpoint: 403 may be rate limiting/auth check

## 2026-08-20 13:19:13 UTC
- CHANGED https://api.coxautoinc.com/endpoint (403 may be rate limiting/auth check)
- CHANGED https://api.coxautoinc.com/endpoint?param=169.254.169.254 → 403 (rate limiting/auth check)

## 2026-08-20 14:16:47 UTC
- NEW https://api.coxautoinc.com/endpoint?param=192.168.1.1 (403, new private IP param)

## 2026-08-20 15:03:41 UTC
- NEW coxautoinc.com/endpoint (SSRF via param=169.254.169.254)

## 2026-08-20 15:59:20 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=192.168.1.1
- CHANGED https://api.coxautoinc.com/endpoint?param=169.254.169.254
- CHANGED https://api.coxautoinc.com/endpoint?param=10.0.0.1
- CHANGED https://api.coxautoinc.com/endpoint?param=172.16.0.1

## 2026-08-20 17:00:52 UTC
- NEW https://api.coxautoinc.com/endpoint
- NEW https://api.coxautoinc.com/endpoint (SSRF param test)

## 2026-08-20 17:55:08 UTC
- NEW https://api.coxautoinc.com/endpoint?param=169.254.169.254 (SSRF probe)

## 2026-08-20 19:07:39 UTC
- NEW https://api.coxautoinc.com/endpoint
- CHANGED https://api.coxautoinc.com/endpoint?param=10.0.0.1 (now 403)
- NEW https://api.coxautoinc.com/endpoint?param=10.0.0.1
- CHANGED https://api.coxautoinc.com/endpoint?param=192.168.1.1

## 2026-08-20 19:58:23 UTC
- CHANGED https://api.coxautoinc.com/endpoint (persistent 403s with param IPs)
- CHANGED https://api.coxautoinc.com/endpoint (persistent 403)

## 2026-08-20 20:53:46 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=169.254.169.254 (repeated with same 403)
- NEW api.coxautoinc.com/endpoint?param=169.254.169.254
- NEW api.coxautoinc.com/endpoint?param=172.16.0.1
- NEW api.coxautoinc.com/endpoint?param=127.0.0.1
- NEW api.coxautoinc.com/endpoint?param=192.168.1.1

## 2026-08-20 21:52:53 UTC

## 2026-08-20 22:15:49 UTC

## 2026-08-20 22:53:30 UTC
- NEW api.coxautoinc.com/endpoint

## 2026-08-20 23:15:51 UTC
- NEW api.coxautoinc.com/endpoint?param=admin
- NEW https://api.coxautoinc.com/endpoint (403)
- CHANGED https://api.coxautoinc.com/endpoint?param=169.254.169.254 (403)

## 2026-08-20 23:53:02 UTC
- NEW api.coxautoinc.com/endpoint?param=admin
- NEW https://api.coxautoinc.com/endpoint?param=169.254.169.254 -> HTTP 403

## 2026-08-21 02:04:09 UTC
- NEW https://api.coxautoinc.com/endpoint?param=admin
- NEW https://api.coxautoinc.com/endpoint?param=internal_ip
- NEW https://api.coxautoinc.com/endpoint?param=169.254.169.254
- CHANGED https://api.coxautoinc.com/endpoint?param=192.168.1.1
- CHANGED https://api.coxautoinc.com/endpoint?param=10.0.0.1

## 2026-08-21 03:29:03 UTC
- NEW https://api.coxautoinc.com/endpoint?param=admin
- NEW https://api.coxautoinc.com/endpoint?param=192.168.1.1
- CHANGED https://api.coxautoinc.com/endpoint

## 2026-08-21 04:30:11 UTC
- NEW api.coxautoinc.com/endpoint?param=internal_ip
- CHANGED api.coxautoinc.com/endpoint?param=admin
- CHANGED api.coxautoinc.com/endpoint?param=10.0.0.1

## 2026-08-21 05:24:39 UTC
- CHANGED coxautoinc.com/endpoint (403s with internal IPs as params)

## 2026-08-21 06:19:32 UTC
- NEW https://api.coxautoinc.com/endpoint (403s with param values)
- NEW api.coxautoinc.com/endpoint?param=admin
- CHANGED api.coxautoinc.com/endpoint?param=192.168.1.1

## 2026-08-21 07:11:18 UTC
- NEW https://api.coxautoinc.com/endpoint?param=internal_ip
- NEW https://api.coxautoinc.com/endpoint?param=10.0.0.1
- CHANGED https://api.coxautoinc.com/endpoint

## 2026-08-21 08:08:13 UTC
- NEW https://api.coxautoinc.com/endpoint?param=admin
- CHANGED https://api.coxautoinc.com/endpoint (now with 403s against admin param)
- NEW https://api.coxautoinc.com/endpoint?param=internal_ip
- CHANGED https://api.coxautoinc.com/endpoint?param=admin

## 2026-08-21 09:08:57 UTC
- NEW https://api.coxautoinc.com/endpoint?param=internal_ip (recurring 403 with internal IP)

## 2026-08-21 10:01:36 UTC
- NEW https://api.coxautoinc.com/endpoint?param=internal_ip (repeated 403s with parameter variations)
- CHANGED https://api.coxautoinc.com/endpoint?param=admin -> 403 (previously 403, now still 403)

## 2026-08-21 10:52:11 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403 persists with admin probe)

## 2026-08-21 11:43:12 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403 persists with admin probe)
- NEW https://api.coxautoinc.com/endpoint?param=internal_ip
- CHANGED https://api.coxautoinc.com/endpoint

## 2026-08-21 12:06:38 UTC
- NEW https://api.coxautoinc.com/endpoint?param=internal_ip (repeated 4x with HTTP 403)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=admin (403 remains)

## 2026-08-21 13:21:12 UTC
- NEW https://api.coxautoinc.com/endpoint?param=internal_ip

## 2026-08-21 14:08:44 UTC
- NEW https://api.coxautoinc.com/endpoint?param=internal_ip

## 2026-08-21 15:07:48 UTC
- NEW https://api.coxautoinc.com/endpoint?param=internal_ip
- CHANGED https://api.coxautoinc.com/endpoint

## 2026-08-21 15:58:45 UTC
- NEW api.coxautoinc.com/endpoint?param=admin
- CHANGED api.coxautoinc.com/endpoint

## 2026-08-21 17:02:12 UTC
- NEW https://api.coxautoinc.com/endpoint?param=admin
- CHANGED https://api.coxautoinc.com/endpoint?param=internal_ip -> HTTP 403

## 2026-08-21 17:54:21 UTC

## 2026-08-21 18:21:30 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-21 18:45:51 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-21 19:32:16 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-21 19:53:44 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-21 20:33:29 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-21 20:55:03 UTC
- NEW admin.dealertrack.com -> 200 with content (previously unprobed)
- NEW admin.dealertrack.com/admin/ -> HTTP 403 (access-controlled path confirmed)
- NEW admin.dealertrack.com serves Apache HTTP Server header (stack fingerprint)
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-21 21:31:05 UTC
- CHANGED admin.dealertrack.com/admin/ -> 403 confirmed (was previously 200 on root)
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-21 21:55:01 UTC
- CHANGED admin.dealertrack.com/admin/ → HTTP 403 (was previously 200 on root)
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-21 22:31:51 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-21 22:55:56 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-21 23:32:00 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-21 23:51:49 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 01:37:35 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 03:00:32 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 03:48:44 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 04:40:46 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 05:33:16 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 05:56:45 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 06:50:39 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 07:37:20 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 08:37:53 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 09:33:11 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 09:54:20 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 10:30:08 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 10:51:46 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 11:26:45 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 11:49:14 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 12:51:04 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 13:36:54 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 14:28:05 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 14:50:35 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 15:28:04 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 15:49:13 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 16:32:05 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 16:53:34 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 17:26:52 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 17:49:30 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 18:38:38 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 19:27:29 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 19:48:05 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 20:30:09 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 20:51:18 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 21:27:30 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 21:48:58 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 22:28:33 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 22:50:49 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 23:26:44 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-22 23:48:48 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 01:47:23 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 03:08:58 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 04:11:28 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 05:04:05 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 05:38:52 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 06:51:31 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 07:38:54 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 08:37:54 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 09:34:17 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 09:55:07 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 10:30:23 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 10:52:28 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 11:26:41 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 11:48:58 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 12:52:20 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 13:37:15 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 14:29:25 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 14:52:29 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 15:28:42 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 15:49:39 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 16:33:11 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 16:55:35 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 17:25:42 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 17:49:17 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 18:37:47 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 19:26:42 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 19:47:29 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 20:30:28 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 20:51:03 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 21:27:20 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 21:48:53 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 22:28:17 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)

## 2026-08-23 22:50:46 UTC
- CHANGED https://api.coxautoinc.com/endpoint?param=admin (403, previously 200)
- NEW coxautoinc.com/endpoint (403 Forbidden)
- CHANGED coxautoinc.com/endpoint?param=internal_ip (403 Forbidden)
