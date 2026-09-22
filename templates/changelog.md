<!--
Changelog. Loosely Keep-a-Changelog, but written for humans.
Rule: newest on top, one entry per user-visible change, no commit hashes as the
whole entry. "Fixed stuff" is not an entry.

Version dates in ISO (YYYY-MM-DD). Mark unreleased work as [Unreleased].
-->

# Changelog

All notable changes to this project are documented here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).
This project uses [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- `--max-retries` flag on the uploader (#482).

### Changed

- Log output is JSON by default. Set `LOG_FORMAT=text` for the old format.

### Fixed

- Uploader leaked a file descriptor on each failed attempt (#482).

## [1.3.2] - 2026-03-14

### Fixed

- Crash when the config file ended with a trailing comma (#471).
- `--dry-run` no longer creates the target directory as a side effect.

### Security

- Bumped `requests` to 2.32.0 for CVE-2024-XXXXX.

## [1.3.0] - 2026-02-28

### Added

- `--dry-run` flag. Prints what would be uploaded without uploading.
- Support for `AWS_PROFILE` from the environment.

### Changed

- Default `POLL_INTERVAL` is 2s (was 5s). Fewer surprises for new users.

### Deprecated

- `UPLOAD_BACKOFF` env var. Use `--retry-backoff`. Will be removed in 2.0.

## [1.2.0] - 2026-01-20

### Added

- Multipart upload for files over 100MB.
- `--prefix` to namespace uploads under an S3 key prefix.

### Removed

- Dropped Python 3.8 support. 3.8 reached end-of-life in Oct 2024.

## [1.0.0] - 2025-12-01

### Added

- First stable release. Directory watching, content-based skip, S3 upload.

<!--
Legend, for reference (do not paste this into your changelog):
  Added       - new features
  Changed     - changes in existing behavior
  Deprecated  - soon-to-be-removed features
  Removed     - features removed in this release
  Fixed       - bug fixes
  Security    - vulnerabilities fixed
-->
