# Domain model and lifecycle

These resource names and relationships are proposed. They implement the accepted scope without coupling every key to a certificate or every tenant to one CA.

## Core resources

| Resource | Purpose and relationships |
| --- | --- |
| Tenant | Organization or individual account; administrative and data-access boundary |
| Principal | Human, integration, workload, or collector identity with tenant-scoped grants |
| TrustDomain | Intended trust boundary, trust anchors, and hierarchy policy; one tenant may have several |
| Authority | Logical managed CA, owned by a tenant/provider and related to a trust domain |
| IssuerVersion | Particular CA certificate, key version, chain, and operational state belonging to an authority |
| CertificateProfile | Versioned constraints for names, usages, algorithms, validity, extensions, and approvals |
| EnrollmentGrant | Which principal/tenant can enroll through which authority/profile and for which identifiers |
| EnrollmentRequest | Authorized request, CSR/public key, input/profile versions, idempotency identity, and outcome |
| CertificateRecord | Immutable issued or imported certificate and parsed metadata; optionally linked to a managed issuer and key |
| CertificateAsset | Stable managed purpose and renewal lineage, such as a service's production TLS identity |
| KeyObject / KeyVersion | Standalone or certificate-associated key identity; immutable version/provider references and custody policy |
| SecretObject / SecretVersion | General secret identity and encrypted value versions, independent of certificates |
| Host / Device / Workload | Managed system identity, ownership, integration identifiers, and labels |
| DeploymentAssignment | Intended association of an asset/certificate with a system, store, service, or endpoint |
| DeploymentObservation | Actual certificate seen at an endpoint/store and when/how it was observed |
| Job / Attempt | Durable work identity, execution attempts, ownership generation, and reconciled result |
| Alert / Delivery | Durable condition and separately retried notification deliveries |
| AuditEvent / OutboxEvent | Security-relevant history and durable events for downstream processing |

For imported certificates, preserve external issuer identity and chain data without requiring a corresponding managed FireCA authority. Inventory never requires possession of a private key.

## Relationships and identity

- A tenant has many trust domains, authorities, principals, certificate assets, keys, and secrets.
- An authority has many issuer versions over its lifetime; a rollover creates a new version.
- A certificate asset has many certificate records as it renews/reissues and many deployment assignments.
- A single certificate can be assigned to several systems. One system can have many certificates and stores/endpoints.
- A key version can be associated with several certificates; an enrollment may instead refer only to a client-owned public key.
- A deployment observation records reality at a particular time and can differ from the assigned certificate.
- Enrollment grants permit deliberate authority sharing without implying that every tenant may use every authority.

Use stable internal identifiers rather than names as resource identities. Certificate serial numbers are unique in the context of an issuer; subject names are not certificate identities. For inventory deduplication, use the certificate fingerprint within tenant scope while preserving distinct assets and deployments.

An endpoint must include enough context to observe the intended certificate: host/address, protocol, port, and optional TLS server name. A host name alone is insufficient for a server with several virtual services.

## Authorization contract

Authenticate the principal and resolve its tenant and permitted resources server-side. Carry tenant, authority, profile, and request identity through gateway, job, signer, export, and audit operations. A caller-provided tenant/authority identifier is not an authorization decision.

Authorize certificate identifiers and usages independently of verifying the CSR signature. Build certificate inputs from approved policy and trusted identity data; do not blindly copy requested SANs, CA constraints, or extensions.

For machine/user profiles, define which authoritative directory identity supplies names and which template permissions permit enrollment. Enrollment and renewal must each satisfy their applicable authorization policy.

Authorization/cache invalidation behavior must be defined before cached permissions or long-running jobs are enabled. A durable job does not bypass a later issuer suspension or key-operation restriction.

## Key custody modes

| Mode | FireCA retains the application private key? | Delivery |
| --- | --- | --- |
| Client CSR | No | Certificate and chain returned to the client |
| Browser-generated | No | Browser generates CSR and assembles its own key/certificate download |
| Managed server generation | Yes, encrypted and versioned | Certificate and policy-authorized key/bundle export |
| Temporary server generation | Temporarily, under explicit policy | Controlled delivery with defined retention and retry behavior |

CSR objects contain public-key information and a signature; they do not convey the subject's private key. [PKCS#10](https://www.rfc-editor.org/rfc/rfc2986.html)

Export formats are separate from custody. PKCS#8 represents key information; PKCS#12/PFX can package private keys and certificates. A CSR-only server cannot produce a bundle containing a private key it never received. [PKCS#8](https://www.rfc-editor.org/rfc/rfc5958.html), [PKCS#12](https://www.rfc-editor.org/rfc/rfc7292.html)

Browser generation needs explicit compatibility/format choices and can provide a convenient noncustodial flow. Temporary server generation needs a precise policy for failed downloads, expiry, logs, durable storage, and backups; do not describe it as guaranteed instantaneous erasure.

## Issuance and renewal

Proposed issuance workflow:

1. Authenticate, authorize identifiers/profile/issuer, and validate the CSR.
2. Persist the enrollment request, approved inputs, idempotency identity, and durable work.
3. Allocate the issuer/serial and claim the work using authoritative ownership checks.
4. Perform the approved signing/provider operation under its protected contract.
5. Persist the certificate/result and audit/outbox records before exposing a completed response.
6. Return the stored result; reconcile interrupted attempts using the durable request identity.

External issuance adds provider-specific pending authorization/order states. A timeout is not evidence that an external issuer failed to issue; reconcile before replacing its order.

Renewal/reissue creates a new immutable certificate record in the asset lineage. It may rotate the application key according to policy. Issuance completion does not update every deployment observation to the new certificate.

Imported certificates can enter the same inventory/asset model without going through issuance. Revocation, certificate expiry, and replacement/supersession are separate facts.

## Deployment health and alert conditions

| Condition | Meaning |
| --- | --- |
| Certificate expiring | A relevant certificate approaches its configured expiry threshold |
| Renewal overdue | Policy expects a replacement by now and none is available |
| Renewal failed | A renewal request/attempt has a recorded failure |
| Replacement not deployed | A replacement exists, but a fresh observation still sees the older certificate |
| Deployment observation stale | There is insufficient recent evidence of the deployed certificate |
| Revoked certificate observed | A fresh observation sees a certificate known to be revoked |

Persist issuance facts, assignments, observations, and alert state separately. "Unreachable" or "not recently observed" is not healthy deployment evidence. A collector must be authorized for the system and cannot rewrite unrelated tenant inventory.

## Lifecycle rules to specify before implementation

- Profile versioning and renewal behavior after policy changes.
- Issuer activation, suspension, rollover, retirement, and revocation publication.
- Key export, backup/restore, rotation, and destruction permissions.
- Credential bootstrap for API clients, ACME accounts, collectors, and native Windows identities.
- Observation freshness and alert thresholds/escalation per certificate purpose.
- Data/audit retention and tenant offboarding.
- Reconciliation for interrupted signing and external public orders.
