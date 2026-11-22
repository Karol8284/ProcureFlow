# `v1.0.0` release checklist

This checklist is for the exact immutable commit candidate. A local build is
necessary but is not sufficient for a release decision.

## 1. Repository gate

- [ ] `dotnet build backend/backend.slnx --configuration Release` passes.
- [ ] Backend unit and integration tests pass.
- [ ] PostgreSQL Testcontainers coverage runs with real migrations.
- [ ] Frontend tests and production build pass.
- [ ] Docker Compose health, HTTP smoke, and Playwright flows pass.
- [ ] NuGet and npm audits are recorded for the candidate.
- [ ] Container image scans have no unresolved HIGH or CRITICAL findings accepted
      by the release policy.
- [ ] Deployment Compose, Caddy, Prometheus, Alertmanager, and scripts validate.
- [ ] The expected schema migration is included and reviewed for rollback
      compatibility.

## 2. Staging gate

- [ ] CI passed for the candidate SHA.
- [ ] CD published immutable backend and frontend images for that SHA.
- [ ] The protected `staging` environment approved the deployment.
- [ ] `/health/live`, `/health/ready`, and `/health/workers` return healthy.
- [ ] TLS certificate, HSTS, cookies, CORS, and email links use the target
      domain.
- [ ] Registration, confirmation, login, and 2FA flows pass over HTTPS.
- [ ] Employee request creation, submission, manager approval/escalation,
      procurement fulfillment, and attachments pass end to end.
- [ ] PostgreSQL, storage, malware-scanner, email, and worker signals are
      operational.
- [ ] Grafana receives application and host metrics.
- [ ] Prometheus rules load and Alertmanager delivers a test notification.

## 3. Recovery gate

- [ ] A coordinated backup was copied to off-host storage.
- [ ] Backup checksums and encryption were verified.
- [ ] The backup was restored in staging or an isolated clone.
- [ ] Authentication sessions, attachment access, migrations, and health were
      verified after restore.
- [ ] Manual rollback restored the previous immutable image and passed
      readiness.
- [ ] Restore and rollback duration, candidate SHA, and known limitations were
      recorded.

## 4. Release decision

Release only when:

- all mandatory boxes have evidence tied to the candidate commit;
- staging behavior is compatible with production configuration;
- the previous image and a verified backup are available;
- no environment-secret, host-key, migration, or scanning blocker remains;
- product limitations are accepted and documented.

After the decision, tag `v1.0.0` and publish release notes from the same
immutable commit. The optional CD image tag does not replace the Git release
decision.
