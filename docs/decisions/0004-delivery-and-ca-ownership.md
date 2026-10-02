# 0004: Initial outcome, CA ownership and delivery process

- Status: accepted
- Date: 2 October 2026
- Authorization: the user delegated adjudication and encoding of the two Sol High proposals.
- Findings: R1, R2 and R10; pilot-specific consequences of R3, R6 and R9.

## Context

The initial roadmap could deliver another issuer before its operational value, and treating cryptographic libraries as a complete CA engine would hide substantial responsibilities. The [resolution proposals](../reviews/2026-10-02-resolutions/README.md) recommend a bounded lifecycle outcome and an explicit ownership choice.

## Decision

The first engineering outcome is self-hosted internal TLS issuance, renewal and observed deployment under an existing customer-controlled root. This is an adoption hypothesis, not a selected customer or demonstrated market. Use two synthetic tenants and authorities to validate shared-service boundaries; introduce one noncritical service alongside incumbent PKI for a later pilot.

Build a narrow FireCA-owned CA core using maintained libraries. FireCA owns authorization, profiles, issuer/serial reservations, issuance and revocation history, reconciliation and lifecycle orchestration in PostgreSQL. Cryptographic primitives and ASN.1 construction remain library responsibilities.

Conduct one bounded EJBCA comparison before freezing core issuance contracts. Compare the same two-authority external-root/issuance/denial/revocation/rollover/recovery workflow. Inspect exact artifact, edition and distribution rights. This is a check on the selected ownership direction, rather than permission to build two products. A material disqualifying result requires an explicit superseding ADR; do not silently introduce a second issuance authority.

An issuance-engine adapter and a key-provider interface are different boundaries. If a future engine owns issuance facts, select one authoritative ledger for those facts and make inventory a projection.

Move one registered TLS endpoint observer into the first lifecycle release. Inventory import, assignments, certificate lineage, expiry/renewal alerts and issued-but-undeployed detection are part of that outcome. Broad scanning, automatic installation and store-specific agents follow later.

Keep API, basic local portal and ACME in the first usable CA release. Windows feasibility starts early in parallel; production native Windows autoenrollment remains mandatory. A Windows-first, HSM-required or stronger-isolation pilot must satisfy its own prerequisite earlier.

## Process and consequences

Use the [implementation process](../implementation-process.md), [pilot hypothesis](../pilot-charter.md), [roadmap](../roadmap.md) and [delivery status](../delivery-status.md). A reversible synthetic scaffold may support the engine and Windows experiments. Complete contracts and interoperability evidence precede unrestricted issuance and a commercial pilot.

Keep the initial build together with clear modules and separately deployable control-plane, signer and worker roles. Avoid creating a network service for every resource or primitive. Exact build/runtime versions and module interfaces are recorded before executable scaffolding.

The selected core carries CA-service engineering and support responsibility. Engine comparison, independent clients, failure drills and later independent implementation reviews provide evidence; AI authorship does not discharge those responsibilities. Customer interviews and laboratory access do not block unrelated synthetic engineering work.

## Validation remaining

Measure setup/coexistence effort, renewal operator effort, replacement-detection delay and recovery time. Qualify the actual buyer and custody requirements before calling the release commercially validated. No customer outreach or application experiment has occurred.
