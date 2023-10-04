# AWS CLI Deploy to ECS

*Originally [based on](https://github.com/cloudgateinfra/gh-actions-ec2-deploy)*

Workflows are triggered on a push to the main/master branch or a manual trigger one-click:

- `deployimage.yml` = builds app with a docker or ecr base image then pushes app image to ECR for deploy in place to ECS ecr-repo/cluster/service.
- `loaddb.yml`      = loads a mysql dump onto an rds instance via bastion mysql client in the same VPC.

*Note: build is done during deploy workflow because secrets need to be passed as env vars at container runtime. This happens via the task definition that is managed with our terraform envs. This avoids secrets being built into image which is a security vulnerability.*

## Setup
Define the following secrets in the GitHub repo:

- `AWS_ACCESS_KEY_ID`: access key ID.
- `AWS_SECRET_ACCESS_KEY`: secret access key.
- `DB_PW`: DB password
- `DB_USERNAME`: DB username
- `DB_NAME`: DB name
- `JUMPHOST_PRIVATE_KEY_AWS`: bastion ec2 pem key for devops

Edit the following environment variables in the workflow file as needed:

- `AWS_REGION`: AWS region.
- `ECR_REPOSITORY`: ECR repository name.
- `ECS_SERVICE`: ECS service name.
- `ECS_CLUSTER`: ECS cluster name.
- `DOCKER_HUB_USERNAME`: Docker Hub username.
- `DOCKER_HUB_TOKEN`: Docker Hub access token.
- `IMAGE_NAME`: Docker image name.
- `IMAGE_TAG`: Docker image tag.

## Usage
Simply run the actions with one click to deploy the new app image to the ecs cluster/service in place [here](https://github.com/cloudgateinfra/gh-workflows/blob/master/deploywebappbasic.yml).

## Terraform ECS Envs

Infra managed with Terraform repo envs [here](https://github.com/cloudgateinfra/terraform-ecs).

## GitHub Actions Workflows

Workflow steps:

1. Checkout the repo code
2. Configure AWS creds
3. Log into ECR
4. Build and push the Docker image to the ECR repo
5. Deploy updated Docker image to ECS cluster/service in place

Terraform alignment:

- `ECR_REPOSITORY` in GitHub Actions <-> `name` in `aws_ecr_repository` in Terraform (`ecr_images`)
- `ECS_SERVICE` in GitHub Actions <-> `name` in `aws_ecs_service` in Terraform (`my_service`)
- `ECS_CLUSTER` in GitHub Actions <-> `name` in `aws_ecs_cluster` in Terraform (`my_cluster`)
- `IMAGE_NAME` and `IMAGE_TAG` in GitHub Actions <-> Used for building and tagging the Docker image.