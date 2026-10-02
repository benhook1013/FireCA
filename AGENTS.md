# Working on FireCA

## Project stage and source of truth

- FireCA is currently a design repository. Do not claim that a planned feature, deployment, or interoperability result has been implemented or verified.
- Keep personal identity, workstation paths/names and private conversational background out of public files and review reports. Session-specific instructions stay local to the session; use project-relative paths and synthetic examples in published material.
- Check staged content and commit attribution before publication. Use the repository's project identity rather than copying a machine's global personal Git identity.
- Read `docs/product-scope.md`, `docs/architecture.md`, and `docs/roadmap.md` before implementing features. Decision records identify accepted product decisions; proposed implementation details remain revisable.
- Follow `docs/implementation-process.md`, `docs/pilot-charter.md` and `docs/delivery-status.md`. ADRs 0004–0007 encode user-delegated adjudication; use them directly rather than reopening settled ordinary choices.
- Maintain repository documentation alongside code. Record consequential architecture changes in `docs/decisions/` and update affected contracts and milestone criteria.
- The selected backend is Java/Spring Boot with Bouncy Castle, PostgreSQL, and Redis. Choose and record supported dependency versions before creating a runnable scaffold.

## Implementation principles

- Use maintained cryptographic and ASN.1 libraries. FireCA owns policy, authorization, custody integration, and lifecycle logic.
- The selected core is FireCA-owned. Complete one bounded existing-engine comparison before freezing issuance contracts; a contrary material result needs a visible superseding ADR and one authoritative issuance ledger.
- Preserve tenant and authority authorization through APIs, background jobs, protocol gateways, exports, and signer operations.
- PostgreSQL is authoritative. Redis leases and notifications must not be the sole protection for issuance, rotation, durable jobs, or audit records.
- Reject stale ownership generations at protected admission and authoritative updates. Do not describe a Redis lease as sufficient for exclusive CA ownership.
- Start with one shared signer. Separate protected admission, repeated/uncertain provider execution, selected committed result and release. Generations increase within supported database history, not globally across restores.
- Uncertain signer/primary takeover requires old-process/provider exclusion. Restore with signing disabled; incomplete/unknown security history leaves affected issuers quarantined. Never turn a restored incomplete CRL into apparently fresh complete status.
- Store encrypted key payloads/metadata in PostgreSQL with separate signer wrapping material. Automatic known-safe restart unlock is default; manual unlock is an explicit profile. Keep independent recovery material and administrator/TLS bootstrap.
- Keep application private keys, CA keys, standalone keys, and general secrets distinct in policy and API permissions. CA keys must not be available through ordinary arbitrary-signing endpoints.
- Never commit live private keys, credentials, tokens, private certificate bundles, database dumps, or customer data. Any later cryptographic test fixtures must be explicitly synthetic and documented.
- Native Windows autoenrollment is an eventual required feature. An inventory agent does not satisfy that requirement.
- Pursue a domain-local XCEP/WSTEP experiment with real unattended Group Policy machine/user enrollment/renewal and intended service use. Missing lab access remains an explicit evidence gap, not a mocked pass or blanket block on unrelated work.
- Use DNS-01 first with tenant-specific namespace/validation context. Hosted private-only DNS requires a demonstrated scoped local validator. EAB alone does not authorize certificate names.
- Include one registered host/port/protocol/SNI TLS observer in the first lifecycle slice; issued, assigned and freshly observed certificates remain separate facts.
- Preserve disconnected self-hosting: avoid mandatory cloud callbacks, remotely loaded UI assets, or online license activation.
- Store licensing decisions in `docs/licensing.md`. Do not silently adopt a permissive or institutional-exemption license.
- Acquired self-hosted versions continue under paid entitlement after maintenance expiry. Preserve authenticated recovery/export/status operations on entitlement-file failure; do not invent institutional exemptions or claim pending terms are legal grants.

## Verification

- Run checks appropriate to the change and report what was actually verified.
- PKI implementation must demonstrate interoperability with independent clients, not merely parse its own output.
- Exercise authorization and concurrency failure cases when implementing issuance and custody.
- Use only synthetic identities and keys in examples and tests.
- Record experiments with tested commit, exact versions/topology, commands, actual result and limitations in `docs/evidence/<experiment-id>/README.md`; update delivery status. Accepted ADRs alone do not close findings.
- Before the first usable release, obtain implementation/evidence review from at least two fresh Sol High subagents at the same frozen baseline, then persist and adjudicate their reports. Repeat at later consequential gates when new trust/signing/recovery/protocol behavior warrants it. Design-only adjudication does not satisfy this implementation gate.
