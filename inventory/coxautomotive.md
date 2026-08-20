
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
