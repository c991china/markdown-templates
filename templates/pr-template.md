<!-- Copy to .github/pull_request_template.md. Delete sections you don't need. -->

## What & why

<!-- One or two sentences. What changes, and what problem it solves.
     If this fixes an issue, link it: Fixes #123 -->

## How

<!-- Brief notes on the approach. Call out anything non-obvious or surprising.
     Reviewers should not have to reverse-engineer your intent from the diff. -->

## How to test

<!-- Exact steps a reviewer can run. Commands, not vibes. -->

```bash
# e.g.
make test
curl -s localhost:8000/health
```

## Risk / rollout

<!-- What could break? Is there a migration, a flag, a rollback path?
     If none, say "low risk, no migration". -->

## Checklist

- [ ] Tests cover the change (or explain why they don't)
- [ ] No secrets, tokens, or credentials committed
- [ ] Docs/README updated if behavior changed
- [ ] Commits are self-contained and messages explain the *why*

<!-- Delete this line if the PR is a draft: -->
<!-- Draft: not ready for review yet -->

<!--
Reviewer notes:
- Don't rubber-stamp. If you didn't read the diff, say so.
- Prefer one review pass with real comments over three "LGTM" rounds.
-->
