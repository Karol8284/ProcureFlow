# Frontend container

`frontend/Dockerfile` builds the Vite application with Node.js 22 and serves
the generated `dist/` output through nginx.

Build from the repository root:

```bash
docker build \
  --build-arg VITE_API_URL=/api \
  -t procureflow-frontend:local \
  -f frontend/Dockerfile \
  frontend
```

`VITE_API_URL` is a public build-time value. The nginx image proxies `/api/`
to the backend in the Compose network and listens on port `3000`.

For local development with hot reload, run Vite directly instead of the image:

```powershell
Set-Location frontend
npm ci
npm start
```

For the complete stack, use `docker compose up --build` from the repository
root. See [Docker Compose](../docker/DOCKER_COMPOSE.md).
