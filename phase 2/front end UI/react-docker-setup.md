# 🚀 React App Docker Setup Guide

## 0) Open a terminal in the project root

PowerShell (recommended) — run:

```powershell
cd "C:\Users\sures\OneDrive\Desktop\project 2\front_end_UI\address-ui"
```

---

## 1) Check you have a React project

You should have `package.json`, `src/` (you already have `src/AddressForm.js`), etc. To check:

```powershell
Get-ChildItem
Get-Content package.json
```

👉 If `package.json` is missing and you want to bootstrap a new React app (only if needed):

```powershell
npx create-react-app .    # WARNING: will add files in current folder
```

⚠️ If you already have your own `src` files, **don’t run create-react-app** — instead manually ensure `package.json` and dependencies exist.

---

## 2) Add a `.dockerignore` (helps speed builds & keep images small)

Create file `.dockerignore` (same folder as Dockerfile):

```powershell
New-Item -Path ".dockerignore" -ItemType "File"
```

Add the following content:

```
node_modules
build
.dockerignore
.git
.gitignore
npm-debug.log
Dockerfile
Dockerfile.* 
.vscode
.idea
*.md
```

Verify:

```powershell
Get-Content .dockerignore
```

✅ Done! Now Docker will ignore unnecessary files (like `node_modules` and `build`) when you run `docker build`.

---

## 3) Create your Dockerfile step by step

### 📝 Step 1: Go to your project folder

```powershell
cd "C:\Users\sures\OneDrive\Desktop\project 2\front_end_UI\address-ui"
```

### 📝 Step 2: Create an empty Dockerfile

```powershell
New-Item -Path "Dockerfile" -ItemType "File"
```

### 📝 Step 3: Add Dockerfile contents

Paste this into **Dockerfile**:

```dockerfile
# ---- build stage ----
FROM node:18 AS build
WORKDIR /app

# Copy package files first for better cache behavior
COPY package*.json ./

# Install dependencies (use npm ci when package-lock.json exists)
RUN npm ci --legacy-peer-deps || npm install

# Copy source
COPY . .

# Optional: allow injecting API base URL at build time
ARG REACT_APP_API_URL
ENV REACT_APP_API_URL=$REACT_APP_API_URL

# Build app
RUN npm run build


# ---- production stage (serve with nginx) ----
FROM nginx:alpine

# Remove default files
RUN rm -rf /usr/share/nginx/html/*

# Copy build artifacts from previous stage
COPY --from=build /app/build /usr/share/nginx/html

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

At this point, your folder should have:

```
C:\Users\sures\OneDrive\Desktop\project 2\front_end_UI\address-ui\
 ├── src\
 ├── public\
 ├── package.json
 ├── Dockerfile
 ├── .dockerignore
 └── ...
```

---

## 4) Build & Run React in Docker

### 📝 Step 1: Build the Docker image

```powershell
docker build -t address-ui:latest .
```

- `-t address-ui:latest` → names your image **address-ui** with the tag `latest`  
- `.` → tells Docker to use the Dockerfile in the current folder  

✅ This will take a little time the first time (downloads Node + Nginx).

---

### 📝 Step 2: Run the container

```powershell
docker run --rm -p 3000:80 --name address-ui address-ui:latest
```

- `--rm` → automatically removes the container when stopped  
- `-p 3000:80` → maps your computer’s port `3000` → container’s port `80`  
- `--name address-ui` → gives the container a friendly name  

---

### 📝 Step 3: Open in browser

Go to:

👉 [http://localhost:3000](http://localhost:3000)

You should see your React app running from Docker 🎉

---

### 📝 Step 4: Stop the container

In another PowerShell window, run:

```powershell
docker stop address-ui
```
