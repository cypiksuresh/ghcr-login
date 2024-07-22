# CI/CD Pipeline

This repository contains a CI/CD pipeline configuration for building and deploying a Docker image using GitHub Actions.

## Workflow Configuration

The GitHub Actions workflow is defined in the `.github/workflows` directory. Below is the complete workflow configuration.

### .github/workflows/cicd-pipeline.yml

```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - master

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      - name: Login to GitHub Container Registry
        uses: docker/login-action@v1
        with:
          registry: ghcr.io
          username: ${{ github.repository_owner }}
          password: ${{ secrets.GITHUBTOKEN }}

      # Uncomment for debugging purposes
      # - name: Debug: List files
      #   run: ls -la

      - name: Build and push Docker image
        uses: docker/build-push-action@v2
        with:
          context: .
          file: ./Dockerfile
          push: true
          tags: ghcr.io/${{ github.repository_owner }}/my-app:${{ github.sha }}

      - name: Deploy to server (optional)
        if: always()
        env:
          IMAGE: ghcr.io/${{ github.repository_owner }}/my-app:${{ github.sha }}
        run: echo "Skipping deploy step for now"
        # Uncomment and configure the lines below for actual deployment
        # run: |
        #   ssh user@yourserver.com "
        #     docker pull $IMAGE &&
        #     docker stop my-app-container || true &&
        #     docker rm my-app-container || true &&
        #     docker run -d --name my-app-container -p 80:80 $IMAGE
        #   "

```

## Explanation of Workflow Configuration
## Workflow Trigger
```yaml
on:
  push:
    branches:
      - master

```
## Jobs
The workflow contains a single job named build, which runs on the latest Ubuntu runner:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest

```
## Steps
## Checkout Repository

This step checks out the repository code:
```yaml
- name: Checkout repository
  uses: actions/checkout@v2
```
## Set up Docker Buildx

This step sets up Docker Buildx for building multi-platform images:

```yaml
- name: Set up Docker Buildx
  uses: docker/setup-buildx-action@v1
```
## Build and Push Docker Image
This step builds the Docker image and pushes it to the GitHub Container Registry:

```yaml
- name: Build and push Docker image
  uses: docker/build-push-action@v2
  with:
    context: .
    file: ./Dockerfile
    push: true
    tags: ghcr.io/${{ github.repository_owner }}/my-app:${{ github.sha }}

```
