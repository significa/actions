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
