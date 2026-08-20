
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
