# ADR: Authentication session policy

## Status

Accepted and implemented.

## Context

The frontend needs short-lived API access while retaining a browser session
without exposing refresh-token material to JavaScript. Account activity,
profile, and role must be read from the current user record during refresh.

## Decision

- Issue a short-lived JWT access token.
- Keep the refresh token in an HttpOnly cookie.
- Store only a hash of the refresh token in PostgreSQL.
- Rotate the refresh token on every successful refresh.
- Mark the predecessor as `TokenRotated` and link it to its successor.
- Keep a stable family identifier for replay handling.
- Use an EF concurrency token so one token cannot rotate successfully twice.
- Treat a reused rotated token as replay and revoke active family tokens.
- Reject refresh for a missing or inactive account.
- Read role, activity, confirmation, and profile data from the current user.
- Do not revoke an already issued JWT on password changes, logout-all, or
  deactivation before its normal expiry.

## Consequences

Refresh is auditable and replay-resistant for the current single-instance
application. Short JWT lifetime limits the delay for role and account changes.
Immediate global revocation would require an additional token version,
deny-list, introspection, or session service and is not currently implemented.

Reactivation is meaningful: a refresh blocked while inactive can be used later
only if the historical refresh token is still otherwise valid and no family
revocation occurred.

## Review triggers

Revisit if access revocation must be immediate, multiple instances require
shared session state, or the JWT/access-token lifetime becomes materially
longer.
