
## 1) Authenticate Docker to GHCR

``` powershell
docker login ghcr.io -u mushamsuresh
# When prompted for a password, paste the PAT you created.

```

------------------------------------------------------------------------
- PAT password is seen only once during the creation of tocken. Try to note it down securly at a place for reuse.
## 2) Tag your local image for GHCR

``` powershell
docker tag address-ui:latest ghcr.io/mushamsuresh/address-ui:latest
```

------------------------------------------------------------------------
- We have to tag the image for GHCR. Here my image name is  address-ui which is created in docker. 
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


