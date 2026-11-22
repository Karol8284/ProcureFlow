# Local Docker Compose

The root `docker-compose.yml` is the local development and smoke-test
environment. It starts:

- PostgreSQL;
- Mailpit;
- MinIO and its bucket initializer;
- ASP.NET Core backend;
- React nginx frontend.

## Start

```powershell
Copy-Item .env.example .env
docker compose up --build
```

Endpoints:

- frontend: `http://localhost:3000`
- API: `http://localhost:5000`
- Swagger: `http://localhost:5000/swagger`
- Mailpit: `http://localhost:8025`
- MinIO console: the port configured in `.env`

## Configuration

Root `.env` supplies database, JWT, CORS, email, forwarded-header, storage, and
frontend build settings. The frontend argument is named
`FRONTEND_REACT_APP_API_URL` for compatibility and maps to `VITE_API_URL` during
the build.

Keep `.env` local. `VITE_*` values are public; all application secrets stay in
server-side or platform configuration.

## Data protection and attachments

`data-protection-keys` preserves the ASP.NET Core key ring.
`minio-data` preserves attachment binaries. PostgreSQL has its own persistent
volume. Avoid `docker compose down -v` during normal shutdown because it
deletes all of these local data stores.

MinIO is private to the Compose environment for this topology. The one-shot
`minio-init` service creates the bucket and prevents anonymous access before
the backend starts.

## Networking

Nginx proxies `/api/` to the backend service by Docker DNS. Browser JavaScript
does not call the internal container hostname directly; use `/api` for the
Compose frontend build.

## Common commands

```bash
docker compose ps
docker compose logs --no-color backend
docker compose exec backend sh
docker compose down
docker compose down -v  # destructive local reset
```

## Scope

This file uses the `Development` environment and local HTTP endpoints. It is
not a production definition and does not replace TLS, secret management,
monitoring, off-host backup, or rollback procedures. Production assets and
procedures are under `deploy/vps/` and in the
[deployment runbook](../doc/DEPLOYMENT_RUNBOOK.md).
