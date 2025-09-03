# Uploading Docker Image to GitHub Container Registry

This guide explains how to push your Docker image to **GitHub Container
Registry (ghcr.io)** and make it available for future use or sharing.\
We will use your local image name **`address-ui`** as the example.

------------------------------------------------------------------------

## 0) Prerequisites

-   Docker installed and running.\
-   A GitHub account (or org) and permission to publish packages there.\
-   Your local image exists (`address-ui:latest`). You can confirm with:

``` powershell
docker images
```

(If you need to build from a Dockerfile:
`docker build -t address-ui:latest .`).

------------------------------------------------------------------------

## 1) Decide owner namespace

You will push under your GitHub username:

-   `ghcr.io/mushamsuresh/address-ui:tag`

------------------------------------------------------------------------

## 2) Create a Personal Access Token (PAT)

-   Go to GitHub: **Settings → Developer settings → Personal access
    tokens → Tokens (classic) → Generate new token**\
-   Minimum scopes: `write:packages` (and `read:packages` for pulling).\
-   Add `repo` if the package is private and linked to a private repo.\
-   Enable SSO if your org enforces it.\
-   Save the token securely.

------------------------------------------------------------------------

## 3) Authenticate Docker to GHCR

``` powershell
docker login ghcr.io -u <GITHUB_USERNAME>
# When prompted for a password, paste the PAT you created.

```

------------------------------------------------------------------------

## 4) Tag your local image for GHCR

``` powershell
docker tag address-ui:latest ghcr.io/mushamsuresh/address-ui:latest
```

------------------------------------------------------------------------

## 5) Push the image

``` powershell
docker push ghcr.io/mushamsuresh/address-ui:latest
```

------------------------------------------------------------------------

## 6) Verify & Change Visibility

-   Visit GitHub → Profile → **Packages** to see the uploaded image.\
-   In package **Settings**, you can change visibility to **Public /
    Private / Internal**.\
    ⚠️ Once public, it cannot be reverted to private.

------------------------------------------------------------------------

## 7) Automate with GitHub Actions (optional)

Example workflow file (`.github/workflows/publish.yml`):

``` yaml
name: build-and-publish
on: [push]

permissions:
  packages: write
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Build and push
        uses: docker/build-push-action@v4
        with:
          push: true
          tags: ghcr.io/mushamsuresh/address-ui:latest
```

------------------------------------------------------------------------

## 8) Common errors

-   **unauthorized/denied** → check PAT scopes (`write:packages`, `repo`
    if private, SSO enabled).\
-   **Image not visible** → check under Profile → Packages or linked
    repository's Packages.

------------------------------------------------------------------------

## 9) Security tips

-   Do not hardcode PAT in scripts. Use **GitHub Secrets** for Actions.\
-   Use `--password-stdin` instead of typing password interactively.\
-   Rotate tokens periodically.

------------------------------------------------------------------------

## Quick copy (PowerShell)

``` powershell
$env:GITHUB_PAT='ghp_XXXXXXXXXXXXXXXXXXXX'
echo $env:GITHUB_PAT | docker login ghcr.io -u mushamsuresh --password-stdin
docker tag address-ui:latest ghcr.io/mushamsuresh/address-ui:v1
docker push ghcr.io/mushamsuresh/address-ui:v1
```
