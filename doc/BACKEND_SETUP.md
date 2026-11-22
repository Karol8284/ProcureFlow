# Backend setup

The backend is an ASP.NET Core 10 API organized as a modular monolith.

## Responsibilities

- Authentication, JWT, refresh-token rotation, email confirmation, 2FA, and
  password reset.
- Organization, branch, membership, catalog, and purchase-request behavior.
- Server-side authorization using verified user and membership context.
- EF Core persistence, migrations, transactions, and concurrency.
- Notifications, email outbox, attachment storage/scanning, health, logging,
  and background workers.

## Projects

- `backend/API`: HTTP composition root, controllers, middleware.
- `backend/Application`: use cases, ports, request-facing contracts, validators.
- `backend/Domain`: entities, value objects, enums, invariants.
- `backend/Infrastructure`: EF Core, stores, integrations, migrations, workers.
- `backend/Shared`: shared responses and settings.
- `backend/UnitTests`: domain and use-case tests.
- `backend/IntegrationTests`: API, EF Core, and PostgreSQL behavior.
- `backend/E2ETests`: smoke checks for a running deployment.

## Configuration

Configuration is read from appsettings files and environment-specific providers.
Important sections include `ConnectionStrings`, `Jwt`, `Cors`, `EmailDelivery`,
`EmailConfirmation`, `EmailTwoFactor`, `AuthSecurity`, `Attachments`,
`DataProtection`, `ForwardedHeaders`, and runtime UI settings.

Use User Secrets, environment variables, CI secrets, or platform secret stores.
Do not add application secrets to appsettings files committed to the repository.

### Data Protection

Data Protection protects authenticator secrets and other framework data. Its key
ring must be durable in deployed environments. Deleting the key ring invalidates
protected data; it is not a normal rotation procedure.

### Reverse proxy and cookies

`ForwardedHeaders` accepts forwarded client/protocol information only from
configured proxy networks. Production refresh cookies use secure transport.
Never configure trust for every address to work around a proxy issue.

## Authentication abuse protection

Public auth endpoints use rate limiting partitioned by client address and
route. Failed logins are also tracked on the user account and can trigger a
temporary lockout. Unknown users, inactive accounts, lockout, and incorrect
passwords receive neutral failure behavior.

Rate limiting is process-local. Multi-instance deployments require a shared or
gateway-level limiter if that becomes an operational requirement.

See [authentication brute-force protection ADR](ADR/AUTH_BRUTE_FORCE_PROTECTION.md).

## Persistence

Use EF Core configuration and migrations for all relational changes. A
transaction boundary belongs to the use case. Stage all business data, history,
notification, and outbox changes that must be atomic before one final
`SaveChangesAsync`.

Use a PostgreSQL test when behavior depends on:

- relational constraints;
- transaction rollback;
- unique indexes;
- optimistic concurrency;
- query behavior that InMemory does not represent accurately.

## Adding backend behavior

1. Identify the product rule and the owner of the invariant.
2. Add or change the domain model and focused tests.
3. Define a use-case command/query and narrow persistence port.
4. Implement the EF adapter and HTTP contract.
5. Register the slice through the owning module.
6. Add authorization, failure-path, API, and PostgreSQL tests as needed.
7. Add a migration when the schema changes.

Use [Adding features](ADDING_FEATURES.md) and the
[module checklist](MODULAR_VSA_MODULE_CHECKLIST.md).

## Validation

```powershell
dotnet restore backend/backend.slnx
dotnet build backend/backend.slnx --configuration Release --no-restore
dotnet test backend/UnitTests/UnitTests.csproj --configuration Release --no-build --no-restore
dotnet test backend/IntegrationTests/IntegrationTests.csproj --configuration Release --no-build --no-restore
```

For the full Docker and browser gate, use
`.\scripts\Invoke-E2ETests.ps1`.
