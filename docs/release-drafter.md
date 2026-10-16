# Release Drafter

[`.github/workflows/release-drafter.yaml`](../.github/workflows/release-drafter.yaml)

This workflow creates a draft release with auto-generated release notes. It finds the latest version tag, calculates the next version (semver bump or a single number increment), creates and pushes a new tag, and creates a draft release using GitHub's release notes generation.

## Usage

```yaml
name: 🆕 Release Drafter

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
      is_prerelease:
        description: 'Mark as is_prerelease'
        type: boolean
        default: false

jobs:
  release:
    name: Create Release Draft
    permissions:
      contents: write
    uses: significa/actions/.github/workflows/release-drafter.yaml@main
    with:
      versioning: semver
      bump_type: ${{ inputs.bump_type }}
      publish_release: ${{ inputs.publish_release }}
      is_prerelease: ${{ inputs.is_prerelease }}
```

### Single number versioning

For projects nobody consumes as a dependency (e.g. internal websites), breaking vs. non-breaking does not matter. Use `versioning: single` to tag releases as `v1`, `v2`, `v3`, ... and drop the `bump_type` input:

```yaml
name: 🆕 Release Drafter

on:
  workflow_dispatch:
    inputs:
      publish_release:
        description: 'Publish release immediately'
        type: boolean
        default: false

jobs:
  release:
    name: Create Release Draft
    permissions:
      contents: write
    uses: significa/actions/.github/workflows/release-drafter.yaml@main
    with:
      versioning: single
      publish_release: ${{ inputs.publish_release }}
```

If the repository already has semver tags, the next single version continues from the highest major (e.g. `v2.4.1` → `v3`).

## Inputs

- `versioning`: Versioning scheme - `semver` (default, `v1.2.3`) or `single` (`v1`, `v2`, `v3`)
- `bump_type`: Version bump type for `semver` - `patch` (default), `minor`, or `major`. Ignored when `versioning` is `single`
- `publish_release`: Publish release immediately instead of creating a draft (default: `false`)
- `is_prerelease`: Mark the release as a is_prerelease (default: `false`)

## Permissions required

- `contents: write` - To create tags and releases
