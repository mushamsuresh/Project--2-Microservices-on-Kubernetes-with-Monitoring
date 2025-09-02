# Microservices Application with Docker

This project demonstrates a simple microservices setup using **React (Frontend)**, **Spring Boot/Node.js (Backend)**, and **Postgres (Database)**.  
We use **Docker** and **Docker Compose** to containerize and run the application locally.

---

## 1. Folder Structure

```
project/
 ├── frontend/         # React app
 │    └── Dockerfile
 ├── backend/          # Spring Boot OR Node/Python API
 │    └── Dockerfile
 ├── docker-compose.yml
 └── README.md
```

---

## 2. Dockerfile for React (Frontend)

Inside `frontend/Dockerfile`:

```dockerfile
# Step 1: Build React app
FROM node:18 AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Step 2: Serve with Nginx
FROM nginx:alpine
COPY --from=build /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

---

## 3. Dockerfile for Backend

### Example: Spring Boot (Java)

Inside `backend/Dockerfile`:

```dockerfile
FROM eclipse-temurin:17-jdk-alpine
WORKDIR /app
COPY target/demo-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java","-jar","/app/app.jar"]
```

### Example: Node.js

```dockerfile
FROM node:18
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 5000
CMD ["npm", "start"]
```

---

## 4. Docker Compose File

`docker-compose.yml`:

```yaml
version: "3.9"
services:
  frontend:
    build: ./frontend
    ports:
      - "3000:80"
    depends_on:
      - backend

  backend:
    build: ./backend
    ports:
      - "8080:8080"
    environment:
      - DB_HOST=db
      - DB_USER=appuser
      - DB_PASS=apppass
    depends_on:
      - db

  db:
    image: postgres:15
    restart: always
    environment:
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: apppass
      POSTGRES_DB: appdb
    ports:
      - "5432:5432"
```

---

## 5. Run the Application

From the project root folder, run:

```bash
docker-compose up --build
```

---

## 6. Access the Application

- React app → [http://localhost:3000](http://localhost:3000)  
- Backend API → [http://localhost:8080](http://localhost:8080)  
- Database → Postgres running at `localhost:5432`

---
