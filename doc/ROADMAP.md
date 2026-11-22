# ProcureFlow roadmap

## Current objective

Validate and release the implemented `v1.0.0` product. Do not reopen completed
feature stages or begin new product work before the release decision.

PF labels describe the original implementation sequence; they are not semantic
release versions.

## Completed product scope

| Stage | Outcome | Status |
|---|---|---|
| PF0 | MVP scope, product workflow, and simplifying decisions | Complete |
| PF1 | Organization, branches, memberships, and business roles | Complete |
| PF2 | Categories, units of measure, and product catalog | Complete |
| PF3 | Purchase-request draft, items, submission, and cancellation | Complete |
| PF4 | Monthly budgets, approval, rejection, escalation, and concurrency | Complete |
| PF5 | Fulfillment, attachments, notifications, dashboard, and browser E2E | Complete |
| PF6 | Retire the demo domain, clean the schema, and prepare release documentation | Complete locally |

The product is one organization with one active branch membership per user. The
release remains intentionally smaller than an ERP: it does not provide stock,
invoicing, payments, supplier marketplace, external ERP integrations, or
configurable workflow engines.

## `v1.0.0` release work

The remaining work is operational and evidence-based:

1. Run fresh repository validation for the exact release commit.
2. Run the protected staging deployment and critical browser workflow.
3. Verify migration, TLS, cookies, health, storage, malware scanning, and email.
4. Copy a backup off host and complete a restore drill.
5. Execute and record a rollback.
6. Record known limitations and make the release decision.

The exact acceptance criteria are in the
[release checklist](RELEASE_CHECKLIST.md).

## Post-`v1.0.0` options

Do not implement these merely to expand the technology list. Select one only
after a recorded product or operational need:

- multi-organization isolation;
- multiple branch memberships with an active-context selection;
- supplier management;
- configurable approval rules;
- richer reporting and export;
- measured performance improvements;
- managed database or object-storage migration;
- reusable project-module extraction.

## Decision rules

- Prefer the smallest coherent vertical slice.
- Keep ownership, authorization, and transaction rules on the backend.
- Use PostgreSQL integration tests for constraints, transactions, and races.
- Treat frontend flags as UX controls, not authorization.
- Do not rename compatibility identifiers until session, storage, backup,
  deployment, and rollback paths have a coordinated migration.
