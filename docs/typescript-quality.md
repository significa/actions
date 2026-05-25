# TypeScript quality checks

[`.github/workflows/typescript-quality.yaml`](../.github/workflows/typescript-quality.yaml)

This reusable workflow runs Significa's deterministic-tooling baseline for TypeScript projects: **Biome + tsc strict + Knip**, in parallel jobs for fast PR feedback.

Node is resolved via [`mise`](https://mise.jdx.dev/) from whichever version file the project has — `mise.toml`, `.tool-versions`, `.node-version`, or `.nvmrc`. pnpm is installed via `pnpm/action-setup`.

## Usage

```yaml
name: Quality

on:
  pull_request:
  push:
    branches: [main]

jobs:
  quality:
    uses: significa/actions/.github/workflows/typescript-quality.yaml@main
```

## Inputs

All optional — the defaults match the standard Significa (pnpm) setup.

- `install_command` — defaults to `pnpm install --frozen-lockfile`
- `biome_command` — defaults to `pnpm exec biome ci .` (CI-optimized: no auto-fix, GitHub annotations)
- `typecheck_command` — defaults to `pnpm typecheck`
- `knip_command` — defaults to `pnpm knip --no-progress`

### Using npm or yarn

Override the four commands. The workflow itself is package-manager-agnostic:

```yaml
jobs:
  quality:
    uses: significa/actions/.github/workflows/typescript-quality.yaml@main
    with:
      install_command: npm ci --prefer-offline --no-audit --no-fund
      biome_command: npx biome ci .
      typecheck_command: npm run typecheck
      knip_command: npx knip --no-progress
```

## What each job checks

- **Biome** — formatting + linting (`biome ci`: no auto-fix, GitHub annotations)
- **Typecheck** — `tsc --noEmit` against the project's `tsconfig.json` strict flags
- **Dead code & unused dependencies** (Knip) — unused exports, files, and npm dependencies

Jobs run in parallel so failures surface independently — a Biome miss doesn't block discovery of typecheck or knip issues.

## Project setup expectations

The consuming project should have:

- A Node version pinned via any of: `mise.toml`, `.tool-versions`, `.node-version`, or `.nvmrc`
- `package.json` with `packageManager` field (used by `pnpm/action-setup`)
- `biome.json` extending `@significa/biome-config` (or equivalent)
- `tsconfig.json` extending `@significa/tsconfig` (or equivalent)
- `knip.json` with project-specific entries
- `pnpm typecheck` script in `package.json`
