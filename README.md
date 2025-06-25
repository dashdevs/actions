# Reusable GitHub Actions Workflows

This repository contains reusable GitHub Actions workflows designed to simplify CI/CD pipelines.

## Available Workflows

### .NET Application Build and Publish

A reusable workflow to build and publish a .NET application for multiple architectures and push the resulting Docker image to GHCR.

#### Usage Example

Call this workflow from another workflow file (e.g., `.github/workflows/build.yml`) like so:

```yaml
name: Build .NET Application

on:
  workflow_dispatch:

jobs:
  build:
    uses: dashdevs/actions/.github/workflows/build-dotnet-app.yml@main
    with:
      amd64: true
      arm64: true
      project_path: "./src/MyProject"
      dockerfile_path: "./Dockerfile"
```

#### Inputs

* `amd64` (boolean, required): Build for `linux/amd64`.
* `arm64` (boolean, required): Build for `linux/arm64`.
* `project_path` (string, required): Path to the .NET project to publish.
* `dockerfile_path` (string, optional): Path to the Dockerfile (default is `release.Dockerfile`).

#### Dockerfile Compatibility for Multi-Arch Builds

* Base images used in `FROM` should support all target platforms (e.g., `linux/amd64` and `linux/arm64`).
* Avoid hardcoding architecture-specific instructions.
* Use environment variables like `${TARGETARCH}` to differentiate build targets.
* Ensure platform-specific binaries are correctly copied (e.g., from `publish/linux-${TARGETARCH}`).
* Test locally using BuildKit: `docker buildx build --platform linux/amd64,linux/arm64 .`

#### Dockerfile Example

```Dockerfile
ARG ASPNET_RUNTIME_IMAGE="mcr.microsoft.com/dotnet/aspnet:8.0-alpine"
FROM ${ASPNET_RUNTIME_IMAGE} AS release

ARG TARGETARCH
WORKDIR /app
COPY publish/linux-${TARGETARCH}/ ./
ENTRYPOINT ["./MyApp"]
```

This Dockerfile uses a configurable base image and `TARGETARCH` to support multi-architecture builds.

### Django Static Files to S3 Upload

A reusable workflow for uploading Django static files to AWS S3 using OIDC authentication.

#### Usage Example

Create a workflow file (e.g., `.github/workflows/deploy.yml`) with the following content:

```yaml
name: Deploy Static Files

on:
  push:
    branches: [main]

jobs:
  upload-static-files:
    uses: dashdevs/actions/.github/workflows/upload-django-static-to-s3-oidc.yml@main
    with:
      python-version: '3.11'
      role-to-assume: "arn:aws-cn:iam::123456789100:role/my-github-actions-role"
      aws-region: "us-east-1"
      source_dir: "./static/"
      s3_bucket: "my-bucket-name/static"
