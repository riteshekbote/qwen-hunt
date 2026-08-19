
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
