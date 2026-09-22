<!--
README template. Delete sections that don't apply to your project.
A README is for: what is this, why would I use it, how do I run it, what breaks.
Everything else is optional.
-->

# PROJECT_NAME

<!-- One line. What it is, in plain words. Not "a powerful platform". -->

A small CLI that watches a directory and uploads changed files to S3.

<!-- Optional badges. Only add the ones that are actually maintained. -->
[![CI](https://img.shields.io/badge/ci-passing-brightgreen)](https://github.com/c991china/PROJECT/actions)
[![License](https://img.shields.io/badge/license-MIT-blue)](LICENSE)

## Why this exists

<!-- The pain point. A real one. Skip if the project is self-explanatory. -->

`aws s3 sync` re-uploads files whose mtime changed even when the content is
identical, which was costing us real money on a large asset directory. This
tracks content hashes instead.

## Install

<!-- Exact commands. Pick the real package manager, don't list five. -->

```bash
pip install project-name
```

From source:

```bash
git clone https://github.com/c991china/PROJECT.git
cd PROJECT
pip install -e .
```

## Quick start

```bash
project-name watch ./assets --bucket my-bucket --prefix uploads/
```

## Usage

<!-- Show the common case with real input/output. Not a wall of flags. -->

```bash
$ project-name watch ./assets --bucket my-bucket
[watching] ./assets
[upload]   logo.png (14.2 KB, hash changed)
[skip]     hero.jpg (hash unchanged)
[upload]   docs/spec.pdf (1.1 MB, new)
```

Full flag list:

```bash
project-name --help
```

## Configuration

| env var | default | meaning |
|---------|---------|---------|
| `AWS_REGION` | `us-east-1` | S3 region |
| `POLL_INTERVAL` | `2` | seconds between scans |
| `LOG_LEVEL` | `info` | `debug`, `info`, `warn`, `error` |

## Gotchas

<!-- The section people actually need. Real errors, real causes. -->

- Requires credentials in the standard AWS chain (`~/.aws/credentials` or
  `AWS_*` env vars). There is no built-in login.
- Does not follow symlinks. A symlinked directory is skipped silently.
- Large files (>5GB) use multipart upload and will not resume after a crash.

## Development

```bash
python -m venv .venv && source .venv/bin/activate
pip install -e '.[dev]'
pytest
```

## License

MIT. See [LICENSE](LICENSE).
