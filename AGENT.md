# Repository guide for agents

## Purpose

Maintain a local Docker Desktop development environment for Keycloak, PostgreSQL, and pgAdmin. It is intended for OAuth2/OIDC and AI-agent integration testing, not production deployment.

## Key files

- `docker-compose.yaml` defines all services and their local ports.
- `.env` contains local credentials and port overrides. It is intentionally ignored by Git.
- `.env.example` documents every environment variable with development-safe defaults.
- `data/` is the host-mounted PostgreSQL data directory. Do not edit its contents directly.

## Working conventions

- Preserve data by default. Do not remove `data/` or run `docker compose down -v` unless explicitly requested.
- Validate Compose changes with `docker compose config` before starting or recreating services.
- Use `postgres` as the database host from a Compose service; use `localhost` only from the host machine.
- Keep Keycloak on port `8081` and pgAdmin on port `5050` unless a requested change or port conflict requires otherwise.
- Never commit `.env`, database files, secrets, access tokens, or pgAdmin state.
- After changing database credentials, align PostgreSQL and Keycloak configuration before recreating services; changing bootstrap credentials does not reset an existing Keycloak administrator.

## Verification

Use `docker compose ps` to inspect service state. Verify Keycloak with its OIDC discovery URL and pgAdmin at `http://localhost:5050`. Do not print credential values or access tokens in command output.
