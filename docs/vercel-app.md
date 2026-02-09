# Build and deploy a Vercel app

[`.github/workflows/vercel-app.yaml`](../.github/workflows/vercel-app.yaml)

This reusable action builds, tests and deploys an application to Vercel.

Requires a `vercel.json` and `.nvmrc` in the source repository.

## Usage

```yaml
ci-cd:
  name: CI/CD
  permissions:
    contents: read
    packages: read
    deployments: write
  uses: significa/actions/.github/workflows/vercel-app.yaml@main
  with:
    npm_scope: "@significa"
  secrets:
    VERCEL_TOKEN: ${{ secrets.VERCEL_TOKEN }}
    VERCEL_ORG_ID: ${{ secrets.VERCEL_ORG_ID }}
    VERCEL_PROJECT_ID: ${{ secrets.VERCEL_PROJECT_ID }}
```

## Inputs

- `install_command`
- `lint_command`
- `test_command`
- `npm_scope`
- `install_registry_url`
- `ref_name_to_vercel_environment`

## Secrets

- `VERCEL_TOKEN`
- `VERCEL_ORG_ID`
- `VERCEL_PROJECT_ID`
