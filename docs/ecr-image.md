# Build and push ECR image

[`.github/workflows/ecr-image.yaml`](../.github/workflows/ecr-image.yaml)

This workflow builds a Docker image and pushes it to an AWS ECR private registry. It outputs the full image URI for use in downstream jobs (e.g., deploying to ECS).

## Usage

```yaml
name: Build and Deploy

on:
  push:
    branches:
      - main

jobs:
  build:
    uses: significa/actions/.github/workflows/ecr-image.yaml@main
    with:
      ecr_repository_name: my-app-staging
      aws_role_arn: ${{ vars.STAGING_AWS_DEPLOY_ROLE_ARN }}

  deploy:
    needs: build
    uses: significa/actions/.github/workflows/deploy-to-ecs.yaml@main
    with:
      ecs_cluster_name: my-cluster
      ecs_service_name: my-service
      ecs_task_definition_name: my-task-definition
      ecs_container_name: my-container
      docker_image: ${{ needs.build.outputs.docker-image }}
      aws_role_arn: ${{ vars.STAGING_AWS_DEPLOY_ROLE_ARN }}

  summary:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Print build info
        run: |
          echo "Image: ${{ needs.build.outputs.docker-image }}"
          echo "Version: ${{ needs.build.outputs.app-version }}"
```

## Inputs

- `ecr_repository_name` (required): ECR repository name
- `aws_role_arn` (required): AWS IAM role ARN for assuming deployment permissions
- `aws_region` (optional, default: `eu-south-2`): AWS region where the ECR repository is located
- `dockerfile` (optional, default: `./Dockerfile`): Path to the Dockerfile

## Outputs

- `docker-image`: Full Docker image URI with tag (e.g., `123456789.dkr.ecr.eu-south-2.amazonaws.com/my-app:main-0.0.1-main-abc1234`)
- `app-version`: Resolved application version (e.g., `1.2.3` for tags or `0.0.1-main-abc1234` for branches)

## Permissions required

- `id-token: write` - For OIDC token generation
- `contents: read` - For reading repository contents

## Behavior

When called, this workflow:
1. Assumes the AWS IAM role using OIDC authentication
2. Logs in to the ECR private registry in the specified region
3. Checks out the repository
4. Computes the app version:
   - For version tags (e.g., `v1.2.3`): strips the `v` prefix → `1.2.3`
   - For branches (e.g., `main`, `feature/foo`): uses `0.0.1-<branch>-<sha>`
5. Builds the Docker image using Buildx with GHA caching, passing `APP_VERSION` as a build arg
6. Tags the image as `<registry>/<repository>:<branch>-<version>`
7. Pushes the image to ECR
8. Outputs the full image URI and app version for downstream consumption

Note: The workflow uses `us-east-1` for OIDC token exchange as required by AWS, but authenticates to ECR in the specified `aws_region`.
