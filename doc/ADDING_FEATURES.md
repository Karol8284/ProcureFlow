# Adding features

Use this workflow for a new ProcureFlow capability after `v1.0.0`. The roadmap
defines product priority; code and tests define the current implementation.

## 1. Define the slice

Before editing code, record:

- actor and business problem;
- resource and data ownership;
- successful result;
- invalid states and HTTP behavior;
- transactional boundary;
- authorization rule;
- cheapest test that proves the main invariant;
- explicit items outside scope.

## 2. Choose the owner

Put the rule in the existing module that owns the business state:

- organization and access: `Organizations`;
- catalog data: `Catalog`;
- request workflow, budget, approval, fulfillment, attachments:
  `PurchaseRequests`;
- notification delivery: `Notifications`;
- authentication and accounts: auth/account capability.

Do not create a module only to gain a folder boundary.

## 3. Model the invariant

If the invariant depends only on aggregate state, enforce it in the domain
model. Keep client-provided organization, branch, owner, role, status, price,
and totals untrusted.

Domain tests should run without ASP.NET, EF Core, or PostgreSQL.

## 4. Build the backend slice

Use focused folders:

```text
backend/Application/Modules/<Module>/<UseCase>/
backend/API/Modules/<Module>/<UseCase>/
backend/Infrastructure/Modules/<Module>/<UseCase>/
backend/UnitTests/Modules/<Module>/<UseCase>/
```

The handler coordinates the use case. Narrow ports describe the required
queries and writes. The EF adapter implements those ports, and the controller
translates HTTP. Module registration stays in one module extension.

Do not add a generic repository, MediatR, event bus, or broker without a
recorded requirement.

## 5. Protect persistence

For schema changes:

1. add explicit EF mapping, constraints, and indexes;
2. generate a migration;
3. verify an empty database and the latest supported upgrade path;
4. add PostgreSQL tests for constraints, transactions, or races;
5. keep application rollback and database compatibility in mind.

Stage all writes that must succeed together before one final
`SaveChangesAsync`.

## 6. Add the frontend

After the API contract is stable:

1. add domain types in `frontend/src/types/`;
2. add a focused API client in `frontend/src/services/api/`;
3. add validation and form state;
4. add routing and the page;
5. cover loading, empty, error, success, and stale conflict behavior;
6. add UI and browser tests appropriate to the risk.

## 7. Update documentation

Update current architecture and setup docs only when actual behavior changes.
Create an ADR only for a durable boundary or policy decision. Do not keep
completed file plans, per-branch checklists, or draft open questions in the
repository after implementation.

## Validation order

Start narrow and expand:

1. Domain/unit test for the changed rule.
2. Focused API or handler test.
3. PostgreSQL test when constraints or concurrency are involved.
4. Frontend test and build when the contract or UI changes.
5. Backend build and broader tests.
6. Docker/browser gate before a release candidate.

Use the [module checklist](MODULAR_VSA_MODULE_CHECKLIST.md) before marking a
slice complete.
