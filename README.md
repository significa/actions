# Shared GitHub actions workflows used by Significa

Reusable GitHub actions workflows.

## Available actions

### Deploy container to fly

[`.github/workflows/container-on-fly.yaml`](./.github/workflows/container-on-fly.yaml)

Usage:

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

Inputs:

- `staging_app_name`
- `production_app_name`
- `staging_branch`

Secrets:

- `FLY_API_TOKEN_STAGING`
- `FLY_API_TOKEN_PRODUCTION`

### Build and push docker image

[`.github/workflows/docker-image.yaml`](./.github/workflows/docker-image.yaml)

Usage:

```yaml
build_and_push:
  name: Build and push image
  permissions:
    contents: read
    id-token: write
    packages: write
  uses: significa/actions/.github/workflows/docker-image.yaml@main
  with:
    image_name: your-image-name
```

Inputs:

- `image_name`
- `platforms_architectures`

### Test and publish elixir library

[`.github/workflows/elixir-library.yaml`](./.github/workflows/elixir-library.yaml)

This reusable action tests an Elixir library with `mix test`, using all the supported Elixir and OTP
versions.

Usage:

```yaml
test-and-publish:
  name: Test and publish
  uses: significa/actions/.github/workflows/elixir-library.yaml@main
  secrets:
    HEX_API_KEY: ${{ secrets.HEX_API_KEY }}
```

Inputs:

- `publish-to-hex`: `true` to publish package to Hex package manager. Defaults to `true`.
- `replace-mix-version`: `true` to replace the version placeholder (`0.0.1-development`) in
  `mix.exs` before publishing. Defaults to `true`.

Secrets:

- `HEX_API_KEY`: Hex package manager api key. More details in
  [Publishing a package](https://hex.pm/docs/publish).
  Required if `publish-to-hex` is `true`.

### Test and publish NPM library

[`.github/workflows/npm-library.yaml`](./.github/workflows/npm-library.yaml)

This action builds tests and publishes an NPM library.
Both to private and public NPM registries (configurable).

Usage:

```yaml
test-and-publish:
  name: Test and publish
  uses: significa/actions/.github/workflows/npm-library.yaml@main
  permissions:
    contents: read
    packages: write
```

Inputs:

- `node_versions`
- `install_command`
- `lint_command`
- `build_command`
- `test_command`
- `npm_scope`
- `install_registry_url`
- `publish_to_github_registry`
- `publish_to_npm_registry`

Secrets:

- `NPM_TOKEN`

If publishing to NPM `publish_to_npm_registry`, make sure that:

- `public` is either not set OR set to false.
- `publishConfig.access` is set to `public`.
  ```json
  {
    "publishConfig": {
      "access": "public"
    }
  }
  ```
- `publishConfig` does not define a registry override for your scope.

### Test and publish Python package

[`.github/workflows/python-package.yaml`](./.github/workflows/python-package.yaml)

Usage:

```yaml
test-and-publish:
  name: Test and publish
  uses: significa/actions/.github/workflows/python-package.yaml@main
  secrets:
    TWINE_USERNAME: ${{ secrets.TWINE_USERNAME }}
    TWINE_PASSWORD: ${{ secrets.TWINE_PASSWORD }}
```

Secrets:

- `TWINE_USERNAME`
- `TWINE_PASSWORD`

### Build and deploy a Vercel app

[`.github/workflows/vercel-app.yaml`](./.github/workflows/vercel-app.yaml)

This reusable action builds, tests and deploys an application to Vercel.

Requires a `vercel.json` and `.nvmrc` in the source repository.

Usage:

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

Inputs:

- `install_command`
- `lint_command`
- `test_command`
- `npm_scope`
- `install_registry_url`
- `ref_name_to_vercel_environment`

Secrets:

- `VERCEL_TOKEN`
- `VERCEL_ORG_ID`
- `VERCEL_PROJECT_ID`
