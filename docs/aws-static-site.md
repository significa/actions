# Build and deploy static site to AWS S3 + CloudFront

[`.github/workflows/aws-static-site.yaml`](../.github/workflows/aws-static-site.yaml)

This reusable action builds and deploys a static site to AWS S3 with CloudFront CDN. It uses OIDC for secure AWS authentication (no long-lived credentials needed).

Features:

- OIDC-based AWS authentication
- Optimized cache headers (immutable for assets, no-cache for index.html)
- CloudFront cache invalidation
- Optional Turbo cache support
- Telemetry disabled by default

## Usage

```yaml
name: Deploy

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  deploy:
    name: Deploy to AWS
    permissions:
      id-token: write
      contents: read
      packages: read
      deployments: write
    uses: significa/actions/.github/workflows/aws-static-site.yaml@main
    with:
      dist_path: dist
      build_command: pnpm build
    secrets:
      AWS_DEPLOY_ROLE_ARN: ${{ secrets.AWS_DEPLOY_ROLE_ARN }}
      AWS_S3_BUCKET_URI: ${{ secrets.AWS_S3_BUCKET_URI }}
      AWS_CLOUDFRONT_DISTRIBUTION_ID: ${{ secrets.AWS_CLOUDFRONT_DISTRIBUTION_ID }}
```

### With build environment variables

```yaml
deploy:
  uses: significa/actions/.github/workflows/aws-static-site.yaml@main
  with:
    dist_path: dist
    build_command: pnpm build
  secrets:
    AWS_DEPLOY_ROLE_ARN: ${{ secrets.AWS_DEPLOY_ROLE_ARN }}
    AWS_S3_BUCKET_URI: ${{ secrets.AWS_S3_BUCKET_URI }}
    AWS_CLOUDFRONT_DISTRIBUTION_ID: ${{ secrets.AWS_CLOUDFRONT_DISTRIBUTION_ID }}
    BUILD_ENV_VARS: |
      VITE_API_URL=${{ vars.API_URL }}
      VITE_APP_VERSION=${{ github.sha }}
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `build_command` | Command to build the static site | Yes | - |
| `dist_path` | Path to the built static files directory | Yes | - |
| `install_command` | Command to install dependencies | No | `pnpm install --frozen-lockfile` |
| `npm_scope` | NPM scope for GitHub packages registry | No | `''` |
| `aws_region` | AWS region for deployment | No | `eu-south-2` |
| `use_turbo_cache` | Enable Turbo cache | No | `false` |

## Secrets

| Secret | Description | Required |
|--------|-------------|----------|
| `AWS_DEPLOY_ROLE_ARN` | AWS IAM role ARN for authentication | Yes |
| `AWS_S3_BUCKET_URI` | S3 bucket URI (e.g., `s3://my-bucket`) | Yes |
| `AWS_CLOUDFRONT_DISTRIBUTION_ID` | CloudFront distribution ID for cache invalidation | Yes |
| `BUILD_ENV_VARS` | Environment variables for build step as `KEY=VALUE` pairs, one per line | No |

## Permissions required

```yaml
permissions:
  id-token: write    # Required for OIDC
  contents: read
  packages: read     # Required if using GitHub packages
  deployments: write
```

## Cache Strategy

The workflow uses an optimized caching strategy:

- **Static assets** (JS, CSS, images, etc.): `public, max-age=31536000, immutable` (1 year)
- **index.html**: `no-cache, no-store, must-revalidate` (always fetched fresh)

This ensures users always get the latest version while benefiting from long-term caching for hashed assets.
