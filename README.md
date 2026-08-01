# Containerized Node.js Application with MongoDB and Mongo Express

## Project Overview

This project demonstrates the deployment of a containerized Node.js application using Docker, MongoDB, and Mongo Express.

The goal of this project was to gain hands-on experience designing, deploying, and troubleshooting a multi-container application environment. The application was packaged into a Docker container while MongoDB and Mongo Express were deployed as supporting services.

This project demonstrates practical DevOps skills including:

- Containerization
- Docker image management
- Docker networking
- Application-to-database communication
- Database administration through Mongo Express
- Container troubleshooting
- Docker Compose configuration
- Data persistence concepts

---

# Business Context

Organizations are increasingly adopting containerized architectures to improve deployment consistency, scalability, and operational efficiency. Traditional application deployments often require teams to manually configure servers, install dependencies, and maintain environment-specific settings. This can lead to configuration drift, inconsistent deployments, and longer troubleshooting cycles.

This project simulates a real-world business scenario where an engineering team needs to package and deploy a web application with supporting database services in a consistent, repeatable environment.

By containerizing the application and supporting services, teams can:

- Standardize application environments across development, testing, and production.
- Package application dependencies into portable containers.
- Separate application workloads from database infrastructure.
- Reduce deployment inconsistencies between environments.
- Improve troubleshooting through predictable runtime environments.
- Simplify future migration to cloud platforms and orchestration systems.

From an operational perspective, this project represents a common workflow used by modern software and infrastructure teams. Developers build applications, cloud engineers package workloads, and DevOps engineers create repeatable deployment processes that improve reliability and maintainability.

The project also demonstrates an important production consideration: containers are designed to be temporary and replaceable, while business data must remain persistent. Testing container lifecycle behavior highlighted the importance of implementing persistent storage solutions before deploying database workloads into production environments.

This hands-on implementation reflects the responsibilities of a junior cloud or DevOps engineer by demonstrating the ability to deploy services, troubleshoot failures, validate system behavior, and document technical solutions.

---

# Architecture Overview

```
                 User
                  |
                  |
        Node.js Application
          Docker Container
                  |
                  |
            MongoDB Database
          Docker Container
                  |
                  |
          Mongo Express UI
        Database Administration
```

## Components

| Component | Purpose |
|---|---|
| Node.js Application | Web application running inside Docker |
| MongoDB | Database service storing application data |
| Mongo Express | Web-based MongoDB administration interface |
| Docker Network | Enables communication between containers |

---

# Technologies Used

- Docker
- Docker Hub
- Node.js
- MongoDB
- Mongo Express
- Docker Compose
- Linux / WSL2
- GitHub

---

# Project Structure

```
js-app/
│
├── app/
│   ├── index.html
│   ├── package.json
│   ├── package-lock.json
│   └── server.js
│
├── screenshots/
│
├── Dockerfile
├── docker-compose.yaml
├── commands.txt
└── docker_commands.md
```

---

# Application Setup

The Node.js application dependencies were installed from the application directory.

```bash
cd app
npm install
```

The application was started and validated:

```bash
node server.js
```

Application output:

```
app listening on port 3000!
```

---

# Docker Image Deployment

MongoDB and Mongo Express images were retrieved from Docker Hub.

## MongoDB Image

```bash
docker pull mongo
```

## Mongo Express Image

```bash
docker pull mongo-express
```

---

# Docker Network Configuration

A dedicated Docker bridge network was created to allow communication between containers.

Create network:

```bash
docker network create mongo-network
```

Verify network:

```bash
docker network ls
```

The custom network allowed MongoDB and Mongo Express containers to communicate using container names instead of localhost references.

---

# MongoDB Container Deployment

MongoDB was started as a Docker container with configured credentials and network connectivity.

```bash
docker run -d \
-p 27017:27017 \
-e MONGO_INITDB_ROOT_USERNAME=admin \
-e MONGO_INITDB_ROOT_PASSWORD=password \
--name mongodb \
--net mongo-network \
mongo
```

MongoDB responsibilities:

- Store application data
- Provide database services to the application
- Allow Mongo Express administrative access

---

# Mongo Express Deployment

Mongo Express was deployed as a web-based MongoDB administration interface.

```bash
docker run -d \
-p 8081:8081 \
-e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
-e ME_CONFIG_MONGODB_ADMINPASSWORD=password \
-e ME_CONFIG_MONGODB_SERVER=mongodb \
-e ME_CONFIG_BASICAUTH_USERNAME=user \
-e ME_CONFIG_BASICAUTH_PASSWORD=pass \
--net mongo-network \
--name mongo-express \
mongo-express
```

Mongo Express provided:

- Database visibility
- Collection management
- Data validation
- Administrative access

---

# Database Validation

The application workflow was validated by:

1. Accessing Mongo Express.
2. Creating a users collection.
3. Adding user data.
4. Updating a user profile through the frontend application.
5. Confirming updated information inside MongoDB.

This validated communication between:

```
Frontend Application
        |
        |
Node.js Container
        |
        |
MongoDB Container
```

---

# Dockerfile Implementation

The application was containerized using a custom Dockerfile.

```dockerfile
FROM node:20-alpine

ENV MONGO_DB_USERNAME=admin \
    MONGO_DB_PWD=password

RUN mkdir -p /home/app

COPY ./app /home/app

WORKDIR /home/app

RUN npm install

CMD ["node", "server.js"]
```

## Dockerfile Breakdown

### Base Image

```dockerfile
FROM node:20-alpine
```

Uses a lightweight Node.js Alpine image.

### Application Directory

```dockerfile
RUN mkdir -p /home/app
```

Creates the application directory inside the container.

### Copy Application Files

```dockerfile
COPY ./app /home/app
```

Copies the Node.js application into the image.

### Working Directory

```dockerfile
WORKDIR /home/app
```

Sets the default execution directory.

### Dependency Installation

```dockerfile
RUN npm install
```

Installs application dependencies during image creation.

### Container Startup

```dockerfile
CMD ["node", "server.js"]
```

Starts the application when the container launches.

---

# Docker Compose Configuration

Docker Compose was explored to manage multi-container deployment.

The configuration included:

- MongoDB service
- Mongo Express service
- Service dependency management
- Container restart behavior
- Persistent storage

MongoDB persistence configuration:

```yaml
volumes:
  mongo-data:
    driver: local
```

Database volume mapping:

```yaml
volumes:
  - mongo-data:/data/db
```

This ensures database data is stored outside the container lifecycle.

---

# Troubleshooting and Lessons Learned

## npm Install Failed

### Issue

Running:

```bash
npm install
```

from the project root failed because `package.json` was located inside the application directory.

### Resolution

Navigated to the correct directory:

```bash
cd app
npm install
```

---

## Docker WSL Integration Issue

### Issue

Docker commands initially failed because Docker Desktop was not connected to the WSL2 environment.

### Resolution

Enabled Docker Desktop WSL2 integration.

---

## Docker Command Syntax Issues

Initial container deployment attempts failed because of:

- Incorrect spacing
- Incorrect Docker flags
- Incorrect command formatting
- Image name typo

### Resolution

Reviewed Docker command syntax and successfully deployed the containers.

---

## Container Lifecycle Testing

Containers were stopped and restarted to simulate operational scenarios.

Testing demonstrated that containerized applications can lose data when persistent storage is not configured.

This led to implementing Docker Compose volumes:

```yaml
mongo-data:/data/db
```

to provide persistent database storage.

---

# Screenshots

Project screenshots are stored in:

```
screenshots/
```

Captured workflow:

| Screenshot | Description |
|---|---|
| 07-07-01 | npm install and application setup |
| 07-07-02 | MongoDB Docker Hub image |
| 07-07-03 | MongoDB image pull |
| 07-07-04 | Mongo Express image pull |
| 07-07-05 | Docker network creation |
| 07-07-06 | Node.js application running |
| 07-07-07 | MongoDB and Mongo Express containers |
| 07-07-08 | Mongo Express UI |
| 07-07-09 | Users collection creation |
| 07-07-10 | User collection details |
| 07-07-11 | Frontend profile update |
| 07-07-12 | Updated MongoDB data |
| 07-07-13 | Container persistence testing |

---

# Skills Demonstrated

This project demonstrates practical experience with:

- Docker container lifecycle management
- Building custom Docker images
- Running multi-container environments
- Docker networking
- MongoDB administration
- Linux troubleshooting
- Application debugging
- Infrastructure documentation
- Git-based project management

---

# Future Improvements

Potential enhancements:

- Implement GitHub Actions and or Jenkins CI/CD pipeline
- Push Docker images to a private registry
- Deploy application to AWS
- Add Terraform infrastructure automation
- Create Kubernetes deployment manifests
- Implement automated application testing
- Add monitoring and logging solutions

---

# Author

Ethan Jones

GitHub:

https://github.com/Ejones904
