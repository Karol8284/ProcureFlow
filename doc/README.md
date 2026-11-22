# Documentation map

The documentation is intentionally split by maintenance responsibility. The
current code and tests are authoritative for implemented behavior.

## Start here

1. [Getting started](GETTING_STARTED.md) - local setup and validation commands.
2. [Roadmap](ROADMAP.md) - completed product scope, release work, and post-v1
   options.
3. [Architecture](ARCHITECTURE.md) - active modules, boundaries, and data flow.
4. [Release checklist](RELEASE_CHECKLIST.md) - evidence required for `v1.0.0`.

## Current implementation

- [Backend setup](BACKEND_SETUP.md)
- [Frontend setup](FRONTEND_SETUP.md)
- [Authentication and JWT architecture](JWT_ARCHITECTURE.md)
- [Email and 2FA flows](EMAIL_2FA_FLOWS.md)
- [Attachment operations](ATTACHMENT_OPERATIONS.md)

## Development

- [Adding features](ADDING_FEATURES.md)
- [Modular VSA module checklist](MODULAR_VSA_MODULE_CHECKLIST.md)
- [AI-assisted development workflow](AI_ASSISTED_DEVELOPMENT_WORKFLOW.md)
- [ADRs](ADR/)

## Operations

- [Docker Compose](../docker/DOCKER_COMPOSE.md)
- [Backend image](../backend/DOCKER.md)
- [Frontend image](../frontend/DOCKER.md)
- [CI/CD](CI_CD.md)
- [Deployment runbook](DEPLOYMENT_RUNBOOK.md)
- [Release checklist](RELEASE_CHECKLIST.md)

## Documentation rules

- A roadmap describes decisions and remaining work, not implementation history.
- Completed implementation plans are removed once their useful decisions are
  represented in current architecture or ADRs.
- Audits and test counts are not evergreen documentation. New release evidence
  belongs to CI artifacts or a release record tied to an immutable commit.
- Historical names may remain in migrations, compatibility identifiers, and
  deployment assets when renaming would risk data or rollback compatibility.
