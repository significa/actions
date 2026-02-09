# Build and push docker image

[`.github/workflows/docker-image.yaml`](../.github/workflows/docker-image.yaml)

## Usage

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

## Inputs

- `image_name`
- `platforms_architectures`
