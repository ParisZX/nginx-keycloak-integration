# Nginx-Keycloak Integration

This project demonstrates how to integrate a simple Node.js app with Keycloak for authentication, using Nginx as a reverse proxy. The integration is done through the Lua dependency in Nginx.

## Prerequisites

- Docker
- Docker Compose

## Project Structure

- `docker-compose.yml`: Contains the configuration for all services (Keycloak, PostgreSQL, Node.js app, and Nginx).
- `app`: The folder containing the simple Node.js app.
  - `Dockerfile`: Dockerfile for building the Node.js app container.
  - `index.js`: The main file for the Node.js app.
  - `package.json`: Defines the dependencies and scripts for the Node.js app.
- `nginx`: The folder containing the Nginx configuration and Dockerfile.
  - `Dockerfile`: Dockerfile for building the Nginx container with Lua support (using the OpenResty image).
  - `nginx.conf`: Configuration file for Nginx, including the Lua script for authentication with Keycloak.

## Setup

1. Clone this repository.
2. Navigate to the root directory of the project.
3. Run `docker-compose up` to start all the services.

The Node.js app will be accessible at `http://localhost/`, and the Keycloak admin console at `http://localhost:8080/`. The default Keycloak admin credentials are:

- Username: `admin`
- Password: `admin`

## Keycloak Configuration

1. Log in to the Keycloak admin console with the default credentials.
2. Create a new realm by clicking on the dropdown menu in the top left corner and selecting "Add realm". Give your realm a name (e.g., "myrealm") and click "Create".
3. Navigate to "Clients" in the left-hand menu and click "Create" to add a new client.
4. Enter a `Client ID` (e.g., "my-client"), set `Client Protocol` to "openid-connect", and click "Save".
5. In the "Settings" tab of the client, set `Access Type` to "confidential", and add "http://localhost/redirect_uri" to the `Valid Redirect URIs` field. Click "Save".
6. Copy the `Client Secret` from the "Credentials" tab of the client.

Now you need to update the `nginx.conf` file with the correct realm name, client ID, and client secret:

- Replace `<myrealm>` in the `discovery` URL with the name of the realm you created (e.g., "myrealm").
- Replace `<myrealm-client>` with the client ID you used when creating the client in Keycloak (e.g., "my-client").
- Replace `<myrealm-client-secret>` with the client secret you copied from the "Credentials" tab of the client in Keycloak.

## Custom Resolver in nginx.conf

In the `nginx.conf` file, the custom resolver is configured using `resolver 127.0.0.11 valid=1s ipv6=off;`. This line tells Nginx to use the Docker-embedded DNS server (at IP address 127.0.0.11) to resolve the service names (e.g., "app") into their corresponding IP addresses.

This is necessary because the Docker Compose file creates a network for the services, and the service names can be used as hostnames within that network. The custom resolver enables Nginx to resolve these hostnames and proxy requests to the correct services


## License

This project is open source and available under the [MIT License](LICENSE).
