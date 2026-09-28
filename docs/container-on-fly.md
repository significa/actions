# Deploy container to fly

[`.github/workflows/container-on-fly.yaml`](../.github/workflows/container-on-fly.yaml)

## Usage example

```yaml
name: CI/CD

on:
  push:
    branches:
      - main
  pull_request:
  release:
    types:
      - published
  workflow_dispatch:

jobs:
  # ...

  build-and-deploy:
    name: Build & Deploy
    permissions:
      contents: read
      packages: read
      deployments: write
    needs:
      - lint # adapt to your workflow
    uses: significa/actions/.github/workflows/container-on-fly.yaml@main
    with:
      staging_app_name: your-app-staging
      production_app_name: your-app-staging-production
      staging_branch: main
    secrets:
      FLY_API_TOKEN_STAGING: ${{ secrets.FLY_API_TOKEN_STAGING }}
      FLY_API_TOKEN_PRODUCTION: ${{ secrets.FLY_API_TOKEN_PRODUCTION }}
```

## Inputs

- `staging_app_name`
- `production_app_name`
- `staging_branch`

## Secrets

- `FLY_API_TOKEN_STAGING`
- `FLY_API_TOKEN_PRODUCTION`
- `SENTRY_AUTH_TOKEN` (optional, see [Sentry source maps](#sentry-source-maps))

## Sentry source maps

Fly builds the image remotely, so it never sees your GitHub secrets, and a secret can't be
passed through `*_deploy_args` (GitHub doesn't allow `secrets` in `with:`). Without a token the
Sentry build plugin skips the source map upload, and errors in Sentry show minified stack traces.

Pass the token and the workflow forwards it to the build as a
[build secret](https://docs.fly.io/apps/build-secrets/). It stays masked in logs and
isn't stored in image layers.

```yaml
    secrets:
      FLY_API_TOKEN_STAGING: ${{ secrets.FLY_API_TOKEN_STAGING }}
      FLY_API_TOKEN_PRODUCTION: ${{ secrets.FLY_API_TOKEN_PRODUCTION }}
      SENTRY_AUTH_TOKEN: ${{ secrets.SENTRY_AUTH_TOKEN }}
```

Read it in the Dockerfile step that runs the build:

```dockerfile
RUN --mount=type=secret,id=SENTRY_AUTH_TOKEN \
  SENTRY_AUTH_TOKEN="$(cat /run/secrets/SENTRY_AUTH_TOKEN 2>/dev/null || true)" \
  pnpm run build
```

If the secret isn't passed, the file is missing, the token is empty, and the build runs as
before without uploading.

## Build args

**Pre-defined args**

APP_VERSION is a pre-defined arg that is always passed as a build argument.
It **does not** need to be passed.
It contains the application version following the semver standard (ex: `v1.2.3`),
when not in a release it will use semver extension like `v0.1.0-main-COMMIT_HASH`

**Custom build args**

Use `production_deploy_args` and `staging_deploy_args` to pass unsafe extra args to the `fly deploy`
command.
⚠️ Command injection/string globing allowed.

Example:

```yaml
with:
  production_deploy_args: --build-arg MY_CUSTOM_ARG=${{ vars.example }} --vm-memory 512
```
