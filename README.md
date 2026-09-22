# markdown-templates

Templates I actually use at work, not the ones that look good in a blog post.
Each one is short enough that filling it out is faster than writing freeform.

I started this because every team I joined had a different PR template, and the
bad ones had 30 checkboxes nobody read. These are the versions that survived
contact with real engineers.

## What's in here

| template | use it for |
|----------|-----------|
| `pr-template.md` | pull requests. Goes in `.github/pull_request_template.md` |
| `release-notes.md` | the notes you paste on a release |
| `readme-template.md` | new project READMEs |
| `meeting-notes.md` | anything with an agenda and decisions |
| `incident-report.md` | postmortems. Timeline + root cause + actions |
| `rfc.md` | proposing a change before you build it |
| `changelog.md` | Keep-a-Changelog-ish, but readable |
| `doc-string.md` | house style for code doc comments |

## How to use

Copy the file, delete the parts you don't need. The `<!-- -->` comments are
instructions to the author; they don't render.

For the PR template, GitHub picks it up automatically from a known path:

```bash
mkdir -p .github
cp templates/pr-template.md .github/pull_request_template.md
```

## Why these are short

A template earns its keep only if people fill it out. Every field that isn't
load-bearing gets deleted. If your incident report template has a "Customer
Sentiment" section and nobody has ever filled it in, it's noise.

Concretely: the PR template asks for *why* and *how to test*, and that's mostly
it. Not a checklist of 15 things that are better handled by CI or a linter.

## Notes

Markdown flavor is CommonMark + GFM (GitHub). The checkboxes `- [ ]` render on
GitHub, GitLab, and most forges. The `<!-- comments -->` are hidden in rendered
output.

No license restriction beyond the repo's; these are just text, adapt freely.
