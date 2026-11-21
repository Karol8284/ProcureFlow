# ADR: Modular vertical slices

## Status

Accepted for new product work.

## Context

The backend is one deployable modular monolith. Business behavior must be easy
to locate and test without splitting every feature into a separate service.
Large application services and direct `DbContext` access make ownership and
transaction boundaries harder to reason about.

## Decision

Use one focused slice per use case in the owning business module:

```text
Application/Modules/<Module>/<UseCase>/
API/Modules/<Module>/<UseCase>/
Infrastructure/Modules/<Module>/<UseCase>/
UnitTests/Modules/<Module>/<UseCase>/
```

- API depends on a handler contract, not persistence.
- The handler coordinates domain invariants and transaction scope.
- Persistence ports describe the exact use-case read or write.
- EF Core implementations stay in Infrastructure.
- Module registration is exposed through one composition extension.
- Cross-module work uses an explicit identifier, read model, port, or
  application workflow.

## Consequences

More files are required than a controller-plus-service approach, but each
dependency and failure path is visible. The application remains one process,
one `ApplicationDbContext`, and one PostgreSQL database. MediatR, a broker, and
distributed transactions are not implied.

Existing older layouts should migrate only when a real change touches them, not
through a repository-wide rewrite.

## Review triggers

Revisit if independent deployment, separately owned schemas, sustained
cross-module contention, or measurable operational needs justify stronger
boundaries.
