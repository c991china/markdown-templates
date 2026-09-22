<!--
RFC / design doc. Write it BEFORE building the thing. The goal is to get
disagreement early, when changing course is cheap.

Keep it under two pages. If it's longer, you're designing in the doc instead of
in code. Ship the decision, then write the code.
-->

# RFC-014: Content-hash based upload skipping

| | |
|---|---|
| **Status** | Draft / In review / Accepted / Rejected |
| **Author** | @name |
| **Reviewers** | @alice, @bob |
| **Created** | 2026-04-02 |
| **Decision deadline** | 2026-04-09 |

## Summary

<!-- One paragraph. If a reader stops here, they should know the proposal. -->

Replace mtime-based change detection in the uploader with content hashing
(BLAKE3), so unchanged files are never re-uploaded. Cost: one full read of each
file per scan. Benefit: eliminate ~40% of redundant S3 PUTs.

## Motivation

<!-- The problem. Numbers if you have them. Why now? -->

`aws s3 sync` (and our current code) uses mtime + size. On our asset directory,
a `git checkout` or a CI cache restore rewrites mtimes without changing content,
so every file looks "changed". We re-upload ~40% of a 200GB directory on every
deploy. At $0.005/1000 PUTs plus egress, that's real money and real time.

## Goals

- Skip uploads when content is unchanged, regardless of mtime.
- Keep the scan under 5s for a 50k-file directory.

## Non-goals

<!-- Explicitly out of scope. This prevents scope creep in review. -->

- Deduplicating identical files across directories (separate RFC).
- Changing the storage backend or bucket layout.
- Incremental/streaming hashing across runs.

## Proposal

<!-- The design. Concrete. Include the data structures / interfaces. -->

Store a sidecar index `.uploader-index.json` mapping `path -> {hash, etag}`.

On each scan:
1. Walk the directory.
2. For each file, compute BLAKE3 in 1MB chunks.
3. Compare against the index. Upload only if the hash differs or the file is new.
4. After a successful upload, record the new hash and the returned ETag.

```json
{
  "assets/logo.png": { "hash": "b3:9f8e...", "etag": "\"a1b2\"" },
  "assets/hero.jpg": { "hash": "b3:3d4e...", "etag": "\"c5d6\"" }
}
```

BLAKE3 over SHA-256 because it's ~3x faster and we don't need cryptographic
collision resistance here. (If we did, SHA-256.)

## Alternatives considered

<!--
Show your work. Reviewers need to know you considered the obvious options.
-->

**mtime + size (status quo).** Cheap (no reads) but wrong after git operations
and cache restores. This is the bug we're fixing.

**S3 ETag comparison.** Would avoid the upload, but still requires an API call
per file, which is slower than hashing locally for small files and rate-limits
us on large directories.

**rsync-style rolling checksum.** Overkill. We're comparing whole files, not
syncing deltas.

## Trade-offs

- Hashing reads every file every scan. On a cold cache this is I/O heavy.
  Mitigation: BLAKE3 is fast; measure before optimizing.
- The sidecar index can drift from S3 if someone uploads out-of-band. Acceptable
  for our use; a `--force` flag re-uploads everything.

## Open questions

<!--
The honest ones. If you have no open questions, you're either done or hiding.
-->

1. Where does the index live? Local disk is lost on ephemeral runners. S3
   itself? Needs a decision.
2. What happens on a partial upload failure mid-scan?

## Rollout plan

- Behind a `--use-hash-index` flag. Default off for one release.
- Dogfood on the staging bucket for a week.
- Flip default if error rate is unchanged.

## Decision

<!-- Filled in after review. Keep the discussion, record the outcome. -->

*Pending review. Decision due 2026-04-09.*
