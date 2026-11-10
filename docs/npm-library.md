# Test and publish NPM library

[`.github/workflows/npm-library.yaml`](../.github/workflows/npm-library.yaml)

This action builds tests and publishes an NPM library.
Both to private and public NPM registries (configurable).

## Usage

```yaml
test-and-publish:
  name: Test and publish
  uses: significa/actions/.github/workflows/npm-library.yaml@main
  permissions:
    contents: read
    packages: write
```

## Inputs

- `node_versions`
- `package_manager` (npm, yarn, pnpm — defaults to `npm`; drives the setup-node cache)
- `install_command`
- `lint_command`
- `build_command`
- `test_command`
- `npm_scope`
- `install_registry_url`
- `publish_to_github_registry`
- `publish_to_npm_registry`

## Secrets

- `NPM_TOKEN` (optional, see below)

## Publishing to NPM

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

### Trusted publishing

Without an `NPM_TOKEN` secret, the workflow publishes with
[npm trusted publishing](https://docs.npmjs.com/trusted-publishers) (OIDC),
and npm adds provenance automatically. To set it up:

1. On npmjs.com, open the package settings and add a GitHub Actions trusted
   publisher with your repository and the filename of **your** workflow (the
   one that calls this action, e.g. `ci-cd.yaml`), not `npm-library.yaml`.
2. Grant `id-token: write` in the calling job:
   ```yaml
   test-and-publish:
     name: Test and publish
     uses: significa/actions/.github/workflows/npm-library.yaml@main
     permissions:
       contents: read
       packages: write
       id-token: write
     with:
       publish_to_npm_registry: true
   ```
3. Stop passing `NPM_TOKEN`. If it is still passed, npm uses it as a fallback
   when OIDC is not available.

The `repository` field in `package.json` must point to the same GitHub
repository.
