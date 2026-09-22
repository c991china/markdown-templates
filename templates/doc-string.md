<!--
doc-string.md -- house style for code documentation comments.

The point: a doc comment answers "how do I use this and what will surprise me".
It is not a restatement of the signature. `# increments i by one` above `i += 1`
is worse than no comment.
-->

# Doc comment style

## The rule

Comment the **why** and the **contract**. Skip the **what** when the code says
it. If a comment and the code disagree, the comment is wrong and should be
deleted, not "kept in sync".

## Functions

State the contract: what it takes, what it returns, what it raises, and any
non-obvious behavior. One or two lines is usually enough.

```python
def retry(fn, attempts=3, backoff=0.5):
    """Call fn, retrying on transient errors.

    Retries attempts times total with exponential backoff (backoff, 2*backoff,
    ...) plus jitter. Raises the last exception if all attempts fail.

    Does NOT retry on ValueError or TypeError - those are bugs, not transients.
    """
```

Things this comment does that a signature can't:
- Says the retry count is *total*, not *additional*.
- Says jitter is added (so the caller doesn't assume fixed timing).
- Names what it won't retry. That's the surprising part.

## Modules / files

Say what the file is for and, if relevant, what it is NOT for. Keep it short.

```python
"""Uploader core. Content-hash based change detection and S3 upload.

The index file (.uploader-index.json) is the source of truth for "already
uploaded". Deleting it forces a full re-upload.
"""
```

## Inline comments

Use them for the non-obvious. A good inline comment explains a decision that a
future reader would otherwise "fix" and break.

```python
# Sleep BEFORE the retry, not after the first attempt, so a call that succeeds
# immediately doesn't pay the backoff.
time.sleep(backoff * (2 ** (attempt - 1)) + random.random())
```

Bad inline comments, for contrast:

```python
i += 1          # increment i            <- restates the code
# loop over files
for f in files: # loop over files        <- same
```

## TODO / FIXME / HACK

Use them, but make them actionable and owned. A TODO with no reason is a wish.

```python
# TODO(alice): replace with BLAKE3 once we drop Python 3.9 (3.10 has no wheel)
# FIXME(#482): this leaks an fd when the upload fails mid-stream
# HACK: S3 returns 503 for throttling AND for transient 5xx; we can't tell them
#       apart from the status code alone. Remove once we parse the error body.
```

Format: `TODO(owner):` or `FIXME(#issue):` then the reason. If you can't name an
owner or an issue, you don't understand the problem well enough to leave a TODO.

## What NOT to do

- Don't document types in the comment if the language has type hints. The hint
  is checked; the comment rots.
- Don't write `@param x the x parameter`. Say what x means or say nothing.
- Don't leave commented-out code. That's what git is for.
- Don't write a changelog in the doc comment (`# 2024-01-02: added foo`). That
  belongs in the commit message and the changelog file.
