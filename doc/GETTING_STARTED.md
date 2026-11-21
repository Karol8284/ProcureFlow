# Getting started

## Prerequisites

- .NET 10 SDK
- Node.js 22.x and npm
- Docker with Docker Compose
- PostgreSQL only if the backend is run outside the Compose stack

## Docker Compose

From the repository root:

```powershell
Copy-Item .env.example .env
docker compose up --build
```

Endpoints:

- frontend: `http://localhost:3000`
- API: `http://localhost:5000`
- health: `http://localhost:5000/health`
- liveness: `http://localhost:5000/health/live`
- readiness: `http://localhost:5000/health/ready`
- worker health: `http://localhost:5000/health/workers`
- Swagger: `http://localhost:5000/swagger`
- Mailpit: `http://localhost:8025`

Stop without deleting persistent data:

```powershell
docker compose down
```

`docker compose down -v` deletes the local database, attachment data, and Data
Protection keys. It is a destructive reset, not a normal shutdown command.

## Backend without Docker

Provide a PostgreSQL connection string and the required secrets through User
Secrets, environment variables, or another local secret store.

```powershell
dotnet restore backend/backend.slnx
dotnet run --project backend/API/API.csproj
```

Configuration is loaded from `appsettings.json`,
`appsettings.Development.json`, and environment-specific providers.

## Frontend without Docker

```powershell
Set-Location frontend
npm ci
Copy-Item .env.example .env.development.local
npm start
```

For a local backend, set:

```env
VITE_API_URL=http://localhost:5000
```

For nginx proxying, use `VITE_API_URL=/api`. Variables beginning with `VITE_`
are public build-time values and must never contain secrets.

## Build and test

```powershell
dotnet build backend/backend.slnx --configuration Release
dotnet test backend/UnitTests/UnitTests.csproj --configuration Release
dotnet test backend/IntegrationTests/IntegrationTests.csproj --configuration Release
dotnet test backend/E2ETests/E2ETests.csproj --configuration Release
Set-Location frontend
npm run test:once
npm run build
Set-Location ..
```

PostgreSQL integration tests require Docker. The full local gate, including
container smoke and browser tests, is:

```powershell
.\scripts\Invoke-E2ETests.ps1
```

## Troubleshooting

Check, in order:

1. Docker, .NET, Node.js, and PostgreSQL availability.
2. Backend secrets and connection-string configuration.
3. `GET /health/live`, then `GET /health/ready`.
4. `GET /api/runtime-config`.
5. Frontend `VITE_API_URL` and browser network requests.
6. Container or process logs using the request correlation ID.

For deployment troubleshooting, use the
[deployment runbook](DEPLOYMENT_RUNBOOK.md).
