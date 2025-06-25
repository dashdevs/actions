# Reusable GitHub Actions Workflows

This repository contains reusable GitHub Actions workflows designed to simplify CI/CD pipelines.

## Available Workflows

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
