# Deployment and operations runbook

The maintained deployment model is one Linux VPS running Docker Compose.
It keeps the operational surface proportional to a modular monolith while
providing immutable images, TLS, migrations, durable data, monitoring, backup,
restore, and rollback.

Definitions are under `deploy/vps/`. The public entry point is Caddy; the API,
frontend, PostgreSQL, MinIO, ClamAV, and monitoring services remain private.

## Compatibility identifiers

Operational names such as `dotnet-react-starter`, `dotnet-react`, image names,
container paths, and volumes are compatibility identifiers. Renaming them is
not a documentation-only task: it can break sessions, Data Protection, storage
paths, backups, staging, and rollback.

## Host requirements

Use a supported Ubuntu LTS host with:

- Docker Engine and Docker Compose from the official installation source;
- enough CPU, memory, and SSD for PostgreSQL, object storage, metrics, ClamAV,
  and at least two backup generations;
- a dedicated deployment account and restricted SSH;
- inbound `80` and `443`;
- DNS records for the application domain;
- `gnupg` for encrypted backups.

The Docker group grants root-equivalent control. Grant it only to trusted host
operators.

## Environment setup

Copy `deploy/vps/.env.production.example` to a protected path outside the
repository. The file must have restricted ownership and permissions. Never
commit it, attach it to issues, or place it in CI logs.

Required protected GitHub `staging` variables:

- `STAGING_SSH_HOST`
- `STAGING_SSH_USER`
- `STAGING_APP_PATH`
- `STAGING_ENV_FILE`
- `STAGING_DOMAIN`

Required secrets:

- `STAGING_SSH_PRIVATE_KEY`
- pinned `STAGING_SSH_KNOWN_HOSTS`
- `STAGING_GHCR_READ_TOKEN`

Do not run `ssh-keyscan` during deployment; host identity must already be
trusted and pinned.

## Staging deployment

1. Merge or select a commit that passed CI.
2. Run the CD workflow from `main`.
3. Publish the immutable full-SHA images.
4. Approve the protected `staging` environment.
5. Confirm the migration container completed.
6. Verify public health, TLS, and browser smoke.
7. Run the critical purchase-request workflow.
8. Verify metrics, alert rules, and a test Alertmanager notification.

The deployment script serializes releases and records active/previous image
tags. It automatically rolls back on deployment startup or readiness failure.

## Database migration

The API does not apply migrations automatically in production:

```text
Database__ApplyMigrationsOnStartup=false
```

A one-shot migration container uses `--migrate-only`, and the API depends on its
successful completion.

Use expand-and-contract for schema evolution. Application rollback does not run
an automatic EF down migration. A rollback-incompatible migration must stop the
release before production.

## Backup and restore

A complete backup must contain:

- a PostgreSQL custom-format dump or equivalent snapshot;
- the complete private attachment bucket;
- the ASP.NET Core Data Protection key ring;
- image and migration metadata;
- a checksum manifest.

Copy the encrypted archive to off-host storage. A backup stored only on the
application host is not disaster recovery.

Perform the first restore drill on staging or an isolated clone:

```bash
./restore.sh \
  /etc/dotnet-react-starter/production.env \
  /var/backups/dotnet-react-starter/<backup>.tar.gz.gpg \
  --force \
  https://staging.example.com \
  /etc/dotnet-react-starter/backup-encryption.key \
  staging
```

After restore:

1. Verify all three health endpoints.
2. Verify an existing authenticated session where applicable.
3. Download a previously stored attachment.
4. Create and delete a new attachment.
5. Run attachment reconciliation and inspect drift.
6. Record duration and snapshot identity.

## Monitoring

Prometheus, Alertmanager, Grafana, blackbox exporter, and node-exporter are
defined under `deploy/vps/observability/`.

Configured alerts cover process and dependency health, storage/scanner/email
failures, worker health, and host capacity. Logs use bounded rotation. Join API
and proxy events by `X-Correlation-ID`.

## Incident handling

### API not ready

1. Check liveness and readiness separately.
2. Inspect database, MinIO, ClamAV, and backend health.
3. Preserve logs before restarting.
4. Identify the failing dependency rather than repeating restarts.

### Database unavailable

1. Stop public writes at Caddy.
2. Inspect disk space and PostgreSQL logs.
3. Restore only from a verified coordinated snapshot if data is damaged.

### Storage or scanner unavailable

Uploads must fail closed. Do not disable malware scanning to regain
availability. Repair the dependency and rerun attachment smoke tests.

### Worker unhealthy

Inspect `/health/workers`, dependency connectivity, and the latest worker
cycle. Restart only after preserving logs and identifying the failure.

## Manual rollback

```bash
./rollback.sh \
  /etc/dotnet-react-starter/production.env \
  https://staging.example.com
```

Rollback changes images, not database schema. It is valid only when the prior
image remains compatible with the migrated database. Otherwise stop the
application and restore the verified backup.

## Release closeout

Use [RELEASE_CHECKLIST.md](RELEASE_CHECKLIST.md). A deployment success alone
does not prove restore, rollback, or product acceptance.
