# Implementation roadmap

The accepted first outcome is self-hosted internal TLS issuance/renewal with observed deployment. The order follows dependencies; native Windows is mandatory and has an early parallel real-client track.

No application milestone has been implemented yet. The documentation/repository baseline is the current completed artifact.

Follow the [implementation process](implementation-process.md), [pilot charter](pilot-charter.md) and [delivery/evidence status](delivery-status.md). ADRs 0004–0007 adjudicate the initial reviews. Their acceptance does not close experimental or customer-evidence findings.

## Feasibility checkpoints before the usable release

- F0: accepted decisions and process encoded in repository documentation.
- F1: supported runtime/build/module baseline and one bounded EJBCA comparison against the selected FireCA-owned CA responsibilities.
- F2: two-tenant synthetic CSR/API/ACME, revocation/rollover and observed TLS deployment slice with failure cases.
- F3: real native Windows background enrollment/renewal and intended-use experiment; secure the lab early.
- F4: disconnected operating/recovery, wrapping/entitlement/status-continuity drills for the supported profile.

F2/F4 incrementally exercise M1–M5. F3 runs independently: an unavailable Windows lab blocks its evidence and Windows compatibility claims, rather than unrelated CA engineering. Do not turn the synthetic slice into a second product or confuse a CLI-only experiment with M5.

## M0: Repository and design baseline

Record the accepted scope, stack, licensing policy, trust/custody model, proposed resource model, and milestone criteria.

Before scaffolding, select supported Java/Spring Boot/library versions, build tooling, development identity bootstrap, and initial module boundaries. Record the choices in decision records. Do not mistake the licensing policy for finalized license terms.

The first focus, ownership, trust/recovery, protocol/observer and commercial-continuity directions are recorded in ADRs 0004–0007. Exact versions and comparison evidence are F1 work.

## M1: Foundation contracts and early Windows track

Define the tenant/principal/authority/profile/request contracts, provider interfaces, and proposed ownership/idempotency protocol. Use a minimal synthetic issuer to investigate Windows interoperability; do not expose this prototype as a production CA.

The selected core is FireCA-owned over maintained libraries. Complete the one-candidate engine comparison before freezing issuance contracts; record exact artifact rights and authoritative responsibility. Signer, gateway and worker are distinct deployment roles, not one network service per primitive.

Acceptance criteria:

- Explicit tenant/resource authorization boundaries and permitted certificate-identifier rules.
- A privilege matrix stating trusted control-plane/database/platform roles and tenant boundaries.
- A design separating protected admission, provider execution, immutable inputs, durable selected result and release; no exactly-once provider promise.
- One active shared signer and explicit suspension/drain, uncertain-takeover exclusion and history-recovery contracts.
- Independent administrator/TLS bootstrap and wrapping/recovery ownership.
- F3 tracked separately: domain-local XCEP/WSTEP, real unattended machine/user enrollment and renewal, Group Policy, denial/rename/disable/outage and intended use; no mandatory PC agent.

A suitable Windows/Active Directory test environment is required for the interoperability evidence. Its availability has not been verified. This dependency must be visible rather than replaced by mocked success.

## M2: Software custody provider

Implement key generation/import, version/provider handles, encrypted durable storage, usage/export policy, unlock, and recovery. Keep CA-key permissions separate from ordinary key operations.

Acceptance criteria:

- Key operations enforce tenant, key version, allowed usage, and export policy.
- CA private keys do not pass through normal control-plane responses or general signing/export APIs.
- Backup/restore preserves required encryption metadata and provider references.
- Failed/unavailable custody fails closed for protected signing.
- Rotation and recovery behavior is demonstrated with synthetic material.
- Encrypted payloads and metadata are restored together in PostgreSQL; signer wrapping material is independent.
- Known-safe automatic restart unlock and optional manual unlock have documented availability implications.
- Fresh offline administrator/TLS/custody rebuild, lost recovery copy and interrupted wrapping rotation work.
- Retained backups and retired-issuer status keys have a supported recovery path.

## M3: First complete private CA workflow

Create/manage an issuing authority, support an external/offline-root signing workflow, validate an authorized CSR, issue a certificate, return the chain, and record the durable/audited result.

Acceptance criteria:

- At least two tenant authorities demonstrate isolated enrollment, inventory, and key access.
- Server and client TLS profiles constrain SANs, usages, validity, and extensions.
- Independently implemented TLS/X.509 clients accept the intended certificates and chains.
- CSR signature validation does not substitute for identifier authorization.
- Repeated requests and interrupted attempts have documented, verified outcomes.
- Lease expiry/stale-worker behavior cannot bypass the protected signing contract.
- Complete CRL publication and the selected relying-client revocation behavior are independently demonstrated.
- Stable publication names, CRL numbering/freshness and selected TLS/mTLS client behavior are recorded before pilot certificates.
- Wrong-tenant identity, revoked/stale/unavailable status and old/new issuer coexistence are independently tested.
- Pause/partition/ambiguous-commit/sign-success-before-persistence and suspension tests distinguish provider execution from selected result/release.
- Pre-revocation restore stays quarantined until complete history is reconciled; old-signer exclusion and the distrust fallback are demonstrated.

Safe manual recovery, rollover and publication continuity are core release requirements. Automatic HA and additional provider profiles remain later work.

## M4: Inventory and lifecycle

Support external certificate import, stable certificate assets, key/certificate relationships, host/device/service assignments, renewal lineage, expiry evaluation, durable alerts and one registered TLS endpoint observer.

Acceptance criteria:

- External certificates can be tracked without private-key possession or a managed FireCA issuer.
- Many-to-many certificate/system relationships and endpoint context are represented.
- Expiry and overdue/failed renewal conditions produce durable, deduplicated alert state.
- A replacement being issued does not mark its deployments complete.
- Freshness/unknown observation state remains visible.
- Redis restart/lost wakeups do not lose accepted jobs or alert state.
- Host/address, protocol, port and SNI select the actual endpoint; observers cannot submit another tenant's targets/results.
- Renew a certificate, leave it undeployed and demonstrate the distinct alert; install it and confirm the fresh observation.
- Interrupt endpoint/observer access and expose unknown/stale evidence rather than a healthy state.

Use ADR 0006's experimental poll/freshness defaults, then measure actual behavior before pilot guarantees. Broad scanning, Windows store inventory and automatic installation follow later.

## M5: Initial usable CA release

Deliver the documented HTTP API, a basic locally served portal, and an ACME server. Add browser-generated enrollment/download and explicitly custodial flows in dependency order.

Acceptance criteria:

- Independent ACME clients can enroll, renew, download chains, and use supported revocation paths.
- ACME accounts are mapped to the correct tenant/authority/profile and identifier authorization policy.
- API/portal CSR enrollment and certificate downloads work end to end.
- Browser-generated keys remain client-side; custodial exports enforce their own permissions.
- Initial offline deployment has no mandatory external asset, identity, telemetry, or licensing callback.
- The supported setup and limitations are documented with executable deployment instructions.
- DNS-01 validation uses tenant/authority/account/namespace/context bindings and final CSR/order agreement.
- Independent clients exercise overlapping DNS views, stale/replayed challenges and resolver/connector substitution.
- Network-denied installation, restart, upgrade/rebuild, wrapping rotation and incomplete-history restore/quarantine pass for the supported profile.
- Entitled acquired versions continue after maintenance expiry; missing/invalid-file recovery and offline verification-key transition are demonstrated.
- Two fresh independent Sol High implementation/evidence reviews are recorded and adjudicated before declaring this release ready.

ACME uses established protocol behavior; it must not be replaced by a proprietary endpoint with an ACME label. [RFC 8555](https://www.rfc-editor.org/rfc/rfc8555.html)

Self-hosted local DNS is first. Initial hosting may validate publicly reachable challenge records; hosted private-only DNS is a separate scoped-validator launch gate. Browser/custodial flows retain the declared key-location/export policy and their independent evidence.

An organizational pilot also requires a named buyer/operator, baseline/value criteria, explicit paid/evaluation rights, accepted custody/trust and its actual Windows/HSM/isolation prerequisites. Engineering readiness alone does not demonstrate commercial validation or grant deployment rights.

## M6: Enterprise Windows enrollment and broader observation

Complete native machine/user enrollment integration, directory-derived identities and template policies, renewal, and the relevant certificate profiles. Add collectors and authenticated observation ingestion for endpoint/stores inventory.

The early F3 experiment precedes Windows contract freeze; M6 turns the demonstrated path into a supported production matrix. One TLS observer is already required at M4/M5. A Windows-first pilot brings relevant M6 acceptance ahead of that pilot.

Acceptance criteria:

- Supported Windows/domain configurations are recorded and tested against real clients.
- Native policy lookup, enrollment, renewal, authorization denial, and operational recovery are demonstrated.
- Directory/template permissions constrain enrollment; caller-supplied identity fields cannot override them.
- Optional agents improve inventory without becoming a requirement for native enrollment.
- A renewed certificate left undeployed produces a distinct condition from failed issuance.
- Ordinary background Group Policy enrollment/renewal, reboot/logon and actual relying-service use are demonstrated.
- Permission removal, disable/rename, gateway outage and recovery have documented effects.
- Relevant domain certificate authentication uses current strong mapping; DCOM/cross-forest/key-archival claims require additional evidence.

## M7: Public providers, standalone keys, and general secrets

Add external public CA enrollment/renewal connectors, richer collectors, deployment integrations, standalone key lifecycle and authorized cryptographic operations, and general secret storage.

Acceptance criteria:

- External CSR issuance works without FireCA possessing the application's private key.
- Provider/account/domain-validation credentials have separate custody and scopes.
- External order reconciliation prevents blind duplicate orders after ambiguous failures.
- Standalone keys and secrets work without requiring a certificate.
- Generic key operations cannot select CA keys or bypass certificate-profile policy.

## M8: HSMs and operational isolation

Implement selected HSM/provider integrations, dedicated customer/authority signing roles where justified, higher-scale operation, and hosted commercial management.

Acceptance criteria:

- Provider capabilities and non-exportable key behavior are documented and tested.
- Existing software-provider flows remain compatible where their capabilities overlap.
- Authority rollover, revocation freshness, backup/restore, signer allocation, and failover are verified for the deployment profile.
- Published throughput/latency statements use measurements of the actual deployment.
- Commercial self-hosting supports disconnected license verification.

M8 extends core recovery/status/licensing evidence to new providers and higher-scale/isolation profiles. It does not postpone the M2–M5 operating foundation. Bring a specific HSM/isolation provider forward when the actual pilot requires it.

## Outstanding choices

These are design/implementation choices, not missing agreement on the product scope:

- Java/Spring Boot versions, Maven or Gradle, API/schema tooling, and test/client matrix.
- Exact crypto/provider implementations and recovery tooling within ADR 0005's chosen PostgreSQL payload/wrapping/unlock model.
- Exact protected admission, claim and release mechanisms; tests of pauses and external side effects.
- Supported Windows builds/authentication/template matrix within the selected domain-local XCEP/WSTEP experiment.
- Profile algorithms and production validity/freshness/client guarantees measured against ADR 0006's synthetic defaults.
- Hosted private-DNS validator and broader observation transport/store/discovery integrations.
- Formal personal, organizational, evaluation, contribution and hosting/resale terms implementing ADR 0007's accepted continuity policy; prices and license metrics.
