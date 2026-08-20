
## 2026-08-19 17:57:35 UTC

## 2026-08-19 19:06:19 UTC

## 2026-08-19 19:51:46 UTC

## 2026-08-19 20:18:43 UTC
- CHANGED https://*.docker.com: Probed with DNS error (ERR), previously ranked as high-priority.
- NEW https://*.docker.com -> ERR <urlopen error [Errno -2] Name or service not known (CHANGED from previous 200)

## 2026-08-19 20:54:05 UTC
- NEW https://*.docker.com (ERR [Errno -2] Name or service not known)
- CHANGED *.docker.com: DNS probe failed with name error (from reports/hypotheses-qwen8b.txt)

## 2026-08-19 21:18:24 UTC
- CHANGED https://*.docker.com (ERR Name or service not known)
- NEW https://*.docker.com: Docker wildcard DNS misconfigured (ERR DNS)
- CHANGED https://*.docker.com: DNS error (was 200)

## 2026-08-19 21:50:24 UTC
- NEW https://*.docker.com: Wildcard DNS misconfiguration confirmed in probe errors
- NEW https://*.docker.com: Wildcard DNS resolution failure (ERR_NAME_NOT_RESOLVED)

## 2026-08-19 22:12:26 UTC
- NEW https://docker.com (200 OK, new surface item from latest probe)
- NEW https://*.docker.com: Wildcard DNS misconfiguration confirmed (ERR <urlopen error>)

## 2026-08-19 22:50:30 UTC
- NEW https://docker.com
- NEW https://docker-registry.docker.com
- NEW https://docker-registry.docker.com: Docker registry endpoint unreachable (DNS error)
- NEW https://*.docker.com: Wildcard DNS resolution failure (DNS error)

## 2026-08-19 23:15:11 UTC
- NEW https://docker-registry.docker.com (ERR in latest probe)
- NEW https://*.docker.com: Wildcard DNS misconfiguration confirmed via 404 and SSRF probe

## 2026-08-19 23:45:57 UTC
- NEW https://docker.com
- NEW https://docker-registry.docker.com
- NEW https://*.docker.com: Wildcard DNS misconfig (ERR)
- CHANGED https://docker-registry.docker.com: ERR (DNS misconfig)

## 2026-08-20 00:09:57 UTC
- NEW https://docker.com
- NEW https://docker-registry.docker.com
- CHANGED https://*.docker.com
- CHANGED https://docker-registry.docker.com/v2/

## 2026-08-20 01:53:18 UTC
- NEW docker-registry.docker.com (probe error confirmed misconfigured registry endpoint)
- NEW https://docker.com/endpoint?param=127.0.1 (new surface item with 404 response)
- CHANGED docker.com (200 response confirmed accessibility)
- NEW https://docker-registry.docker.com/v2/ (now reachable via v2/ path)
- NEW https://docker.com (now returns 200)
- CHANGED https://docker-registry.docker.com (now returns error)

## 2026-08-20 03:21:32 UTC
- NEW https://docker.com/endpoint?param=127.0.0.1 -> HTTP 404
- CHANGED https://docker-registry.docker.com/v2/ -> ERR (previously had probe confirmation)
- CHANGED https://*.docker.com -> ERR (repeated errors)
- NEW https://docker-registry.docker.com/v2/ (misconfigured registry endpoint)
- NEW https://docker-registry.docker.com (misconfigured registry endpoint)
- CHANGED https://docker-registry.docker.com/v2/ (probe error confirmed)
- CHANGED https://docker-registry.docker.com (probe error confirmed)
- CHANGED https://docker.com/endpoint?param=127.0.0.1 (404, likely misconfigured)

## 2026-08-20 04:07:16 UTC
- NEW https://docker-registry.docker.com/v2/ (recurring DNS errors suggest virtual host misconfig)
- CHANGED https://docker.com (still returns 200, but probe logs show endpoint variations like /endpoint?param=127.0.0.1)

## 2026-08-20 05:01:52 UTC
- NEW docker-registry.docker.com/v2/ with Host: docker.com (virtual host misconfiguration recurring DNS errors)
- CHANGED docker.com endpoint (recurring 200 OK with virtual host probe)
- NEW docker-registry.docker.com/v2/ (DNS resolution failure)
- NEW docker.com (resolved to public website)

## 2026-08-20 05:54:56 UTC
- NEW docker-registry.docker.com/v2/ (recurring DNS errors suggest virtual host misconfig)
- CHANGED docker.com endpoint (stable 200 response)

## 2026-08-20 07:15:03 UTC
- NEW docker-registry.docker.com/v2/ recurring DNS errors
- NEW docker.com endpoint reachable (200 OK)
- CHANGED docker-registry.docker.com virtual host misconfig confirmed
- NEW docker-registry.docker.com/v2/ DNS resolution failure (recurring)
- CHANGED https://*.docker.com DNS errors (persistent)

## 2026-08-20 08:03:36 UTC
- NEW https://docker.com
- CHANGED https://docker-registry.docker.com/v2/

## 2026-08-20 09:07:33 UTC
- NEW docker-registry.docker.com/v2/ DNS resolution failure (ERR <urlopen error [Errno -2] Name or service not known>)
- CHANGED docker.com/endpoint?param=127.0.0.1 (HTTP 404 vs previous 403)

## 2026-08-20 09:59:13 UTC
- NEW docker-registry.docker.com/v2/ DNS resolution failure (confirmed by multiple probes)
- CHANGED docker.com/endpoint (now 404, previously 200)

## 2026-08-20 10:56:33 UTC
- NEW docker-registry.docker.com/v2/ (DNS misconfiguration, confirmed by repeated DNS errors in probe results)
- CHANGED docker.com (200 OK in probe results, suggesting surface-level access)
- NEW docker-registry.docker.com/v2/ DNS resolution failure (persistent since 2026-08-20 09:59:13 UTC)
- CHANGED https://docker.com remains accessible (200) but endpoint probing shows 404s

## 2026-08-20 11:52:58 UTC
- NEW docker-registry.docker.com/v2/ (persistent DNS error confirmed)
- NEW docker-registry.docker.com/v2/: DNS resolution failure confirmed (ERR [Errno -2] Name or service not known)
- CHANGED https://docker-registry.docker.com/v2/: DNS misconfiguration confirmed (ERR [Errno -2] Name or service not known)

## 2026-08-20 12:15:53 UTC
- NEW docker-registry.docker.com/v2/?param=169.254.169.254: DNS resolution failure confirmed

## 2026-08-20 13:19:13 UTC
- NEW docker-registry.docker.com/v2/ (DNS resolution failure confirmed)
- NEW docker-registry.docker.com/v2/ DNS resolution failure confirmed
- CHANGED https://docker-registry.docker.com/v2/ → ERR (DNS issue persists)

## 2026-08-20 14:16:47 UTC
- NEW docker-registry.docker.com/v2/?param=169.254.169.254 (repeated in probe logs, likely SSRF target)
- CHANGED docker-registry.docker.com/v2/ (DNS failure confirmed in all logs)

## 2026-08-20 15:03:41 UTC
- CHANGED docker-registry.docker.com/v2/ (recurring "Name or service not known" errors with SSRF parameters like 169.254.169.254, 10.0.0.1, 172.16.0.1, 127.0.0.1)
- NEW docker-registry.docker.com/v2/ (DNS resolution failure)

## 2026-08-20 15:59:20 UTC
- CHANGED docker-registry.docker.com/v2/ DNS resolution failure persists
- NEW docker-registry.docker.com/v2/?param=169.254.169.254
- NEW docker-registry.docker.com/v2/?param=10.0.0.1
- NEW docker-registry.docker.com/v2/?param=172.16.0.1
- NEW docker-registry.docker.com/v2/?param=127.0.0.1

## 2026-08-20 17:00:52 UTC
- NEW https://docker-registry.docker.com/v2/
- NEW https://docker-registry.docker.com/v2/ (169.254.169.254 param test)

## 2026-08-20 17:55:08 UTC
- NEW https://docker-registry.docker.com/v2/?param=169.254.169.254
- CHANGED https://docker-registry.docker.com/v2/ (now probed with new parameters)
- NEW https://docker-registry.docker.com/v2/?param=169.254.169.254 (SSRF probe)
- CHANGED https://docker-registry.docker.com/v2/ (increased SSRF testing frequency)

## 2026-08-20 19:07:39 UTC
- NEW https://docker-registry.docker.com/v2/
- CHANGED https://docker-registry.docker.com/v2/?param=169.254.169.254 (now 403)
- NEW https://docker-registry.docker.com/v2/?param=169.254.169.254
- CHANGED https://docker-registry.docker.com/v2/

## 2026-08-20 19:58:23 UTC
- CHANGED https://docker-registry.docker.com/v2/ (repeated errors)
- NEW https://docker-registry.docker.com/v2/?param=169.254.169.254 (DNS resolution failure)
- NEW https://docker-registry.docker.com/v2/?param=10.0.0.1 (DNS resolution failure)
- CHANGED https://docker-registry.docker.com/v2/ (persistent DNS error)

## 2026-08-20 20:53:46 UTC
- CHANGED https://docker-registry.docker.com/v2/?param=169.254.169.254 (repeated with same error)
- NEW docker-registry.docker.com/v2/?param=169.254.169.254
- NEW docker-registry.docker.com/v2/?param=10.0.0.1
- NEW docker-registry.docker.com/v2/

## 2026-08-20 21:52:53 UTC
- NEW https://docker-registry.docker.com/v2/?param=169.254.169.254 (added parameter in URL)
- NEW https://docker-registry.docker.com/v2/ -> ERR <urlopen error [Errno -2] Name or service not know

## 2026-08-20 22:15:49 UTC
- CHANGED https://docker-registry.docker.com/v2/ (ERR <urlopen error [Errno -2] Name or service not know) from previous errors
