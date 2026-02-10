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
      publish_release:
        description: 'Publish release immediately'
        type: boolean
        default: false
      prerelease:
        description: 'Mark as prerelease'
        type: boolean
        default: false

jobs:
  release:
    name: Create Release Draft
    permissions:
      contents: write
    uses: significa/actions/.github/workflows/release-drafter.yaml@main
    with:
      bump_type: ${{ inputs.bump_type }}
      publish_release: ${{ inputs.publish_release }}
      prerelease: ${{ inputs.prerelease }}
```

## Inputs

- `bump_type`: Version bump type - `patch` (default), `minor`, or `major`
- `publish_release`: Publish release immediately instead of creating a draft (default: `false`)
- `prerelease`: Mark the release as a prerelease (default: `false`)

## Permissions required

- `contents: write` - To create tags and releases
