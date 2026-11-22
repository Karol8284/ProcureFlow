# Backend container

`backend/Dockerfile` is a multi-stage .NET 10 image:

1. The SDK stage restores and publishes `API.csproj`.
2. The ASP.NET runtime stage copies the published output.
3. The image creates writable attachment, log, and Data Protection paths.
4. The API runs as a non-root application user.

Build from the repository root:

```bash
docker build -t procureflow-backend:local -f backend/Dockerfile backend
```

The container still requires a database connection string, JWT settings, and
the remaining runtime configuration. For local use, prefer the root Compose
file:

```bash
docker compose up --build
```

The backend listens on port `5000`. Health checks are exposed under `/health`,
`/health/live`, and `/health/ready`.

Production deployment does not use this image as a standalone container. Use
the deployment assets and [runbook](../doc/DEPLOYMENT_RUNBOOK.md).
