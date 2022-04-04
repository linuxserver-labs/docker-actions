# README

[Reusable workflows](https://docs.github.com/en/actions/learn-github-actions/reusing-workflows) for use with our other repos

## Workflows

The following workflows are available for use:

### build-image.yml

This workflow will build a Docker image on release or on a PR being opened. It is designed to work with a single Dockerfile for all architectures.

`release_type` should be consistent across all workflows. Supported `release_type`s are:

* github - Standard github release, requires `release_url` input
* github_tag - Github release tag, requires `release_url` and `release_name` inputs
* github_commit - Github commit release, requires `release_url` input
* alpine - Alpine package release, requires `release_url` and `release_name` inputs
* script - Shell script release. Requires `get-version.sh` script in root of repo

`app_name` is a mandatory input and should be the desired name of the image and consisten across all workflows.

`target-arch` is mandatory and can be one of `any`, `amd64`, `arm`, `64`

Example workflow:

```yaml

```yaml
name: Build Image On Release

on:
  release:
    types: [published]
  pull_request:

jobs:
  call-workflow:
    uses: linuxserver-labs/docker-actions/.github/workflows/build-image.yml@v6
    with:
      repo_owner: ${{ github.repository_owner }}
      app_name: "your_spotify"
      release_type: "github"
      release_url: "https://api.github.com/repos/Yooooomi/your_spotify"
      target-arch: all
    secrets:
      scarf_token: ${{ secrets.SCARF_TOKEN }}
```

### build-split-image.yml

This workflow will build a Docker image on release or on a PR being opened. It is designed to work with a individual Dockerfiles for each architecture.

`release_type` should be consistent across all workflows. Supported `release_type`s are:

* github - Standard github release, requires `release_url` input
* github_tag - Github release tag, requires `release_url` and `release_name` inputs
* github_commit - Github commit release, requires `release_url` input
* alpine - Alpine package release, requires `release_url` and `release_name` inputs
* script - Shell script release. Requires `get-version.sh` script in root of repo

`app_name` is a mandatory input and should be the desired name of the image and consisten across all workflows.

`target-arch` is mandatory and should be an array of one or more of `amd64`, `arm64v8`, `arm32v7`

Example workflow:

```yaml
name: Build Image On Release

on:
  release:
    types: [ published ]
  pull_request:

jobs:
  call-workflow:
    uses: linuxserver-labs/docker-actions/workflows/build-split-image.yml@v6
    with:
      repo_owner: ${{ github.repository_owner }}
      app_name: "radarr"
      release_type: "script"
      target-arch: >-
        ["amd64", "arm64v8", "arm32v7"]
    secrets:
      scarf_token: ${{ secrets.SCARF_TOKEN }}
```

### check-and-release.yml

This workflow will check for updates to the upstream application and generate a release if there are any.

`release_type` should be consistent across all workflows. Supported `release_type`s are:

* github - Standard github release, requires `release_url` input
* github_tag - Github release tag, requires `release_url` and `release_name` inputs
* github_commit - Github commit release, requires `release_url` input
* alpine - Alpine package release, requires `release_url` and `release_name` inputs
* script - Shell script release. Requires `get-version.sh` script in root of repo

`app_name` is a mandatory input and should be the desired name of the image and consisten across all workflows.

`prerelease` is an optional input and will cause all releases to be marked as prerelease.

`branch` is an optional input and will cause all releases to be targeted against that branch. Defaults to `main` so needs to be specified if your default branch is `master`

Example workflow:

```yaml
name: Check for update and release

on:
  workflow_dispatch:
  schedule:
    - cron:  '11 * * * *'

jobs:
  call-workflow:
    uses: linuxserver-labs/docker-actions/.github/workflows/check-and-release.yml@v6
    with:
      repo_owner: ${{ github.repository_owner }}
      app_name: "radarr"
      release_type: "script"
      prerelease: true
      branch: nightly
    secrets:
      repo_release_token: ${{ secrets.repo_release_token }}
```

check-baseimage-update.yml

This workflow will check for updates to the base image used by the repo and generate a release if there are any.

`app_name` is a mandatory input and should be the desired name of the image and consisten across all workflows.

`baseimage` is a mandatory input and should be the name of the base image to check for updates. e.g. if you're using `linuxserver/baseimage-alpine-nginx` then the `baseimage` should be `alpine-nginx`.

`basebranch` is a mandatory input and should be the release tag of the base image you're using. e.g. if you're using the focal tag of the Ubuntu baseimage then the `basebranch` should be `focal`.

`branch` is an optional input and will cause all releases to be targeted against that branch. Defaults to `main` so needs to be specified if your default branch is `master`

Example workflow:

```yaml
name: Check for base image updates
on:
  workflow_dispatch:
  schedule:
    - cron:  '0 0 * * 0'

jobs:
  call-workflow:
    uses: linuxserver-labs/docker-actions/.github/workflows/check-baseimage-update.yml@v6
    with:
      repo_owner: ${{ github.repository_owner }}
      baseimage: "alpine"
      basebranch: "3.15"
      app_name: "whisparr"
      branch: nightly
    secrets:
      repo_release_token: ${{ secrets.repo_release_token }}
```
