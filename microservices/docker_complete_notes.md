# Complete Docker Notes (Theory + Practical + Interview Questions)

## 1. What is Docker?

Docker is a containerization platform that allows applications to run in
isolated environments called containers. Containers package the
application along with its dependencies.

**Key Idea:**\
Build once → Run anywhere

------------------------------------------------------------------------

## 2. Why Docker is Used

-   Eliminates "works on my machine" issues
-   Lightweight compared to VMs
-   Fast startup
-   Easy scaling in microservices
-   Consistent environments across dev, test, prod

------------------------------------------------------------------------

## 3. Containers vs Virtual Machines

  Feature        Containers       Virtual Machines
  -------------- ---------------- ------------------
  OS             Shared host OS   Separate OS
  Size           MBs              GBs
  Startup Time   Seconds          Minutes
  Performance    Faster           Slower

------------------------------------------------------------------------

## 4. Docker Architecture

Components: - Docker Client - Docker Daemon - Docker Image - Docker
Container - Docker Registry (Docker Hub)

Flow: 1. Build Image 2. Push/Pull Image 3. Run Container

------------------------------------------------------------------------

## 5. Important Docker Concepts

### Image

Blueprint of application.

### Container

Running instance of image.

### Dockerfile

Instructions to build image.

### Volume

Persistent storage.

### Network

Communication between containers.

------------------------------------------------------------------------

## 6. Important Docker Commands

### Check version

docker --version

### Pull image

docker pull nginx

### Run container

docker run -d -p 8080:80 nginx

### List containers

docker ps

### Stop container

docker stop `<containerId>`{=html}

### Remove container

docker rm `<containerId>`{=html}

------------------------------------------------------------------------

## 7. Dockerfile Example (Spring Boot Microservice)

    FROM openjdk:17-jdk-slim
    COPY target/app.jar app.jar
    ENTRYPOINT ["java","-jar","/app.jar"]

------------------------------------------------------------------------

## 8. Build Docker Image

docker build -t order-service .

------------------------------------------------------------------------

## 9. Run Container

docker run -p 8080:8080 order-service

------------------------------------------------------------------------

## 10. Creating One Microservice from Scratch (Step-by-Step)

### Step 1: Create Spring Boot App

Create simple REST controller.

Example:

    @RestController
    public class HelloController {
       @GetMapping("/hello")
       public String hello() {
          return "Hello Docker";
       }
    }

### Step 2: Build Jar

mvn clean package

### Step 3: Create Dockerfile

Use example shown earlier.

### Step 4: Build Image

docker build -t hello-service .

### Step 5: Run Container

docker run -p 8080:8080 hello-service

Open: http://localhost:8080/hello

------------------------------------------------------------------------

## 11. Docker Volumes

Used to persist data.

Example: docker run -v myvolume:/data mysql

------------------------------------------------------------------------

## 12. Docker Networking

Types: - Bridge - Host - Overlay

Example: docker network create mynetwork

------------------------------------------------------------------------

## 13. Docker Compose

Used to run multiple services.

Example docker-compose.yml:

    version: '3'
    services:
      app:
        build: .
        ports:
          - "8080:8080"
      redis:
        image: redis

Run: docker-compose up

------------------------------------------------------------------------

## 14. Best Practices

-   Use slim base images
-   Keep images small
-   Use multi-stage builds
-   Do not store secrets in images
-   Use .dockerignore

------------------------------------------------------------------------

## 15. Most Asked Interview Questions

1.  Difference between image and container?
2.  How Docker is different from VM?
3.  What is Dockerfile?
4.  What is volume?
5.  What is Docker Compose?
6.  How containers communicate?
7.  How to reduce Docker image size?
8.  What is multi-stage build?
9.  What happens when container crashes?
10. Difference between CMD and ENTRYPOINT?

------------------------------------------------------------------------

## 16. Tricky Interview Questions

1.  Why container startup is fast?
2.  Where containers are stored in Linux?
3.  How Docker uses namespaces and cgroups?
4.  Why container size is small?
5.  How networking works internally?
6.  How to debug container?
7.  What is layered filesystem?
8.  How Docker caching works?
9.  How to handle logs in containers?
10. Why not run multiple processes in one container?

------------------------------------------------------------------------

## 17. One-Line Interview Summary

Docker is a containerization platform that packages applications and
dependencies into lightweight containers for consistent and scalable
deployment.
