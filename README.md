## About

GitHub Action to use Docker Buildx Bake as a high-level build command

:warning: This repository is considered EXPERIMENTAL and under active development until further notice. It is subject to non-backward compatible changes or removal in any future version so you should pin to a specific tag/commit of this action in your workflow (i.e soramitsukhmer/docker-bake-action@v1).

## Recommended GitHub Action to use
- [docker/login-action](https://github.com/docker/login-action#readme)

## Usage

```yml
name: ci

on:
  schedule:
    # https://crontab.guru/#40_10_*_*_*
    - cron: "40 10 * * *"
  push:
    branches: [main]
    # Publish semver tags as releases.
    tags: ["v*.*.*"]
  pull_request:
    branches: [main]

env:
  # Use docker.io for Docker Hub if empty
  REGISTRY: ghcr.io
  # github.repository as <account>/<repo>
  IMAGE_NAME: ${{ github.repository }}

jobs:
  bake:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2

      # Login against a Docker registry
      # https://github.com/docker/login-action
      - name: Log into registry ${{ env.REGISTRY }}
        uses: docker/login-action@v1
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      # Build and push Docker image with Buildx Bake
      # https://github.com/soramitsukhmer/docker-bake-action
      - name: Build and push
        uses: soramitsukhmer/docker-bake-action@v3
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          targets: build
          push: true
```

## Customizing

| Name    | Type     |                                                                     |
| ------- | -------- | ------------------------------------------------------------------- |
| images  | List/CSV | List of Docker images to use as base name for tags                  |
| targets | List/CSV | List of bake targets                                                |
| pull    | Bool     | Always attempt to pull a newer version of the image (default false) |
| push    | Bool     | Push is a shorthand for --set=\*.output=type=registry               |

## Built-in actions

- [docker/setup-qemu-action](https://github.com/docker/setup-qemu-action)
- [docker/setup-buildx-action](https://github.com/docker/setup-buildx-action)
- [docker/metadata-action](https://github.com/docker/metadata-action#inputs)
- [dockerbakery/github-metadata-action](https://github.com/dockerbakery/github-metadata-action)
- [docker/bake-action](https://github.com/docker/bake-action#customizing)

## Keep up-to-date with GitHub Dependabot

Since Dependabot has native GitHub Actions support, to enable it on your GitHub repo all you need to do is add the `.github/dependabot.yml` file:

```yml
version: 2
updates:
  # Maintain dependencies for GitHub Actions
  - package-ecosystem: "github-actions"
    directory: "/"
    schedule:
      interval: "daily"
```
