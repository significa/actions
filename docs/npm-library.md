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
- `install_command`
- `lint_command`
- `build_command`
- `test_command`
- `npm_scope`
- `install_registry_url`
- `publish_to_github_registry`
- `publish_to_npm_registry`

## Secrets

- `NPM_TOKEN`

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
