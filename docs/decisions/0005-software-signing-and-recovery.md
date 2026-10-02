# 0005: Initial software custody, signing guarantees and recovery

- Status: accepted
- Date: 2 October 2026
- Findings: R3, R4 and R5; status-continuity consequences of R8.

## Decision

The initial software tier trusts control-plane authorization, privileged PostgreSQL administrators, signer administrators and platform operators. Tenant separation protects ordinary product paths; it does not promise survival of their privileged compromise.

Start with one active shared signer serving many tenants and authorities. Workers and protocol gateways carry tenant/request references and cannot obtain CA provider credentials or submit arbitrary CA signing bytes. The signer resolves authoritative bindings and permitted structured operations. Profile/grant changes are versioned and audited.

Store encrypted key payloads and metadata together in PostgreSQL. Use maintained authenticated encryption and per-record data keys wrapped by a versioned deployment wrapping key, binding tenant, key identity/version and purpose. Supply wrapping material separately through a protected local signer-only mount or explicit manual unlock. Host/cluster administrators remain inside the declared trust boundary.

Automatic unlock is the default for a routine restart after the prior process is known to have stopped. Manual unlock is an explicit operating profile for restricted deployments; document its renewal and CRL-availability consequences. Neither mode permits uncertain automatic takeover. Keep two independently secured offline recovery copies and named custodians.

Bootstrap the first administrator through a local, single-use setup operation. Provision service TLS through customer-supplied credentials or a locally pinned installation trust mechanism. Recovery access must work independently of the production CA and external identity provider.

## Protected-operation contract

| Boundary | Required behavior |
| --- | --- |
| Admission | Authenticate operation caller; resolve tenant/authority/issuer/key/profile/request and current grants; enforce allowed operation and authority state with the current PostgreSQL ownership generation. |
| Intended certificate | Persist issuer/serial reservation and exact canonical to-be-signed bytes, algorithm and immutable policy/input versions before provider execution. Retries cannot silently change them. |
| Provider execution | An admitted attempt may execute more than once after ambiguity and may finish after a suspension request or connection loss. No exactly-once or instantaneous provider-stop guarantee. |
| Durable result | Select at most one completed certificate result for a durable request within the supported database history; preserve attempt outcomes and issuer/serial uniqueness. |
| Release | Return only the selected committed result after current authorization/release-state checks. Stale attempts cannot overwrite or expose an alternate uncommitted result. |
| Suspension | Recording a request blocks new leaf admission and new leaf-result release. Report effective/drained only after relevant attempts finish or their execution is excluded. Preserve signing history of withheld results. |
| Revocation | Leaf suspension or issuer retirement does not disable separately authorized status operations needed for outstanding certificates. |

Already released certificates remain inventory records; issuance suspension does not itself revoke them. Repeated completed requests still require current read authorization.

PostgreSQL locking and per-authority serialization are candidate mechanisms for the short software path. A database generation/lock is not a guarantee that an already admitted provider operation stops on connection loss. Redis remains a wakeup/cache/temporary-coordination layer.

## Recovery and takeover

A signer replica count, deleted pod object, changed wrapping secret or new local counter is insufficient proof that an old process cannot use an in-memory CA key. Exclude the old signer and primary at the process/node/provider boundary before admitting replacement signing. Prefer explicit unavailability while execution is uncertain.

Manual recovery follows this order:

1. Block admissions and new releases; preserve existing status artifacts with their actual freshness.
2. Establish exclusion of old primary/signer processes and invalidate their service credentials.
3. Restore PostgreSQL, encryption metadata, required WAL and recovery material together; keep signing disabled.
4. Establish complete issuance, revocation, grant, suspension and ownership history using recorded recovery evidence. A fresh recovery epoch labels the new run; it does not establish completeness.
5. Resume each issuer only after reconciliation. Missing or uncertain history leaves it quarantined.
6. If completeness is unrecoverable, use a tested parent-revocation or relying-party distrust/blocking procedure and replacement issuance. A new issuer alone does not invalidate old certificates.

Never publish an incomplete restored CRL as complete current status. Never reuse a pre-rollback counter as globally monotonic. Local durable commits, backups and continuous WAL archiving are the initial operating profile; zero-loss disaster recovery is not implied. A pilot requiring synchronous durability must provide and demonstrate that arrangement.

Keep old wrapping versions until live records and retained backups have a supported recovery path. Retired issuer versions retain required status-signing capabilities. Ordinary CA-key export remains unavailable; any approved custody transfer is a separate protected operator procedure.

PostgreSQL requires exclusion of an old primary after promotion, and Kubernetes force deletion does not confirm the old process has stopped. The signer consequence is FireCA's operating design, to validate in drills. [PostgreSQL failover](https://www.postgresql.org/docs/current/warm-standby-failover.html), [Kubernetes force deletion](https://kubernetes.io/docs/tasks/run-application/force-delete-stateful-set-pod/)

## Validation remaining

Exercise tenant/job/provider-handle substitution, post-queue grant removal, stale cache/generation, pause/partition at admission/provider/persistence/release, ambiguous commit, sign-success before persistence, suspension, old signer during takeover and rollback before known revocation. Demonstrate a fresh disconnected rebuild, one lost recovery copy and interrupted wrapping rotation. Implementation of these guarantees remains unverified.
