# Implementation roadmap

The order follows dependencies rather than exposing raw cryptographic functions as the first public product. Native Windows enrollment is a mandatory eventual feature and gets an early feasibility prototype.

No application milestone has been implemented yet. The documentation/repository baseline is the current completed artifact.

## M0: Repository and design baseline

Record the accepted scope, stack, licensing policy, trust/custody model, proposed resource model, and milestone criteria.

Before scaffolding, select supported Java/Spring Boot/library versions, build tooling, development identity bootstrap, and initial module boundaries. Record the choices in decision records. Do not mistake the licensing policy for finalized license terms.

## M1: Foundation contracts and Windows feasibility

Define the tenant/principal/authority/profile/request contracts, provider interfaces, and proposed ownership/idempotency protocol. Use a minimal synthetic issuer to investigate Windows interoperability; do not expose this prototype as a production CA.

Acceptance criteria:

- Explicit tenant/resource authorization boundaries and permitted certificate-identifier rules.
- A design for PostgreSQL claims, issuer/serial uniqueness, interrupted-request reconciliation, and stale-worker rejection at the signer.
- A real native Windows enrollment prototype with policy discovery, enrollment, and renewal demonstrated through the selected path.
- Document actual Group Policy, authentication, template, directory, and connector requirements.
- Native-client success does not depend on a FireCA endpoint agent.

A suitable Windows/Active Directory test environment is required for the interoperability evidence. Its availability has not been verified. This dependency must be visible rather than replaced by mocked success.

## M2: Software custody provider

Implement key generation/import, version/provider handles, encrypted durable storage, usage/export policy, unlock, and recovery. Keep CA-key permissions separate from ordinary key operations.

Acceptance criteria:

- Key operations enforce tenant, key version, allowed usage, and export policy.
- CA private keys do not pass through normal control-plane responses or general signing/export APIs.
- Backup/restore preserves required encryption metadata and provider references.
- Failed/unavailable custody fails closed for protected signing.
- Rotation and recovery behavior is demonstrated with synthetic material.

## M3: First complete private CA workflow

Create/manage an issuing authority, support an external/offline-root signing workflow, validate an authorized CSR, issue a certificate, return the chain, and record the durable/audited result.

Acceptance criteria:

- At least two tenant authorities demonstrate isolated enrollment, inventory, and key access.
- Server and client TLS profiles constrain SANs, usages, validity, and extensions.
- Independently implemented TLS/X.509 clients accept the intended certificates and chains.
- CSR signature validation does not substitute for identifier authorization.
- Repeated requests and interrupted attempts have documented, verified outcomes.
- Lease expiry/stale-worker behavior cannot bypass the protected signing contract.
- Revocation records and CRL publication demonstrate relying-client behavior where supported.

## M4: Inventory and lifecycle

Support external certificate import, stable certificate assets, key/certificate relationships, host/device/service assignments, renewal lineage, expiry evaluation, and durable alerts.

Acceptance criteria:

- External certificates can be tracked without private-key possession or a managed FireCA issuer.
- Many-to-many certificate/system relationships and endpoint context are represented.
- Expiry and overdue/failed renewal conditions produce durable, deduplicated alert state.
- A replacement being issued does not mark its deployments complete.
- Freshness/unknown observation state remains visible.
- Redis restart/lost wakeups do not lose accepted jobs or alert state.

## M5: Initial usable CA release

Deliver the documented HTTP API, a basic locally served portal, and an ACME server. Add browser-generated enrollment/download and explicitly custodial flows in dependency order.

Acceptance criteria:

- Independent ACME clients can enroll, renew, download chains, and use supported revocation paths.
- ACME accounts are mapped to the correct tenant/authority/profile and identifier authorization policy.
- API/portal CSR enrollment and certificate downloads work end to end.
- Browser-generated keys remain client-side; custodial exports enforce their own permissions.
- Initial offline deployment has no mandatory external asset, identity, telemetry, or licensing callback.
- The supported setup and limitations are documented with executable deployment instructions.

ACME uses established protocol behavior; it must not be replaced by a proprietary endpoint with an ACME label. [RFC 8555](https://www.rfc-editor.org/rfc/rfc8555.html)

## M6: Enterprise Windows enrollment and deployment observation

Complete native machine/user enrollment integration, directory-derived identities and template policies, renewal, and the relevant certificate profiles. Add collectors and authenticated observation ingestion for endpoint/stores inventory.

Acceptance criteria:

- Supported Windows/domain configurations are recorded and tested against real clients.
- Native policy lookup, enrollment, renewal, authorization denial, and operational recovery are demonstrated.
- Directory/template permissions constrain enrollment; caller-supplied identity fields cannot override them.
- Optional agents improve inventory without becoming a requirement for native enrollment.
- A renewed certificate left undeployed produces a distinct condition from failed issuance.

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

## Outstanding choices

These are design/implementation choices, not missing agreement on the product scope:

- Java/Spring Boot versions, Maven or Gradle, API/schema tooling, and test/client matrix.
- Durable encrypted payload storage, wrapping-key hierarchy, unlock and recovery flow.
- Exact signer fencing and interrupted-issuance protocol.
- Native Windows path, supported versions, domain authentication, and connector placement.
- Profile algorithms, initial validity policies, CRL/OCSP publication scope, and trust-bundle distribution.
- Initial discovery targets and observation transport.
- Formal personal-use, organizational-use, hosting/resale license terms and operational expiry behavior.
