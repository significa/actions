# Deploy container to fly

[`.github/workflows/container-on-fly.yaml`](../.github/workflows/container-on-fly.yaml)

## Usage

```yaml
build-and-deploy:
  name: Build & Deploy
  needs:
    - lint
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
