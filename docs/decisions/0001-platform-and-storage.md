# 0001: Platform and storage

- Status: accepted
- Date: 2 October 2026

## Context

FireCA needs broad PKI/format support, eventual native Windows enrollment and HSM integration, an API/portal, durable certificate inventory, and multitenant containerized operation. The implementation is expected to be developed with AI assistance, so conventional structure and independently verifiable behavior matter.

## Decision

Use Java/Spring Boot for the backend, Bouncy Castle and Java providers for crypto/PKI building blocks, PostgreSQL for authoritative state, and Redis for temporary coordination and caching. Use container deployments, with Kubernetes as the intended primary target. The official hosted service runs the same product customers self-host.

Every service role can serve many tenants/authorities. Dedicated customer pods or authority-specific workers are optional later allocation/isolation features.

## Consequences

Java's PKI ecosystem favors the intended breadth. Go remains a credible alternative for particular future components, but it is not the selected primary backend. Rust is not selected for the initial product.

Redis leases cannot supply the sole authority for CA ownership or job completion. PostgreSQL claims/idempotency and an enforceable signer contract must protect critical actions from stale workers. Durable notifications/jobs need database reconciliation.

Shared software signing has a shared compromise boundary. Per-tenant records and encryption keys must not be described as equivalent to separate hardware/process isolation.

## Implementation choices remaining

Supported versions, build tooling, exact initial role/module packaging, provider storage, and the protected signer/ownership contract require decisions and validation before implementation. This record makes no performance claims or interoperability guarantees.
