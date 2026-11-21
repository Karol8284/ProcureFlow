# ProcureFlow

ProcureFlow is a full-stack B2B purchase-request system for an organization with
multiple branches. Employees create requests, managers approve within branch
budgets, and procurement handles escalation, ordering, and delivery.

## Release status

The `v1.0.0` product scope is implemented as a local release candidate. A formal
release still requires fresh CI, staging, backup, restore, and rollback evidence.
Documentation must not treat historical test counts as evidence for the current
commit.

See the [product roadmap](doc/ROADMAP.md) and
[release checklist](doc/RELEASE_CHECKLIST.md).

## Product scope

- One organization with multiple branches and branch-scoped memberships.
- Catalog of categories, units of measure, and products.
- Purchase-request drafts, line items, submission, and cancellation.
- Monthly branch budgets, manager approval, and procurement escalation.
- Ordered and delivered fulfillment states.
- Approval history, notifications, attachments, and a product dashboard.
- PostgreSQL transaction and concurrency protection for critical operations.
- Browser tests for authentication and the critical purchase-request workflow.

## Stack

### Backend

- .NET 10 LTS and ASP.NET Core
- Entity Framework Core and PostgreSQL
- FluentValidation and Serilog
- xUnit and Moq

### Frontend

- React 19 and TypeScript
- React Router, React Hook Form, and Zod
- Vite, Vitest, Testing Library, and Playwright

## Repository layout

```text
backend/
  API/                 HTTP host, controllers, middleware
  Application/         use cases, ports, DTOs, validators
  Domain/              entities, value objects, domain rules
  Infrastructure/      EF Core, persistence, integrations, workers
  Shared/              shared responses and settings
  UnitTests/
  IntegrationTests/
  E2ETests/
frontend/              React application
deploy/vps/            production and staging deployment assets
docker/                local Compose documentation
doc/                   maintained project documentation
scripts/               local validation and backup automation
```

## Quick start

Prerequisites: .NET 10 SDK, Node.js 22, and Docker with Compose.

```powershell
git clone https://github.com/Karol8284/ProcureFlow.git
Set-Location ProcureFlow
Copy-Item .env.example .env
docker compose up --build
```

Local endpoints:

- Frontend: `http://localhost:3000`
- API: `http://localhost:5000`
- Health: `http://localhost:5000/health`
- Swagger: `http://localhost:5000/swagger`
- Mailpit: `http://localhost:8025`

The default Compose stack is for development and smoke testing, not production.
For configuration and non-container development, read
[Getting Started](doc/GETTING_STARTED.md).

## Validation

Repository validation:

```powershell
dotnet build backend/backend.slnx --configuration Release
dotnet test backend/UnitTests/UnitTests.csproj --configuration Release
dotnet test backend/IntegrationTests/IntegrationTests.csproj --configuration Release
Set-Location frontend
npm ci
npm run test:once
npm run build
Set-Location ..
.\scripts\Invoke-E2ETests.ps1
```

The integration suite includes PostgreSQL Testcontainers scenarios, so Docker
must be available. `E2ETests` and Playwright require a running application
stack or the script above to create it.

## Security and operations

- Access tokens are JWTs; refresh tokens are rotated and stored in HttpOnly
  cookies.
- Email confirmation and email-based 2FA are supported.
- Product authorization is enforced by the backend using verified identity and
  branch membership.
- Attachments require private storage and fail closed when required malware
  scanning is unavailable.
- CI builds and tests the repository, scans container images, and supports a
  protected staging deployment.
- Production promotion is manual and requires the
  [release checklist](doc/RELEASE_CHECKLIST.md).

## Documentation

- [Documentation map](doc/README.md)
- [Product roadmap](doc/ROADMAP.md)
- [Architecture](doc/ARCHITECTURE.md)
- [Getting started](doc/GETTING_STARTED.md)
- [Backend setup](doc/BACKEND_SETUP.md)
- [Frontend setup](doc/FRONTEND_SETUP.md)
- [Authentication architecture](doc/JWT_ARCHITECTURE.md)
- [Email and 2FA flows](doc/EMAIL_2FA_FLOWS.md)
- [CI/CD](doc/CI_CD.md)
- [Deployment runbook](doc/DEPLOYMENT_RUNBOOK.md)

See the [Polish README](README.PL.md) for a Polish overview.

## License

MIT. See [LICENSE](LICENSE).
