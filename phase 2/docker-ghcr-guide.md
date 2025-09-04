
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

## 7) Pull the image and run 

Example workflow file (`.github/workflows/publish.yml`):

``` yaml
docker pull ghcr.io/mushamsuresh/address-ui:latest

docker run -d -p 3000:80 ghcr.io/mushamsuresh/address-ui:latest

```
Here latest is the tag which is given while pushing the file to github. we can see the same tag in GHCR
------------------------------------------------------------------------




