# Release v1.4.0

<!--
Keep the header as the version. Tag the commit that matches.
Audience: whoever upgrades. Write for someone who is NOT on your team.
Delete empty sections. Do not list every commit; list what changed for users.
-->

**Released:** 2026-04-02
**Compare:** [`v1.3.2...v1.4.0`](https://github.com/c991china/PROJECT/compare/v1.3.2...v1.4.0)

## Highlights

<!-- 1-3 bullets. If you can't summarize it, the release is doing too much. -->

- Added streaming uploads, so files no longer buffer fully in memory.
- Config now reloads on SIGHUP instead of requiring a restart.

## Added

- `--max-retries` flag on the uploader (default 3).
- `/metrics` endpoint exposing request counts (Prometheus format).

## Changed

- Default log format is now JSON. Set `LOG_FORMAT=text` for the old format.
- Minimum Python is now 3.10 (was 3.8). 3.8 hit end-of-life.

## Fixed

- Uploader no longer leaks a file descriptor per failed attempt (#482).
- Fixed a crash when the config file had a trailing comma.

## Deprecated

- `UPLOAD_BACKOFF` env var. Use `--retry-backoff` instead. Removed in v2.0.

## Upgrade notes

<!-- Anything a user must DO to upgrade safely. This is the section people read. -->

1. The JSON log default changes log parsing. If you grep for `level=`, set
   `LOG_FORMAT=text` to keep the old format.
2. Python 3.8 is no longer supported. Upgrade before pulling this version.
3. No database migration required.

## Breaking changes

<!-- If empty, write "None." and mean it. -->

None in this release. The next major (v2.0) will remove `UPLOAD_BACKOFF`.

## Thanks

<!-- Credit external contributors by handle. It matters. -->

Thanks to @someone for reporting the fd leak and @other for the repro script.
