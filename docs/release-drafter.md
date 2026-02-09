# Release Drafter

[`.github/workflows/release-drafter.yaml`](../.github/workflows/release-drafter.yaml)

This workflow creates a draft release with auto-generated release notes. It finds the latest semver tag, calculates the next version based on the bump type, creates and pushes a new tag, and creates a draft release using GitHub's release notes generation.

## Usage

```yaml
name: Release Drafter

on:
  workflow_dispatch:
    inputs:
      bump_type:
        description: 'Version bump type [MAJOR.MINOR.PATCH]'
        type: choice
        required: true
        default: 'patch'
        options:
          - patch
          - minor
          - major

jobs:
  release:
    name: Create Release Draft
    permissions:
      contents: write
    uses: significa/actions/.github/workflows/release-drafter.yaml@main
    with:
      bump_type: ${{ inputs.bump_type }}
```

## Inputs

- `bump_type`: Version bump type - `patch` (default), `minor`, or `major`

## Permissions required

- `contents: write` - To create tags and releases
