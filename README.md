# jl_terraform_cloud
# My Multi-Environment ECS Deployment with Terraform Cloud and GitHub Actions

This project automates the deployment of your application to a multi-environment ECS infrastructure using Terraform Cloud and GitHub Actions. It leverages separate environments for development (dev) and production (prod), each with its own AWS region and Terraform workspace.

## Prerequisites:
- An AWS account with proper IAM permissions
- A Terraform Cloud organization and workspace
- An ECR repository for your Docker image
- An ECS cluster, task definition, and service (configured in Terraform)
- A GitHub repository for your infrastructure code (infra-repo)
- A GitHub repository for your application code (app-repo) with a Dockerfile

## Workflow Overview:
### This project uses two GitHub Actions workflows:
1. infra-deploy: This workflow runs in the infra-repo and is triggered on pushes to the main branch. It:
    - Authenticates to AWS using secrets stored in the repository.
    - Builds and pushes your Docker image to ECR.
    - Plans and applies Terraform changes for the specified environment (dev or prod), determined by a workflow environment variable.

2. app-deploy: This workflow runs in the app-repo and is triggered on pushes to the main and dev branches. It:
    - Authenticates to AWS using secrets stored in the repository.
    - Uses the AWS CLI to update the ECS service with the latest image from ECR, based on the environment (dev or prod).

### Setting Up the Workflows:
1. Configure Terraform Cloud:
    - Create a Terraform Cloud workspace for each environment (dev and prod).
    - Set up environment variables in Terraform Cloud for AWS credentials and other configuration.

2. Configure GitHub Environments:
    - Create two GitHub environments named dev and prod.
    - Set the workflow environment variable to dev for the dev environment and prod for the prod environment.

3. Configure GitHub Actions Secrets:
    - Add AWS credentials and other secrets to both repositories.

### Using the Workflows:
- Pushes to the main branch of the infra-repo will deploy infrastructure changes to the prod environment after manual approval.
- Pushes to the dev branch of the infra-repo will deploy infrastructure changes to the dev environment automatically.
- Pushes to the main and dev branches of the app-repo will deploy the application to the corresponding ECS service (dev or prod).