# ProcureFlow frontend

React 19 and TypeScript client for the ProcureFlow organization, catalog, and
purchase-request workflows.

## Requirements

- Node.js 22.x
- npm and `package-lock.json`

## Development

```powershell
npm ci
npm start
```

Configure `frontend/.env.development.local`:

```env
VITE_API_URL=http://localhost:5000
```

Vite runs the local client on port `3000`.

## Scripts

```powershell
npm run test:once
npm run build
npm run test:e2e
npm run test:e2e:ui
npm run preview
```

The application loads runtime feature flags from `GET /api/runtime-config`.
Feature flags control UI availability only; authorization remains on the
backend.

See [frontend setup](../doc/FRONTEND_SETUP.md) and
[architecture](../doc/ARCHITECTURE.md).
