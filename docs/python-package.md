# Test and publish Python package

[`.github/workflows/python-package.yaml`](../.github/workflows/python-package.yaml)

## Usage

```yaml
test-and-publish:
  name: Test and publish
  uses: significa/actions/.github/workflows/python-package.yaml@main
  secrets:
    TWINE_USERNAME: ${{ secrets.TWINE_USERNAME }}
    TWINE_PASSWORD: ${{ secrets.TWINE_PASSWORD }}
```

## Secrets

- `TWINE_USERNAME`
- `TWINE_PASSWORD`
