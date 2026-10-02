# 0002: Trust and key custody

- Status: accepted
- Date: 2 October 2026

## Context

Customers need private CA hierarchies, certificates for services/users/devices, and inventory of certificates from any issuer. Convenient portal enrollment may coexist with clients that never give FireCA their private keys.

## Decision

Support many independent customer authorities/trust domains in shared infrastructure. Support customer-signed issuing authorities and offline roots. A provider-owned shared private CA offering is not part of the initial product.

Public-certificate functionality uses existing public CA providers and external inventory. FireCA will not seek its own public root-program admission.

Prefer client-generated keys and CSR enrollment. Also support browser-generated noncustodial enrollment, explicitly managed application keys, and later precisely defined temporary server generation.

Represent keys, certificate records, certificate purposes, and general secrets independently. Keep CA-key operation permissions distinct from application and standalone-key permissions. Introduce a provider boundary before integrating HSMs.

## Consequences

External public enrollment does not inherently require custody of the application's private key. Public-provider account/domain-validation credentials are separate from that key.

An imported certificate has inventory value even when FireCA cannot sign, renew, revoke, or export its private key. Every supported action must reflect the actual issuer integration and authorization.

Certificate issuance and deployment are independent lifecycle facts. Renewal creates a new certificate record and does not imply that the replacement is installed on assigned hosts.

CSR verification establishes key-related proof, not authorization for every requested identity or extension. Certificate profiles and principal/directory authorization remain authoritative.

## Required integration

ACME is part of the initial usable CA release. Native Windows autoenrollment is a required eventual feature; an endpoint inventory agent does not fulfill it. Validate the native route early with real clients before treating Windows compatibility as established.
