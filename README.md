# Local Keycloak and pgAdmin development stack

This Docker Desktop stack provides Keycloak, PostgreSQL, and pgAdmin for local OIDC/OAuth2 and AI-agent development.

| Service | Local address | Purpose |
| --- | --- | --- |
| Keycloak | [http://localhost:8081](http://localhost:8081) | OAuth2/OIDC identity provider |
| Keycloak admin console | [http://localhost:8081/admin/](http://localhost:8081/admin/) | Realm, user, and client administration |
| pgAdmin | [http://localhost:5050](http://localhost:5050) | PostgreSQL administration UI |
| PostgreSQL | `localhost:5432` | Direct host-machine database access |

## Start

1. Make sure Docker Desktop is running.
2. Create local credentials from the example file if `.env` does not already exist:

   ```sh
   cp .env.example .env
   ```

3. Start the services:

   ```sh
   docker compose up -d
   ```

4. Open [Keycloak Admin Console](http://localhost:8081/admin/) and sign in with `KEYCLOAK_ADMIN` and `KEYCLOAK_ADMIN_PASSWORD` from `.env`.

Check readiness with:

```sh
docker compose ps
docker compose logs -f keycloak
```

## pgAdmin

pgAdmin is available at [http://localhost:5050](http://localhost:5050). Sign in with `PGADMIN_DEFAULT_EMAIL` and `PGADMIN_DEFAULT_PASSWORD` from `.env`.

To register the included database, use **Register > Server** and enter:

- Host name/address: `postgres`
- Port: `5432`
- Maintenance database: `keycloak`
- Username/password: `POSTGRES_USER` / `POSTGRES_PASSWORD` from `.env`

Use `postgres`, rather than `localhost`, because pgAdmin connects over the Docker Compose network.

For connections made directly from your host machine (for example, `psql` or an IDE), use `localhost` and the `POSTGRES_PORT` value from `.env` instead.

## Local configuration

All development settings are stored in `.env`, which is ignored by Git. The file controls service ports, PostgreSQL credentials, the Keycloak administrator, and the pgAdmin login. See `.env.example` for the required variables and safe development defaults.

Keycloak and PostgreSQL data persist in `./data`. pgAdmin preferences persist in the Docker-managed `pgadmin_data` volume.

## Configure an agent test client

In the admin console, create a realm such as `agent-dev`, then create an OpenID Connect client.

- For an interactive web agent, enable **Standard flow** and add its local callback URL (for example `http://localhost:3000/callback`) to **Valid redirect URIs**.
- For a service-to-service agent, enable **Client authentication** and **Service accounts roles**. Use the client ID and generated client secret with the `client_credentials` grant.

The realm OIDC discovery document is:

```
http://localhost:8081/realms/<realm-name>/.well-known/openid-configuration
```

For the example realm, the issuer is `http://localhost:8081/realms/agent-dev`.

## Stop or reset

```sh
docker compose down
```

This preserves PostgreSQL data in the local `./data` directory. To fully reset local Keycloak data, stop the stack and remove that directory:

```sh
docker compose down -v
rm -rf data
```

This permanently removes Keycloak realms, users, clients, PostgreSQL data, and saved pgAdmin preferences.
