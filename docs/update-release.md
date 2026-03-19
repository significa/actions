# Update Release

[`.github/workflows/update-release.yaml`](../.github/workflows/update-release.yaml)

This workflow updates a release with deployment information when a version tag is pushed. It appends deployment details including the timestamp and a link to the action run to the release notes.

## Usage

```yaml
name: Deploy and Update Release

# ... triggers ...

jobs:
  deploy:
    name: Deploy
    runs-on: ubuntu-latest
    steps:
      # ... deployment steps ...

  update-release:
    name: Update Release
    needs: deploy
    uses: significa/actions/.github/workflows/update-release.yaml@main
    with:
      application_name: 'my-app to production'
```

## Inputs

- `application_name` (required): Application name and/or environment where the release was deployed (e.g., `my-app to production`, `frontend to staging`)

## Permissions required

- `contents: write` - To update releases

## Behavior

When triggered on a version tag (e.g., `v1.2.3`), this workflow:
1. Retrieves the release associated with the tag
2. Appends deployment information to the release notes including:
   - Timestamp (ISO 8601 format)
   - Application name/environment
   - Link to the workflow run
3. Updates the release with the new notes

If the release is not found, the workflow logs a message and completes gracefully without failing.
