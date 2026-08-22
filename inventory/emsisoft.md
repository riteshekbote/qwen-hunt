
## 2026-08-19 17:57:35 UTC

## 2026-08-19 19:06:19 UTC

## 2026-08-19 19:51:46 UTC

## 2026-08-19 20:18:43 UTC

## 2026-08-19 20:54:05 UTC

## 2026-08-19 21:18:24 UTC

## 2026-08-19 21:50:24 UTC

## 2026-08-19 22:12:26 UTC

## 2026-08-19 22:50:30 UTC

## 2026-08-19 23:15:11 UTC

## 2026-08-19 23:45:57 UTC

## 2026-08-20 00:09:57 UTC

## 2026-08-20 01:53:18 UTC

## 2026-08-20 03:21:32 UTC

## 2026-08-20 04:07:16 UTC

## 2026-08-20 05:01:52 UTC

## 2026-08-20 05:54:56 UTC

## 2026-08-20 07:15:03 UTC

## 2026-08-20 08:03:36 UTC

## 2026-08-20 09:07:33 UTC

## 2026-08-20 09:59:13 UTC

## 2026-08-20 10:56:33 UTC

## 2026-08-20 11:52:58 UTC

## 2026-08-20 12:15:53 UTC

## 2026-08-20 13:19:13 UTC

## 2026-08-20 14:16:47 UTC

## 2026-08-20 15:03:41 UTC

## 2026-08-20 15:59:20 UTC

## 2026-08-20 17:00:52 UTC

## 2026-08-20 17:55:08 UTC

## 2026-08-20 19:07:39 UTC

## 2026-08-20 19:58:23 UTC

## 2026-08-20 20:53:46 UTC

## 2026-08-20 21:52:53 UTC

## 2026-08-20 22:15:49 UTC

## 2026-08-20 22:53:30 UTC

## 2026-08-20 23:15:51 UTC

## 2026-08-20 23:53:02 UTC

## 2026-08-21 02:04:09 UTC

## 2026-08-21 03:29:03 UTC

## 2026-08-21 04:30:11 UTC

## 2026-08-21 05:24:39 UTC

## 2026-08-21 06:19:32 UTC

## 2026-08-21 07:11:18 UTC

## 2026-08-21 08:08:13 UTC

## 2026-08-21 09:08:57 UTC

## 2026-08-21 10:01:36 UTC

## 2026-08-21 10:52:11 UTC

## 2026-08-21 11:43:12 UTC

## 2026-08-21 12:06:38 UTC

## 2026-08-21 13:21:12 UTC

## 2026-08-21 14:08:44 UTC

## 2026-08-21 15:07:48 UTC

## 2026-08-21 15:58:45 UTC

## 2026-08-21 17:02:12 UTC

## 2026-08-21 17:54:21 UTC

## 2026-08-21 18:21:30 UTC

## 2026-08-21 18:45:51 UTC

## 2026-08-21 19:32:16 UTC

## 2026-08-21 19:53:44 UTC

## 2026-08-21 20:33:29 UTC
- NEW api.emsisoft.com/v1/workspaces/{guid} returns **404** (not 401) — endpoint may not exist or requires different auth pattern
- NEW api.emsisoft.com/v1/workspaces/00000000-0000-0000-0000-000000000000 returns **404** — zeroed GUID matches error response, no info leak

## 2026-08-21 20:55:03 UTC

## 2026-08-21 21:31:05 UTC
- NEW api.emsisoft.com/v2/ -> HTTP 404 (new probe confirms v2 path doesn't exist)
- NEW apitest.emsisoft.com/swagger/v1.0/swagger.json -> 200 (testing endpoint returns identical spec to production)

## 2026-08-21 21:55:01 UTC
- CHANGED api.emsisoft.com/v2/ → HTTP 404 (confirms v2 path dead, spec is v1 only)
- CHANGED apitest.emsisoft.com/swagger/v1.0/swagger.json → 200 (testing endpoint returns identical spec to production)
- CHANGED apitest.emsisoft.com spec confirmed identical to production (65 paths, same GUIDs)

## 2026-08-21 22:31:51 UTC

## 2026-08-21 22:55:56 UTC

## 2026-08-21 23:32:00 UTC
- NEW apitest.emsisoft.com/v1/account → 401 (auth enforced, same as production)
- NEW apitest.emsisoft.com/v1/tokens → 401 (auth enforced)
- NEW apitest.emsisoft.com/v1/workflows → 401 (auth enforced)
- CHANGED apitest.emsisoft.com AUTH hypothesis: previously confidence 48, now evidence CONFIRMS same auth as production — class dead

## 2026-08-21 23:51:49 UTC
- NEW emsisoft: api.emsisoft.com/v1/account — 401 (auth enforced, confirmed)
- NEW emsisoft: api.emsisoft.com/v2/ — 404 (v2 endpoint not implemented)

## 2026-08-22 01:37:35 UTC

## 2026-08-22 03:00:32 UTC

## 2026-08-22 03:48:44 UTC

## 2026-08-22 04:40:46 UTC

## 2026-08-22 05:33:16 UTC

## 2026-08-22 05:56:45 UTC

## 2026-08-22 06:50:39 UTC

## 2026-08-22 07:37:20 UTC

## 2026-08-22 08:37:53 UTC

## 2026-08-22 09:33:11 UTC

## 2026-08-22 09:54:20 UTC

## 2026-08-22 10:30:08 UTC
