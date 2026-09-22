<!--
Meeting notes template. The value is the Decisions and Actions sections.
If a meeting has neither, the meeting probably didn't need to happen.
Keep it in the repo or the wiki so people who missed it can catch up.
-->

# Meeting: <topic>

**Date:** 2026-04-02
**Time:** 14:00–14:45 UTC
**Facilitator:** @name
**Note-taker:** @name

**Attendees:** @alice, @bob, @carol
**Absent:** @dave (sent notes ahead)

## Agenda

<!-- Numbered, with a rough time budget. If it's over 3 items, it's two meetings. -->

1. Decide on the retry strategy for the uploader (15m)
2. Q2 priorities (20m)
3. AOB (10m)

## Context

<!-- 2-3 lines. What prompted this meeting. Link the relevant issue/doc. -->

The uploader drops files under load (issue #482). Two candidate fixes on the
table, need a decision so we can unblock the release.

## Discussion

<!--
Notes, not a transcript. Attribute positions to people when it matters.
It is fine to record disagreement. "We agreed" when you didn't is worse.
-->

- @alice: prefers exponential backoff with jitter. Simple, no new infra.
- @bob: worried backoff alone won't help if the bottleneck is the S3 client
  connection pool. Suggests fixing the pool first.
- @carol: pulled the metrics. Errors are 503s from S3, not timeouts, so the
  pool is probably fine. Backoff should help.

## Decisions

<!-- Numbered, dated, with an owner. This is the part people come back for. -->

1. **Adopt exponential backoff with jitter.** @alice owns the implementation.
2. **Revisit the connection pool only if backoff doesn't cut errors below 0.1%.**
3. **Release 1.4.0 slips by 3 days** to include the fix.

## Action items

| # | Action | Owner | Due |
|---|--------|-------|-----|
| 1 | Implement backoff in `uploader.py` | @alice | 2026-04-04 |
| 2 | Add a dashboard panel for S3 503 rate | @carol | 2026-04-05 |
| 3 | Update release notes with the new default | @bob | 2026-04-06 |

## Parking lot

<!-- Things raised but deliberately not discussed. Do not silently drop them. -->

- Multi-region failover. Out of scope for this quarter.
- Whether to switch off the legacy S3 SDK. Needs its own meeting.

## Next meeting

2026-04-09 14:00 UTC. Agenda: backoff results, Q2 kickoff.
