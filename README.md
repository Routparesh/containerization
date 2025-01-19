# Containerization Project

This project sets up a containerized environment for running a web application with multiple services, using Docker and Docker Compose. The project is configured to run on **Vagrant** for local development and testing. This README outlines the steps for setting up, running, and testing the project.

## Project Overview

The project includes the following services:

1. **MySQL (Database Service)**: Version 8.0.33
2. **Memcache (Database Caching Service)**: Version 1.6
3. **RabbitMQ (Message Broker/Queue Service)**: Version 4.0
4. **JDK (Java Development Kit)**: JDK 21
5. **Maven**: Version 3.9.9
6. **Tomcat (Application Service)**: Version 10 with JDK 21
7. **Nginx (Web Server)**: Version 1.27

## Setup Steps

### Step 1: Requirements Gathering

Ensure you have the following software installed in your development environment:

- **Vagrant**: To set up and manage the virtual environment.
- **Docker**: For containerizing the services.
- **Docker Compose**: To orchestrate multiple containers.

### Step 2: Find the Right Base Image from Docker Hub

The following base images from Docker Hub will be used for the respective services:

- **MySQL**: `mysql:8.0.33`
- **Memcache**: `memcache:latest`
- **RabbitMQ**: `rabbitmq:latest`
- **Maven**: `maven:3.9.9-eclipse-temurin-21-jammy`
- **Tomcat**: `tomcat:10-jdk21`
- **Nginx**: `nginx:latest`

### Step 3: Write Dockerfile to Customize Images

For each service, a `Dockerfile` is created to customize the container images. This may involve setting environment variables, configuring volumes, or adding necessary dependencies.

Example for customizing **MySQL**:

```Dockerfile
FROM mysql:8.0.33
ENV MYSQL_ROOT_PASSWORD=rootpassword
ENV MYSQL_DATABASE=myapp_db
```

### Step 4: Write `docker-compose.yml` File

The `docker-compose.yml` file orchestrates the services, linking them together to form a complete system.
```
services:
  vprodb:
    build:
      context: ./Docker-files/db
    images: routparesh/vprodb
    container_name: vprodb
    ports:
      - '3306:3306'
    volumes:
      - vprodbdata: /var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=vprodbpass

  vprocache01:
    images: memcached
    container_name: vprocache01
    ports:
      - '11211:11211'

  vpromq01:
    images: rabbitmq
    ports:
      - '5672:5672'
    environment:
      - RABBITMQ_DEFAULT_USER=guest
      - RABBITMQ_DEFAULT_PASS=guest

  vproapp:
    build:
      context: ./Docker-files/app
    images: routparesh/vproapp
    container_name: vproapp
    ports:
      - '8080:8080'
    volumes:
      - vproappdata: /usr/local/tomcat/webapps

  vproweb:
    build:
      context: ./Docker-files/web
    images: routparesh/vproweb
    container_name: vproweb
    ports:
      - '80:80'

volumes:
  vprodbdata: {}
  vproappdata: {}

```

### Step 5: Test & Host Images on Docker Hub

1. **Test Locally**:
   - Bring up the services using Docker Compose:

   ```
   docker-compose up --build
   ```

   - Ensure all services are running and interacting correctly.

2. **Host Images on Docker Hub**:
   - After testing, push your custom images to Docker Hub:

   ```
   docker tag <image_id> <dockerhub_username>/<repository_name>:<tag>
   docker push <dockerhub_username>/<repository_name>:<tag>
   ```

## Vagrant Setup

To run this setup within a Vagrant environment, create a `Vagrantfile` with the appropriate configurations:

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
  config.vm.network "forwarded_port", guest: 80, host: 8080
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
  end
  config.vm.provision "docker" do |d|
    d.pull_images "mysql:8.0.33", "memcache:latest", "rabbitmq:latest", "nginx:latest"
  end
end
```

Then run:

```bash
vagrant up
```

This will set up the Vagrant virtual machine with Docker installed and ready to run your containers.

## Conclusion

This containerization project demonstrates how to set up a multi-container environment for an application using Docker, Docker Compose, and Vagrant. The services are designed to interact with one another, providing a scalable and modular environment for development and testing.
