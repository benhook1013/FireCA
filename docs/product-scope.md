# Product scope

## Accepted direction

FireCA is an API-first, multitenant crypto services product. Private CA operations and certificate inventory/lifecycle management are the primary focus. Standalone key management and general secret storage extend the same product over time.

The official hosted service and self-hosted installations use the same product. A single deployment can serve many tenants and many authorities; separate customer pods are an optional later isolation tier.

## Customers and environments

- Organizations securing internal services, corporate devices, users, and application workloads.
- Government and other customers operating restricted or disconnected networks.
- Individuals running personal private PKI under the intended personal-use license.
- Integrations requesting and managing certificates programmatically.

All organizational deployments are intended to require a paid license, including government, education, charities, and public-interest organizations. See [licensing](licensing.md).

The accepted first engineering focus is self-hosted internal TLS issuance/renewal with observed deployment under an existing customer root. The [pilot charter](pilot-charter.md) is a commercial hypothesis, not a selected customer. Windows/HSM/isolation requirements move ahead of any pilot that depends on them.

## Feature commitments

| Area | Intended capability | Delivery position |
| --- | --- | --- |
| Private authorities | Create/manage independent CA hierarchies and bring customer-signed issuing authorities | Core |
| Multitenancy | Tenant-scoped administration, identities, permissions, policies, inventory, and keys | Foundation |
| Certificate issuance | Authorized CSRs, certificate profiles, chains, renewal/reissue, and revocation | Core |
| Certificate uses | Internal server TLS, client/mutual TLS, user, computer, and device identities | Progressive profiles and integrations |
| HTTP API | Versioned API and machine identities; documented request and lifecycle behavior | Core |
| Web portal | CSR submission, enrollment, inventory, downloads, and administration | Initial usable CA release |
| ACME server | Standard enrollment and renewal against FireCA private issuers | Initial usable CA release |
| Native Windows autoenrollment | Native clients, policy/templates, enrollment and renewal; no mandatory endpoint agent | Required eventual feature; early feasibility prototype |
| Inventory | FireCA-issued, imported, and discovered certificates without requiring their private keys | Core |
| Assignments and observations | Relate certificates to hosts/services/devices and record what is actually deployed | One registered TLS observer in the first lifecycle release; stores/discovery expand later |
| Alerts | Expiry, renewal failures, missing replacement deployment, and stale observations | Core lifecycle, expanding with collectors |
| Key custody | Client-owned CSR keys, browser-generated keys, managed software keys, and explicit export policy | CSR first; other modes phased |
| Standalone keys | Generate/import/store/version/export keys independently of certificates; controlled operations later | Progressive |
| General secrets | Tenant-scoped encrypted storage, versions, access controls, and audit | Later product expansion |
| Public certificates | Track external certificates and obtain/manage them through external public CA connectors | Tracking first; connectors later |
| HSM providers | Non-exportable key handles and supported signing operations | Provider boundary early; integration later |
| SDKs and deployment connectors | Improve automation over the documented API | Later |

"Initial usable CA release" includes the HTTP API, a basic portal, and an ACME server. This is distinct from the later enterprise milestone that completes native Windows enrollment.

DNS-01 is the first ACME challenge. Self-hosted local DNS works in the configured network context; initial hosted support may use publicly reachable challenge records. Hosted private-only DNS is supported only after its scoped tenant-local validator is demonstrated.

## Trust and public certificates

FireCA will not pursue its own publicly trusted root or operate a new browser-trusted public CA. Customers can use FireCA to manage certificates obtained from existing publicly trusted providers.

Public accessibility of the API does not imply public trust of the issued certificates. Private clients need the intended trust anchors. Separate tenants can share infrastructure while retaining independent trust domains.

A provider-owned shared private CA offering is omitted from the initial product. The authority access model should accommodate explicit enrollment grants if a later use case justifies shared trust.

External public issuance can accept a client-generated CSR. FireCA need not possess the certificate private key. Provider account credentials and domain-validation credentials are separate resources. External provider support and authorization requirements must be evaluated per connector.

## Deployment requirements

- Containerized API, signing, and worker roles that can serve many tenants.
- Kubernetes as the intended primary deployment target; exact packaging remains to be selected.
- Core private issuance, inventory, local integrations, and administration can operate without internet access.
- Ship UI assets locally; support local identity providers and locally verifiable paid licenses.
- Declare egress requirements for public issuers, external notifications, remote identity providers, and trust-bundle updates.
- Support offline roots and locally reachable revocation services without assuming internet access.

## Product boundaries

FireCA implements product policy and orchestration using maintained crypto and ASN.1 libraries. It does not implement new cryptographic primitives.

The selected direction is a narrow FireCA-owned CA core, checked against one existing-engine workflow before issuance contracts freeze. The initial shared software tier trusts privileged platform/database operators and control-plane authorization. One shared signer can serve many tenants; uncertain takeover and history recovery are manual safety operations.

An inventory agent may observe certificate stores or deployments. It does not replace the native Windows enrollment requirement. Discovery does not imply authority to issue for every discovered name.

SCEP, EST, SSH certificates, code signing, S/MIME, timestamping, and additional algorithms/protocols have not been committed as initial features. Evaluate them against real integrations after the core PKI lifecycle is established.
