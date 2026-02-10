# Deploy to ECS Service

[`.github/workflows/deploy-to-ecs.yaml`](../.github/workflows/deploy-to-ecs.yaml)

This workflow deploys a Docker image to an AWS ECS service. It downloads the task definition, updates the container image, and deploys the new task definition to the specified ECS cluster and service.

## Usage

```yaml
name: Deploy

on:
  push:
    tags:
      - 'v*'

jobs:
  build:
    name: Build Image
    runs-on: ubuntu-latest
    outputs:
      docker-image: ${{ steps.image.outputs.image }}
    steps:
      # ... build and push Docker image steps ...

  deploy:
    needs: build
    uses: significa/actions/.github/workflows/deploy-to-ecs.yaml@main
    with:
      ecs_cluster_name: my-cluster-production
      ecs_service_name: my-service-production
      ecs_task_definition_name: my-task-definition-production
      ecs_container_name: my-container
      docker_image: ${{ needs.build.outputs.docker-image }}
      aws_role_arn: ${{ vars.PRODUCTION_AWS_DEPLOY_ROLE_ARN }}
```

## Inputs

- `ecs_cluster_name` (required): ECS cluster name
- `ecs_service_name` (required): ECS service name
- `ecs_task_definition_name` (required): ECS task definition name
- `ecs_container_name` (required): Container name in the task definition
- `docker_image` (required): Docker image URI to deploy (e.g., `123456789.dkr.ecr.us-east-1.amazonaws.com/my-app:v1.2.3`)
- `aws_role_arn` (required): AWS IAM role ARN for assuming deployment permissions
- `aws_region` (optional, default: `eu-south-2`): AWS region for ECS deployment

## Permissions required

- `id-token: write` - For OIDC token generation
- `contents: read` - For reading repository contents

## Behavior

When called, this workflow:
1. Assumes the AWS IAM role using OIDC authentication
2. Downloads the task definition from ECS
3. Updates the container image in the task definition
4. Deploys the new task definition to the ECS service
5. Does not wait for service stability (returns immediately after deployment)

Note: The workflow uses `us-east-1` for OIDC token exchange as required by AWS, but deploys to the specified `aws_region`.
