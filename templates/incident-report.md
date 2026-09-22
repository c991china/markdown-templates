<!--
Incident report / postmortem. Blameless: focus on systems and process, not
people. Write it while it's fresh, publish it within a few days.

Severity scale (adjust to yours):
  SEV1 - full outage, customer-facing
  SEV2 - partial degradation
  SEV3 - internal only, no customer impact
-->

# Incident: <short title>

**Severity:** SEV2
**Status:** Resolved
**Date:** 2026-04-02
**Duration:** 14:03–14:41 UTC (38 minutes)
**Author:** @name
**Incident commander:** @name

## Summary

<!--
Two or three sentences a busy exec can read. What broke, who was affected,
how long, what fixed it. Write this LAST even though it goes first.
-->

Between 14:03 and 14:41 UTC, ~12% of upload requests failed with 503 errors
after a deploy raised the default worker count past what the DB connection pool
allowed. Rolling back the worker count restored service.

## Impact

<!-- Quantify. "Some users" is not impact. -->

- **Users affected:** ~12% of upload requests, roughly 4,000 requests.
- **Duration:** 38 minutes.
- **Data loss:** none. Failed requests were retried by clients.
- **SLA:** burned 11 minutes of the monthly error budget.

## Timeline

<!--
UTC timestamps. Include the boring parts (when you noticed, when you paged)
because they reveal detection gaps. This is the most useful section.
-->

| Time (UTC) | Event |
|------------|-------|
| 14:00 | Deploy `v1.4.0-rc3` rolled out (worker count 2 -> 16). |
| 14:03 | Error rate climbs. No alert fires yet (alert threshold is 15%). |
| 14:07 | Customer report in support channel. |
| 14:09 | On-call paged manually. |
| 14:12 | Incident declared. IC assigned. |
| 14:20 | DB connection pool saturation identified (max 100 conns, 16 workers x 8 threads). |
| 14:28 | Decision to roll back worker count, not the whole deploy. |
| 14:41 | Rollback complete. Error rate back to baseline. |
| 14:50 | Incident closed. Monitoring for 30 min. |

## Root cause

<!--
The mechanism, not "human error". If a human error contributed, ask what made
that error easy to make.
-->

The deploy raised `WORKERS` from 2 to 16. Each worker opens up to 8 DB
connections under load. The Postgres pool is capped at 100. 16 x 8 = 128 > 100,
so connections queued and eventually timed out, surfacing as 503s.

The config change was reviewed and approved, but nothing validated the product
`WORKERS x THREADS` against `PG_MAX_CONNECTIONS`. There was no check because the
two values live in different repos.

## Contributing factors

- Alert threshold (15% error rate) was too high to catch a 12% failure.
- The connection limit is defined in the infra repo; the worker count in the app
  repo. No single place sees both.
- Rollback took 13 minutes because the runbook was out of date.

## What went well

- Support caught it before the alert did, and flagged it fast.
- The team chose a targeted rollback over a full redeploy, cutting downtime.

## Action items

<!--
Each with an owner and a due date, and tracked somewhere real (issue tracker).
An action item with no owner is a wish.
-->

| # | Action | Owner | Due | Tracking |
|---|--------|-------|-----|----------|
| 1 | Lower error-rate alert threshold to 5% | @carol | 2026-04-03 | #501 |
| 2 | Add a startup check: `WORKERS*THREADS <= PG_MAX_CONNECTIONS` | @alice | 2026-04-10 | #502 |
| 3 | Move `PG_MAX_CONNECTIONS` into the app config repo | @bob | 2026-04-15 | #503 |
| 4 | Update the rollback runbook | @name | 2026-04-04 | #504 |

## Lessons

<!-- Optional. One or two lines. Not a place for platitudes. -->

We have two sources of truth for a number that must satisfy an invariant. The
real fix is one config, or a check that fails loudly at startup.
