# Independent resolution proposal B — FireCA

**Privacy editing note:** Personal/local context is omitted. Baseline IDs and evidence links refer to privacy-sanitized equivalents of the original reviewed commits; technical findings are retained.

Provenance: GPT-6.1-Sol, high reasoning effort; 2 October 2026. Reviewed clean snapshot `3daaa89becd0bc66279215930e73e4e56e19f500`, including AGENTS.md, the consolidated R1–R10 findings, both original reviews, scope, architecture, domain model, roadmap, licensing, references, and all accepted ADRs. I did not consult the other resolution reviewer. This was read-only work; no implementation or interoperability tests were run.

**Recommendation:** proceed with a bounded feasibility phase centered on one observed TLS certificate lifecycle. Make the initial operating and trust boundaries explicit, provisionally favor a FireCA-owned CA core, and use experiments to confirm the engine choice and native Windows route before expanding implementation.

These are proposed defaults for discussion, not accepted changes. They preserve the selected Java/Spring Boot/Bouncy Castle stack, PostgreSQL authority, temporary Redis coordination, shared services, initial-release ACME, eventual mandatory native Windows autoenrollment, disconnected self-hosting, custody choices, eventual keys/secrets, and paid organizational licensing.

**Coverage and decisions**

| Finding | Proposed resolution | Decide now or experiment first | Acceptance evidence and dependency |
| --- | --- | --- | --- |
| R1: First customer/release | Adopt a provisional self-hosted internal TLS lifecycle pilot hypothesis; qualify a real buyer against it | Hypothesis now; buyer/value validation before commercial pilot | Buyer-backed charter, current-process baseline, setup/coexistence cost, measurable operational improvement |
| R2: Own/adapt CA engine | Provisionally favor custom core over maintained libraries; run one bounded comparison with a Java engine before locking contracts | Ownership hypothesis now; final engine decision after experiment | Common two-authority workflow, responsibility map, dependency/edition rights, implementation and operating burden |
| R3: Signer approval authority | Declare control plane, database administration, signer administration, and platform operators inside the initial trusted boundary | Decide now | Tenant-denial tests throughout execution; privilege matrix; explicit statement that privileged compromise can authorize signing |
| R4: Fencing/restore | One active shared software signer initially, controlled admission, durable results, manual exclusion before takeover; quarantine incomplete restored CA history | Guarantee now; fault experiments before production signing | Pause/partition/crash/restore matrix; one released certificate outcome; old-process exclusion evidence |
| R5: Bootstrap/custody | PostgreSQL encrypted payloads, versioned wrapping hierarchy, independent recovery copies; human unlock default for restricted self-hosting | Operating model now; recovery experiment before custody pilot | Fresh offline rebuild, administrator/TLS bootstrap, unlock, interrupted rotation, retired-issuer CRL signing |
| R6: Native Windows | Investigate domain-local XCEP/WSTEP connector with real Group Policy autoenrollment and directory authentication | Lab/topology now; compatibility conclusions after experiment | Unattended machine/user enrollment and renewal, denial cases, interruption recovery, actual relying-service use |
| R7: Hosted private ACME | DNS-01 first; tenant-selected validation network context; private hosted validation through a scoped outbound connector | Challenge/topology now; routing experiment before hosted support | Independent clients, split DNS, overlapping names, replay/routing/namespace denial tests |
| R8: Trust/revocation | One supported relying-service configuration, stable customer-controlled publication names, issuer-aware identity mapping, CRLs first | Contract now; client behavior before pilot certificates | Wrong-tenant rejection, revoked/stale/unreachable-status cases, rollover and retirement drill |
| R9: Offline licensing | Prefer perpetual operation of entitled self-hosted versions; maintenance/update entitlement can expire; explicit hosted exit policy | Continuity principle now; formal terms before organizational pilot | Written entitlement plus offline install/upgrade/rebuild/expiry/export tests |
| R10: Deployment health | Bring one scoped TLS endpoint observer into the lifecycle release | Decide now; demonstrate with vertical slice | Replacement issued but undeployed alert, correct-SNI observation, stale/unknown state, later deployment confirmation |

**R1 — Pick an adoption hypothesis without inventing customer validation**

I recommend starting with this hypothesis:

> A platform or infrastructure team operating internal TLS services wants API-driven issuance and renewal, inventory of existing certificates, and reliable evidence that replacements reached their endpoints. It can self-host FireCA, retain its existing root, and initially accept software custody and the declared shared-service trust boundary.

This is narrow enough to test and connects issuance to lifecycle value. Restricted-network and government requirements are target-market considerations, without evidence that a particular buyer has selected FireCA.

The first pilot would introduce a customer-signed FireCA issuing authority alongside existing infrastructure. Import existing certificate inventory and move one noncritical service first. Do not require wholesale CA migration or an immediate AD CS replacement.

Record a short charter containing the buyer and operator, current process, certificate purposes, deployment environment, custody requirements, required integrations, procurement/evaluation entitlement, coexistence procedure, and measurable outcome. Useful measures include operator time per renewal, time to detect an undeployed replacement, setup effort, and recovery time. Obtain measurements from the existing process rather than choosing impressive improvement percentages in advance.

A Windows-centric buyer is a valid alternative, but its pilot gate must include complete native Windows evidence earlier. An HSM-required buyer similarly changes provider sequencing. Neither requirement should be assumed merely because the buyer is governmental.

**Acceptance:** a real prospective operator accepts the stated deployment/trust limitations and agrees that the demonstrated workflow addresses a relevant problem. If no buyer is selected, the same slice remains an engineering feasibility release; it must not be described as a validated commercial release.

**R2 — Make custom-core ownership deliberate, and keep the comparison small**

My provisional preference is **a FireCA-owned CA core built using maintained cryptographic and ASN.1 libraries**. The intended tenant policy, lifecycle model, offline delivery, commercial model, and early provider boundary make that a defensible direction. It also matches the user’s leaning. That is a preference to test, not proof that custom development is cheaper.

Bouncy Castle supplies building blocks, not the transaction, authorization, revocation, recovery, and operating model of a complete CA service. Its published license permits broad use subject to its conditions; selected artifacts and transitive dependencies still need an inventory. [Bouncy Castle license](https://www.bouncycastle.org/about/license/)

Compare custom ownership against one realistic Java-engine alternative, such as EJBCA, using the same synthetic workflow:

- Two authorities served in one installation.
- An externally signed issuing-authority CSR.
- Authorized leaf issuance and a rejected unauthorized request.
- Revocation and publication.
- Issuer rollover with continuing old-issuer status service.
- Interrupted issuance and database recovery behavior.

Timebox the comparison to roughly one working week, with an explicit option to extend only for a decisive unresolved issue. Run the operations the candidate already supports and inspect the remaining contracts. Do not build two complete implementations to discover which is cheaper.

The comparison should answer who owns issuance facts, approvals, profiles, serials, keys, revocations, recovery, and protocol enrollment. Distinguish an **issuance-engine adapter** from a **key-provider interface**: an existing CA engine is not interchangeable with an HSM signing provider.

If an adopted engine owns its issuance ledger in PostgreSQL, designate that ledger authoritative for those facts. FireCA inventory projections cannot become a second competing issuance authority. This preserves PostgreSQL authority while clarifying subsystem ownership.

EJBCA’s current Community README recommends Enterprise for production and describes CE as LGPL-licensed and intended for learning/testing/prototyping. That is a vendor positioning/support statement; it should not be converted into a claim that the license legally prohibits production. Evaluate actual release terms, required features, and deployment/redistribution rights separately. [EJBCA Community README](https://github.com/Keyfactor/ejbca-ce), [published license](https://github.com/Keyfactor/ejbca-ce/blob/main/LICENSE)

**Decision rule:** retain custom-core ownership if the bounded slice is feasible and adaptation introduces substantial duplicate state, unsuitable edition dependencies, or contractual restrictions. Prefer adaptation if it demonstrably removes a large supported responsibility without undermining the fixed product requirements.

**R3 — Accept a clear initial trust boundary instead of implying independent signer approval**

Declare the initial shared deployment to trust:

- The control plane to establish enrollment authorization and policy.
- The signer to enforce structured CA operations and key-use restrictions.
- Database and platform administrators not to maliciously rewrite authorization or extract/invoke software-held keys.
- The deployment operator to control bootstrap, recovery, and wrapping material.

Within this boundary, the signer should independently check database-backed bindings and invariant rules: tenant, authority, issuer/key version, allowed operation, approved immutable inputs, request identity, applicable profile, operational state, and ownership generation. Ordinary workers must never submit arbitrary bytes for a CA key to sign.

However, if a malicious control plane or database administrator can manufacture the authoritative approval or alter the policy it relies on, these checks do not establish an independent approval authority. Process separation reduces accidental exposure and some attack paths; it does not make the signer immune to all trusted-component compromise.

Write a privilege matrix for enrollment, grant/profile changes, issuer activation/suspension, authority import, recovery, key export, and CA-key invocation. Record consequential policy changes as immutable versions rather than silently rewriting prior request policy.

Use PostgreSQL audit/outbox records for normal operations and export audit evidence to a separately administered customer destination where useful. An audit copy administered by the same attacker is not proof against that attacker.

**Acceptance:** synthetic tenant A cannot issue, retrieve, export, revoke, observe, or invoke tenant B’s resources through APIs, gateways, queued jobs, retries, or substituted provider handles. Test forged tenant context and tampered records that violate preserved bindings. Separately document that an actor controlling all authoritative approval fields can authorize signing under the accepted model.

If a pilot requires survival of control-plane compromise, bring forward an independently administered approval/policy boundary. Signing an approval token using a key controlled by that same control plane would not supply it. HSM non-exportability alone would not supply it either.

**R4 — Promise one durable/released issuance outcome, and treat takeover as an operating action**

Use one active shared software signer initially. It can serve many tenants and authorities; this does not require tenant-specific pods. Separate scheduling from custody: workers request protected operations, while only the signer holds software CA keys.

For the initial short software-signing path, investigate per-authority serialization and PostgreSQL locking that covers admission, canonical input selection, signing, and result persistence. All relevant ownership and suspension transitions must follow the same protected path. Avoid broad distributed signer takeover while establishing this contract.

The proposed guarantee has three distinct parts:

1. **Provider execution:** an authorized operation may execute more than once after an ambiguous failure. An operation already admitted may still execute after a partition or suspension request. Do not promise exactly-once signing.
2. **Durable issuance:** a tenant-scoped idempotency identity binds one issuance decision to one reserved issuer/serial and canonical certificate inputs.
3. **Result release:** clients receive only the committed certificate result. Replays return that stored result. A losing/stale attempt cannot release its own alternate result.

Persist the complete canonical to-be-signed inputs, including serial, validity, extensions, and profile/issuer versions, before signing. Reconciliation must not quietly change those inputs. Randomized signing can produce different encoded signatures for the same approved inputs; that is distinct from issuing a second semantic certificate decision.

Define suspension as two observable states:

- **Requested/admission blocked:** prevent newly admitted enrollment operations.
- **Effective/drained:** the signer has acknowledged that relevant in-flight operations are finished or excluded.

Do not acknowledge a stronger “nothing can still execute” state merely because a database flag changed. An emergency operation can block release while outstanding provider execution remains uncertain.

Before replacement signer activation or restoration, prove old primary/signer exclusion. Rotating a wrapping key or database credential does not remove a key already present in an old process’s memory. Pod deletion alone is insufficient evidence during a node partition; Kubernetes specifically warns that force deletion can leave an old process running. [Kubernetes force deletion guidance](https://kubernetes.io/docs/tasks/run-application/force-delete-stateful-set-pod/)

PostgreSQL also requires exclusion of an old primary after failover. Asynchronous replication can lose committed transactions, so automatic promotion must not be treated as preserving complete issuance history. [PostgreSQL failover](https://www.postgresql.org/docs/current/warm-standby-failover.html), [replication durability](https://www.postgresql.org/docs/current/warm-standby.html#SYNCHRONOUS-REPLICATION)

For the first deployment, prefer manual recovery and availability loss over uncertain signer coexistence. Define which failures preserve acknowledged issuance/revocation records and which require recovery mode. If a buyer needs continuity through database/storage loss, provision and test the corresponding synchronous durability arrangement before that pilot.

A restored database with incomplete issuance or revocation history must quarantine affected issuer operations. Restore complete WAL/history where available. Otherwise, recover missing authoritative history or execute a documented issuer distrust/replacement procedure. Do not publish a newly “clean” CRL from an older backup.

A new ownership counter, random serial, recovery UUID, or replacement issuer does not recover forgotten revocations or automatically stop clients accepting old certificates. PITR intentionally recreates earlier database state; the external-world mismatch is FireCA’s recovery problem. [PostgreSQL PITR](https://www.postgresql.org/docs/current/continuous-archiving.html)

**Acceptance:** pause/crash/partition tests around admission, provider invocation, result persistence, and release; suspension during an operation; Redis loss; ambiguous commit; old signer still running during attempted takeover; and rollback before a known issuance/revocation. Expected results must include pending/quarantined states where safe completion is impossible.

**R5 — Choose an operable custody and bootstrap model**

For initial software custody, store encrypted key payloads and their metadata in PostgreSQL. This reduces coordination between separately restored payload and metadata stores. Use maintained authenticated encryption, versioned wrapping keys, and binding to tenant/resource/key version. Database encryption metadata does not itself isolate keys from platform administrators.

For restricted self-hosting, propose human unlock after complete signer shutdown as the default. Retain at least two separately secured recovery copies of high-entropy wrapping/recovery material, with named custodians and a tested procedure. Do not introduce a complicated quorum cryptographic scheme before an actual assurance requirement calls for it.

Automatic unlock can be a supported alternative using locally managed operator credentials. Hosted operation will likely choose it. State the consequence: the credential’s administrative environment joins the custody trust boundary. The same product can support both operating profiles without mandatory cloud key services.

Bootstrap must work when FireCA’s CA is unavailable:

- Install with customer-supplied service TLS, or a locally pinned bootstrap certificate with an explicit replacement procedure.
- Create the first administrator through a local, one-time setup operation; remove setup access afterward.
- Maintain a documented local administrative recovery path independent of an unavailable external identity provider.
- Provision/unlock custody separately from ordinary tenant enrollment permissions.

Wrapping rotation should retain old metadata/material until rewrapping and backup compatibility are verified. Key rotation, wrapping rotation, and issuer rollover are different operations. Retired issuers may still need their old signing capability for CRLs.

**Acceptance:** rebuild into a fresh disconnected environment from supplied artifacts, database backup/WAL, recovery material, and documented credentials. Test loss of one recovery copy, interruption during wrapping rotation, locked-custody denial, administrator recovery, and old-issuer revocation publication.

If the selected pilot requires an HSM or a specific validated module, move that one provider experiment forward. Do not infer a universal government requirement or postpone a buyer’s actual prerequisite to M8.

**R6 — Test native Windows automation with a narrow connector hypothesis**

Start with a domain-local server-side connector exposing the XCEP/WSTEP path and handling Windows authentication/directory lookup. FireCA remains the CA/policy backend. The connector may use a Windows-appropriate implementation; no mandatory endpoint agent is introduced.

The experiment should establish how a native caller is authenticated, mapped to a stable directory identity, and granted a profile. FireCA should accept scoped connector identity assertions over an authenticated channel, with directory-derived names and permissions. It must not trust a caller-supplied UPN, SAN, or SID as authoritative.

Microsoft documents web-service autoenrollment using Group Policy endpoint configuration, XCEP policy retrieval, WSTEP requests, and local certificate/key stores. Its example describes Microsoft’s complete topology; it is not evidence that inserting an arbitrary backend already works. [Autoenrollment model](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cersod/dd492d51-9c18-4d52-a8db-e9cfe35a80b2), [domain XCEP/WSTEP example](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cersod/e35f4aaa-9087-4513-845a-aabb7eb12418)

Secure a real lab with patched supported domain controllers, a member server, and Windows clients. Begin with one declared version/domain/authentication configuration. Expand only after it works.

**Acceptance:** machine and user enrollment and renewal triggered by ordinary background policy behavior; reboot/logon; changed policy; removed enrollment permission; disabled/renamed identities; connector outage and recovery; certificate installation; and use by the intended relying service. Manual `certreq` success is diagnostic evidence, not completion.

For AD certificate authentication, include strong mapping and appropriate trust registration in the test. Current patched domain-controller behavior cannot be validated by relying on obsolete weak-mapping compatibility settings. This requirement is conditional on that certificate purpose. [Microsoft KB5014754](https://support.microsoft.com/en-us/servicing/os/windows-server/2022/05/kb5014754-certificate-based-authentication-changes-on-windows-domain-controllers)

DCOM parity, key archival, and broad AD CS replacement can remain explicit exclusions. Windows feasibility remains early and mandatory; completing all Windows production support need not block a TLS-only first usable CA release.

**R7 — Use DNS-01 first and make network context part of authorization**

Support DNS-01 for the first ACME release. Begin with exact DNS names authorized by enrollment grants; add wildcards, IP identifiers, and further challenges when a defined use needs them.

Self-hosted FireCA validates through explicitly configured local resolvers. Hosted FireCA can validate public DNS directly where appropriate, but private split DNS requires a tenant-local outbound connector. It performs scoped DNS validation, rather than providing a general customer-network tunnel.

Bind each validation task/result to tenant, authority, profile, account-key identity, authorization/order, challenge token, expected value, validator context, and expiry. The authenticated connector identity must determine its tenant scope. Do not accept a validation result because it merely names an order supplied by the connector.

Account binding helps account association; namespace grants still determine permissible identifiers. DNS-01 is standard ACME functionality, and HTTP challenge validation has explicitly documented SSRF concerns. [RFC 8555 DNS challenge](https://www.rfc-editor.org/rfc/rfc8555.html#section-8.4), [account binding](https://www.rfc-editor.org/rfc/rfc8555.html#section-7.3.4), [SSRF considerations](https://www.rfc-editor.org/rfc/rfc8555.html#section-10.4)

A customer connector/DNS operator can affect evidence inside its own authorized namespace. Treat that as part of the customer trust boundary. Its credentials must not permit assertions for another tenant or authority.

**Acceptance:** two tenants use the same private hostname with different DNS views; correct independent-client enrollment/renewal; cross-tenant connector substitution; expired/replayed results; namespace denial; connector outage; bounded DNS alias handling; and no order-triggered general HTTP access.

HTTP-01 is the convenience alternative. It requires explicit private-target ranges, redirects, address revalidation, and routing protections. Do not build it initially unless pilot evidence justifies the additional surface.

**R8 — Define relying-service behavior and publication lifetime before issuance**

Choose a representative server TLS client and an explicitly configured mTLS relying service for the slice. Document trust installation, supported names/usages, permitted issuers, identity mapping, revocation checking, cache behavior, and stale/unavailable-status handling.

For separate tenant services, provision only the intended trust domain. For a shared service trusting multiple customer roots, map identities using an authorized trust/issuer context plus the subject’s stable identity. A repeated subject or SAN must not collapse identities across issuers.

Use CRLs first where the chosen client configuration supports them. Do not make “revoked in FireCA” synonymous with “rejected everywhere.” For example, Go’s standard X.509 verification does not perform revocation checking. [Go X.509 verification documentation](https://pkg.go.dev/crypto/x509#Certificate.Verify)

Propose stable customer-controlled publication names for self-hosting, with issuer-version paths independent of pod/deployment names. Preserve old issuer keys, CRL numbers, publication routes, and necessary history until the last affected certificate’s validity and applicable retention obligations end. The CRL freshness contract must account for `nextUpdate` and actual client refresh behavior. [RFC 5280 CRL freshness](https://www.rfc-editor.org/rfc/rfc5280.html#section-5.1.2.5)

A reasonable experimental target is publication within five minutes of accepted revocation, hourly CRL expiry, and a tested relying-service refresh bound. These are proposed targets; publication in five minutes does not imply client rejection in five minutes.

**Acceptance:** correct-chain use, wrong-tenant rejection with the same subject name, revoked certificate rejection, stale/missing status behavior, old/new issuer coexistence, retirement, and publication-host migration. Document any supported client that accepts certificates without revocation checking and choose its validity policy accordingly. Short lifetime limits exposure; it is not immediate revocation.

**R9 — Separate paid entitlement from avoidable runtime outages**

My preferred self-hosted model is a paid entitlement granting continued operation of the acquired version, while support, maintenance, new versions, and additional licensed capacity have their own terms. A locally verified signed file can encode those rights without making existing renewal or CRL service depend on a runtime expiration cliff.

This remains paid use for every organization. It does not create government, educational, charitable, or organizational noncommercial exemptions. Hosting/resale/embedding rights remain separate. It is a proposal about continuity mechanics, not an adopted license grant.

This model has a commercial tradeoff: customers may continue using old versions without renewing maintenance. A term-based runtime license could increase renewal pressure, but creates a substantially harder outage, emergency-license, vendor-unavailability, and recovery problem. Source availability also makes adversarial enforcement against the deployment administrator a poor technical security objective; define rights contractually and implement clear entitlement checks.

Before an organizational pilot, establish a written agreement covering price or expressly granted evaluation rights, permitted deployment, evaluation termination, exports, recovery, and continuing status publication. Do not imply that the current reservation notice already grants evaluation rights. Set contribution/modification permissions before accepting external work.

Hosted service needs a separate cancellation/exit contract. Propose a defined renewal grace interval, then scheduled cessation of issuance, while preserving export and revocation/status service for outstanding certificates through their remaining supported lifetime. Bound issuance validity and fund that retention obligation. Vendor disappearance still requires a customer-controlled publication/transfer contingency; indefinite vendor availability cannot be guaranteed.

**Acceptance:** supplied container images, charts/manifests, dependencies, assets, notices, verification keys, and documentation install and rebuild without external networking. Test expired maintenance, clock anomalies, license-verification-key changes, recovery, data export, and continuing old-issuer publication.

Test upgrades and supported downgrade/recovery procedures. Blindly restoring an old database after later issuance is not an acceptable software rollback strategy.

**R10 — Move one observer forward**

Bring a narrow TLS endpoint observer into the initial lifecycle slice. It needs a registered host/address, port, protocol, and SNI context; authenticated target scope; timestamp; observed certificate; and reachability/freshness state.

Use explicit registered targets rather than broad network scanning. A tenant-local worker/connector can perform hosted-private observations, sharing transport infrastructure with DNS validation where practical but retaining separate permissions and message types.

The workflow must visibly demonstrate:

1. Import or issue the currently deployed certificate.
2. Obtain a replacement using an independent ACME client.
3. Leave the endpoint on the old certificate and detect “replacement not deployed.”
4. Install the replacement and confirm it through a fresh observation.
5. Stop the observer or endpoint and show stale/unknown status.

This provides deployment-health value without requiring a Windows store agent, automated installation across arbitrary products, or a broad discovery platform. If deployment automation is absent, describe FireCA as detecting the deployment gap; do not imply it performs the deployment.

**A coherent next phase**

I would package the work into three outcomes:

1. **A short decision packet.** One pilot hypothesis; initial trust/privilege matrix; protected-operation and recovery guarantees; custody/bootstrap defaults; ACME and observer topology; relying-client contract; proposed license continuity. Select supported dependencies/build tooling before runnable scaffolding. Record accepted consequential choices as ADRs and adjust affected milestone gates.
2. **One synthetic vertical slice plus a Windows compatibility experiment.** The slice serves two tenants/authorities, accepts customer-root signing, issues through API and ACME, observes a real TLS endpoint, publishes revocation, rolls over an issuer, and performs an offline rebuild. Include the bounded engine comparison. The separate real Windows lab exercises unattended native behavior.
3. **A pilot readiness decision.** Select the actual buyer, validate measurable benefit and setup burden, establish written entitlement, and demonstrate the recovery/trust/custody conditions its operator requires. Bring forward a particular HSM or isolation capability only where that pilot requires it.

The slice can use a minimal experimental scaffold while these choices are being tested. Requiring a complete production engine decision, a real paying customer, and every Windows behavior before any executable experiment would make the evidence unnecessarily expensive to obtain.

Retain broad HSM coverage, general secrets, standalone-key operations, public-provider connectors, extensive collectors, and additional Windows compatibility surfaces for later milestones. Browser-generated keys and optional managed application custody remain committed product flows; they should follow the CSR-first path with explicit formats and export permissions rather than expand this first feasibility slice.

The phase succeeds when it produces a justified ownership choice, bounded operational guarantees, independently demonstrated interoperability, and a pilot-relevant lifecycle outcome. Documentation agreement alone does not close the findings.
