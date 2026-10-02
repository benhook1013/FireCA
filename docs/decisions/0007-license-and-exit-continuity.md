# 0007: Self-hosted entitlement and hosted exit continuity

- Status: accepted business and product policy; formal terms pending
- Date: 2 October 2026
- Findings: R9 and publication/recovery consequences of R4, R5 and R8.

## Decision

Paid self-hosted organizational entitlement permits continued operation of acquired software versions within its licensed scope. Updates, support, maintenance and expansion of licensed capacity have separately defined commercial terms. Maintenance expiry does not stop entitled-version issuance, renewal, revocation, recovery or exports.

Locally verifiable signed license files identify deployment/use rights, entitled releases or version ranges and any licensed limits separately from maintenance eligibility. Core continued-use rights must not depend on current wall-clock maintenance expiry or cloud activation. A restore of an entitled version must remain operable offline.

This is a paid-version business model, not a free organizational license. All businesses, government, education, charities, NGOs and other organizations remain paid categories. Free personal noncommercial use stays limited to individuals acting for themselves. Service-provider hosting, resale and embedding require distinct rights.

Formal personal, organizational and evaluation terms must be published or executed before their grants are represented as available. Organizational evaluation is paid by default under an explicit agreement. Any separately approved promotional waiver is a commercial exception, never a category-wide exemption. Evaluation-only deployments use synthetic/nonproduction material; production certificates require a continuity entitlement.

## Product behavior

| Condition | Required behavior |
| --- | --- |
| Valid entitlement; maintenance expired | Continue entitled-version core operations. Show maintenance status; do not authorize unentitled upgrades or capacity expansion. |
| Missing/invalid entitlement on a new installation | Do not activate production issuers; report a clear entitlement error. |
| Missing/invalid entitlement during recovery | Preserve keys and history. Permit authenticated incident administration, backup/recovery, authorized exports and necessary status operations for existing issuers; restore legitimate entitlement before new leaf issuance. |
| Clock anomaly | Report it; do not revoke perpetual acquired-version operation because a maintenance clock changed. Certificate validity/signing clock requirements remain separate. |
| Verification-key change | Ship verifiable transition material with offline releases and preserve verification of existing entitled-version licenses. |
| Unentitled upgrade | Refuse activation of that release through a documented check; provide a supported entitled-version recovery path without rolling CA history back. |

Recovery/export permissions remain authorization-controlled. Ordinary CA-key download is not introduced. Non-exportable HSM material cannot be promised as a downloadable exit artifact.

Entitlement-recovery allowances do not override tenant authorization, custody lock or incomplete-history quarantine. Status operations require complete authoritative status history even when incident administration remains available.

## Hosted exit default

Use a 90-day notified transition period for ordinary subscription cancellation, with bounded renewal of existing assets, migration/export assistance and cessation of new customer/authority expansion. Stop leaf issuance at the transition deadline. Fund and retain revocation/status publication through the latest issued certificate expiry plus the recorded retention/cache margin, including certificates renewed during the transition.

Record validity bounds and publication responsibility in the agreement before hosted production issuance. Data export includes certificates, inventory/lineage, profiles, assignments, observations, audit and status artifacts. Protected software-key transfer or replacement issuance depends on custody policy. Establish a customer-controlled publication/transfer contingency rather than promising indefinite vendor availability.

Any different commercial exit arrangement needs an explicit recorded exception and continuity assessment before that deployment issues certificates.

## Consequences and validation

Continued acquired-version use reduces runtime subscription leverage and improves procurement/recovery continuity. Revenue comes from paid entitlement, updates/support, capacity and separately licensed services. Prices and license metrics remain commercial implementation work.

This ADR records intent and required product behavior; it is not an operative license grant. The current reservation notice remains in force until formal terms are adopted.

Demonstrate network-denied installation, restart, upgrade, supported rollback/rebuild and restore; maintenance expiry; missing/invalid-file recovery; clock anomalies; offline verification-key transition; exports; and issuer status retention after exit. Do not use restoring an older database after later issuance as a software rollback shortcut.
