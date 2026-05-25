# Secret scan

[`.github/workflows/secret-scan.yaml`](../.github/workflows/secret-scan.yaml)

Runs [gitleaks](https://github.com/gitleaks/gitleaks) on every PR to detect committed secrets — API keys, tokens, `.env` contents, private keys. Scans full git history (`fetch-depth: 0`) so historical leaks are caught even if removed from HEAD.

Language-agnostic. Works on any repo (TypeScript, Elixir, Python, infra).

## Usage

```yaml
name: Quality
on:
  pull_request:
  push: { branches: [main] }

jobs:
  secrets:
    uses: significa/actions/.github/workflows/secret-scan.yaml@main
```

No inputs. Gitleaks is always installed from its latest GitHub release — secret-detection patterns evolve quickly, and the cost of a broken release is just one CI failure to investigate.

## What happens on detection

The job fails. The log lists the file, line, and a redacted match for each finding. The PR check goes red.

## False positives

If you have a known false positive (e.g. a test fixture with a fake-but-realistic-looking key), add a `.gitleaks.toml` at the repo root:

```toml
[allowlist]
description = "Test fixtures"
paths = ['fixtures/.*\.json$']
```

See the [gitleaks configuration docs](https://github.com/gitleaks/gitleaks#configuration) for full syntax. Per-rule allowlists, regex match exceptions, and commit-based ignores are all supported.

## Why a separate workflow

Secret scanning is language-agnostic. It belongs at the org level, not bundled with language-specific quality jobs. Significa projects opt-in independently — a TypeScript project calls both `typescript-quality.yaml` and `secret-scan.yaml`; an Elixir project will eventually call its language workflow plus `secret-scan.yaml`.
