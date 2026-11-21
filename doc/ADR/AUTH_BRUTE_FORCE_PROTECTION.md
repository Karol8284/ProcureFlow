# ADR: Authentication brute-force protection

## Status

Accepted and implemented.

## Context

Public authentication endpoints must resist repeated password and token guesses
without exposing whether an account exists. The application may run as one
instance in the current deployment model.

## Decision

- Apply a shared fixed-window `AuthPolicy` to public auth operations.
- Partition the limit by client address and endpoint path.
- Track failed password attempts on the current user.
- Temporarily lock the account after the configured threshold.
- Return neutral responses for unknown users, incorrect passwords, inactive
  accounts, and lockout.
- Protect counter updates with optimistic concurrency.
- Validate trusted proxy settings before relying on the forwarded client IP.

## Consequences

The protection is simple and testable without external infrastructure. It is
process-local, so a multi-instance deployment must add gateway-level or shared
rate limiting. A generic IP partition can also affect users behind one public
NAT address.

## Review triggers

Revisit the decision if the service runs on multiple instances, a real abuse
pattern differs from the fixed-window model, or proxy ownership changes.
