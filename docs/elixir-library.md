# Test and publish elixir library

[`.github/workflows/elixir-library.yaml`](../.github/workflows/elixir-library.yaml)

This reusable action tests an Elixir library with `mix test`, using all the supported Elixir and OTP versions.

## Usage

```yaml
test-and-publish:
  name: Test and publish
  uses: significa/actions/.github/workflows/elixir-library.yaml@main
  secrets:
    HEX_API_KEY: ${{ secrets.HEX_API_KEY }}
```

## Inputs

- `publish-to-hex`: `true` to publish package to Hex package manager. Defaults to `true`.
- `replace-mix-version`: `true` to replace the version placeholder (`0.0.1-development`) in
  `mix.exs` before publishing. Defaults to `true`.

## Secrets

- `HEX_API_KEY`: Hex package manager api key. More details in
  [Publishing a package](https://hex.pm/docs/publish).
  Required if `publish-to-hex` is `true`.
