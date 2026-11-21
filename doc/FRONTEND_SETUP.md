# Frontend setup

The frontend is a React 19 and TypeScript application built by Vite.

## Responsibilities

- Public and protected routing.
- Session and runtime-configuration bootstrap.
- Authentication, 2FA, profile, organization, catalog, and purchase-request UI.
- Typed API clients and consistent loading/error states.
- UI feature gating and product navigation.

## Structure

- `src/components/`: application shell and reusable UI.
- `src/context/`: auth, runtime configuration, and notification state.
- `src/hooks/`: context access and feature availability.
- `src/pages/`: route-level screens.
- `src/services/api/`: typed backend clients.
- `src/types/`: request and response contracts.
- `src/utils/`: validation and formatting.
- `src/tests/`: component, page, context, and service tests.
- `e2e/`: Playwright workflows.

## Bootstrap

`RuntimeConfigProvider` loads `/api/runtime-config`, while `AuthProvider`
restores the authenticated session. `AppBootstrapGate` waits for both before
rendering the shell.

The access token is stored by the client; the refresh token stays in an
HttpOnly cookie. `HttpClient` includes credentials and can retry one request
after a successful token refresh.

## Routing and authorization

`AppRoutes.tsx` owns route classification. Protected and admin routes are
handled centrally.

Frontend feature flags and hidden controls are UX decisions. The backend
remains responsible for authorization and must reject requests that bypass the
UI.

## Forms and API contracts

Forms use React Hook Form and Zod. Domain API contracts live in
`src/types/`, and HTTP calls use focused clients from `src/services/api/`.

Every new product screen should handle:

- initial loading;
- empty results;
- API failure;
- validation failure;
- stale `409 Conflict` recovery where relevant.

## Development

```powershell
Set-Location frontend
npm ci
npm start
```

Use `frontend/.env.development.local`:

```env
VITE_API_URL=http://localhost:5000
```

All `VITE_*` values become public at build time. Never put secrets in them.

## Validation

```powershell
npm run test:once
npm run build
npm run test:e2e
```

Playwright tests require the expected frontend, backend, and Mailpit URLs. The
full Compose setup is started by `.\scripts\Invoke-E2ETests.ps1`.
