# Nginx-Keycloak Integration Example

This example demonstrates how to use Nginx as a reverse proxy with Keycloak integration to provide access control for a sample Node.js service.

## Overview

The project consists of three services:

1. Keycloak - Provides authentication and authorization
2. Nginx - Acts as a reverse proxy and enforces access control using Keycloak
3. Node.js - A simple Node.js service

## Project Structure
```
.
├── docker-compose.yml
├── nginx
│ ├── Dockerfile
│ └── nginx.conf
└── nodejs
├── index.js
└── package.json
```

- `docker-compose.yml`: The Docker Compose configuration file that defines the services and their relationships.
- `nginx`: The folder containing the Nginx configuration file and custom Dockerfile.
- `nodejs`: The folder containing the sample Node.js service files.

## Setup

1. Make sure Docker and Docker Compose are installed on your system.

2. Clone this repository:
```
git clone https://github.com/your-username/your-repo-name.git
cd your-repo-name
```
Make sure to replace your-username and your-repo-name with the appropriate values for your GitHub account and repository.

3. Start the services using Docker Compose:
```
docker-compose up
```

4. Access the Keycloak admin console at `http://localhost:8080/auth/` and log in with the username `admin` and password `admin`.

2. Create a new realm in Keycloak and configure the client as needed. Update the `nginx/nginx.conf` file with the appropriate `your_realm_name`.

3. Access the Node.js service at `http://localhost/`. You should be redirected to the Keycloak login page if you're not authenticated.

## Notes

This example is intended for demonstration purposes and should not be used as-is in a production environment. For production use, consider using a separate database for Keycloak, configuring SSL/TLS for secure communication, and following best practices for securing your application and infrastructure.
