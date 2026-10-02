# Working on FireCA

## Project stage and source of truth

- FireCA is currently a design repository. Do not claim that a planned feature, deployment, or interoperability result has been implemented or verified.
- Read `docs/product-scope.md`, `docs/architecture.md`, and `docs/roadmap.md` before implementing features. Decision records identify accepted product decisions; proposed implementation details remain revisable.
- Maintain repository documentation alongside code. Record consequential architecture changes in `docs/decisions/` and update affected contracts and milestone criteria.
- The selected backend is Java/Spring Boot with Bouncy Castle, PostgreSQL, and Redis. Choose and record supported dependency versions before creating a runnable scaffold.

## Implementation principles

- Use maintained cryptographic and ASN.1 libraries. FireCA owns policy, authorization, custody integration, and lifecycle logic.
- Preserve tenant and authority authorization through APIs, background jobs, protocol gateways, exports, and signer operations.
- PostgreSQL is authoritative. Redis leases and notifications must not be the sole protection for issuance, rotation, durable jobs, or audit records.
- Reject stale ownership generations at protected operations. Do not describe a Redis lease as sufficient for exclusive CA ownership.
- Keep application private keys, CA keys, standalone keys, and general secrets distinct in policy and API permissions. CA keys must not be available through ordinary arbitrary-signing endpoints.
- Never commit live private keys, credentials, tokens, private certificate bundles, database dumps, or customer data. Any later cryptographic test fixtures must be explicitly synthetic and documented.
- Native Windows autoenrollment is an eventual required feature. An inventory agent does not satisfy that requirement.
- Preserve disconnected self-hosting: avoid mandatory cloud callbacks, remotely loaded UI assets, or online license activation.
- Store licensing decisions in `docs/licensing.md`. Do not silently adopt a permissive or institutional-exemption license.

## Verification

- Run checks appropriate to the change and report what was actually verified.
- PKI implementation must demonstrate interoperability with independent clients, not merely parse its own output.
- Exercise authorization and concurrency failure cases when implementing issuance and custody.
- Use only synthetic identities and keys in examples and tests.
