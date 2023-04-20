# Nginx-Keycloak Integration

This project demonstrates how to integrate a simple Node.js app with Keycloak for authentication, using Nginx as a reverse proxy. The integration is done through the Lua dependency in Nginx.

## How It Works

This project uses Docker Compose to create and manage four services: Keycloak, PostgreSQL (as the database for Keycloak), the Node.js app, and Nginx with Lua support. The integration between Nginx and Keycloak is done using the Lua script in the `nginx.conf` file.

When a user tries to access the protected Node.js app, Nginx uses the Lua script to check whether the user is authenticated with Keycloak. If the user is not authenticated, they will be redirected to the Keycloak login page. After successful authentication, the user is redirected back to the Node.js app, and Nginx proxies the request to the app.

The Lua script in the `nginx.conf` file also handles the logout process, redirecting the user to the Keycloak logout endpoint and then back to the app's main page.

## OpenID Connect Workflow

OpenID Connect (OIDC) is an authentication layer built on top of the OAuth 2.0 authorization framework. It allows clients to verify the identity of end-users based on the authentication performed by an authorization server (in our case, Keycloak). OIDC also enables clients to obtain basic profile information about the end-users in a standardized and REST-like manner.

The typical OIDC workflow consists of the following steps:

1. The user tries to access a protected resource (in our case, the Node.js app).
2. The client (Nginx with Lua) redirects the user to the authorization server (Keycloak) to authenticate and provide consent.
3. The authorization server (Keycloak) authenticates the user and asks for their consent (if necessary).
4. The user is redirected back to the client (Nginx with Lua) with an authorization code.
5. The client (Nginx with Lua) exchanges the authorization code for an access token and an ID token at the token endpoint of the authorization server (Keycloak).
6. The access token can be used to access protected resources on behalf of the user, and the ID token contains user profile information.

In this project, the Lua script in the `nginx.conf` file handles the OIDC authentication flow. It redirects unauthenticated users to Keycloak, handles the authorization code grant flow, and validates the ID token. Once the user is authenticated, Nginx proxies the request to the Node.js app.

For more information about OpenID Connect, visit the [official documentation](https://openid.net/connect/).

## Session and Token Management

In this example, the session and token management is handled by the Nginx server, specifically the Lua script in the `nginx.conf` file.

When a user successfully authenticates with Keycloak, an ID token and an access token are issued. The ID token contains user profile information, while the access token can be used to access protected resources on behalf of the user.

The Lua script in the `nginx.conf` file uses the [lua-resty-openidc](https://github.com/zmartzone/lua-resty-openidc) library to manage the tokens. The library takes care of exchanging the authorization code for the tokens, validating the tokens, and storing them in a session.

By default, the session is stored in a cookie in the user's browser, and is encrypted using AES with a random secret. The session cookie is used to keep track of the user's authentication state. On subsequent requests to the protected resource, the Lua script checks the session to see if the user is already authenticated, and if so, allows access to the resource.

The session management configuration can be customized in the `nginx.conf` file, by modifying the `opts` table in the `access_by_lua` block. For example, you can change the session storage mechanism, set the cookie expiration time, or enforce additional security settings for the cookie.

For more information on configuring the session management options, please refer to the [lua-resty-openidc documentation](https://github.com/zmartzone/lua-resty-openidc#session-management).

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

## Disclaimer

This project serves as an example of how to integrate a Node.js app with Keycloak for authentication, using Nginx as a reverse proxy and Lua for scripting. It is not intended for production use and may not cover all possible security concerns or edge cases. Before deploying a similar setup in a production environment, make sure to thoroughly review and test the configuration and ensure it meets your security requirements.

Always keep your Keycloak instance, Nginx, Node.js app, and any dependencies up to date, and follow best practices for securing your environment.

## License

This project is open source and available under the [MIT License](LICENSE).
