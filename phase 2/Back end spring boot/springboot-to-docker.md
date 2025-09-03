# Convert Spring Boot Backend Project to Docker Image

## ✅ Step 1: Build Your Spring Boot Project (JAR)
First, package your project into a runnable **JAR file**.  
Inside your project root, run:

```sh
./mvnw clean package -DskipTests
```

(or, if using Gradle):

```sh
./gradlew build -x test
```

After this, you’ll find your **JAR file** in:

```
target/demo-0.0.1-SNAPSHOT.jar   # (Maven)
build/libs/demo-0.0.1-SNAPSHOT.jar  # (Gradle)
```

---

## ✅ Step 2: Create a Dockerfile
In the **root folder** of your Spring Boot project, create a file named `Dockerfile` (no extension).

Example `Dockerfile`:

```dockerfile
# Step 1: Use an OpenJDK base image
FROM openjdk:17-jdk-slim

# Step 2: Set working directory inside container
WORKDIR /app

# Step 3: Copy the JAR file into the container
COPY target/demo-0.0.1-SNAPSHOT.jar app.jar

# Step 4: Expose the application port (default 8080)
EXPOSE 8080

# Step 5: Run the jar file
ENTRYPOINT ["java","-jar","app.jar"]
```

👉 If your JAR is inside `build/libs`, update the `COPY` line accordingly.

---

## ✅ Step 3: Build Docker Image
Open terminal in your project root (where the `Dockerfile` is):

```sh
docker build -t springboot-backend:latest .
```

---

## ✅ Step 4: Run the Docker Container
Run the container from your image:

```sh
docker run -p 8080:8080 springboot-backend:latest
```

Now your app is running at:

```
http://localhost:8080
```

---

## ✅ Step 5 (Optional): Push to Docker Hub / GitHub Container Registry
- If you want to **share the image**:
  - [Docker Hub](https://hub.docker.com)  
  - [GitHub Container Registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry)

I can give you the exact steps for pushing to either one.

---

👉 Do you want me to write the **Docker ignore file (`.dockerignore`)** too? That will keep your image small.
