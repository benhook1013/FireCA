# Independent resolution proposal A

**Privacy editing note:** Personal/local context is omitted. Baseline IDs and evidence links refer to privacy-sanitized equivalents of the original reviewed commits; technical findings are retained.

- **Reviewer:** GPT-6.1-Sol / high.
- **Date:** 2 October 2026.
- **Repository snapshot:** `3daaa89becd0bc66279215930e73e4e56e19f500`.
- **Method:** Independent, read-only review of AGENTS.md, both complete adversarial reports, their consolidation, the product/architecture/domain/roadmap/licensing documents, and accepted ADRs. Selected primary references were checked.
- **Status:** Recommendations for discussion. Nothing below is an accepted decision, implemented feature, demonstrated interoperability result, or legal license grant.
- **Verification:** HEAD matched the supplied snapshot and the working tree was clean. No application tests were possible; the repository remains a design repository.

I recommend proceeding with a bounded feasibility phase followed by a narrow synthetic implementation. The first commercial hypothesis should be **self-hosted internal TLS issuance and observed renewal under an existing customer root**, for an organization that explicitly accepts shared software custody. This fits the accepted stack and ACME requirement without making Windows, every HSM, general secrets, or deployment automation prerequisites for all customers.

No evidence establishes that a particular organization will adopt this product, accept its custody model, or pay for its first release.

## Coverage and recommended decisions

| Finding | Recommended choice | Why and principal tradeoff | Acceptance evidence | Decision and implementation order |
| --- | --- | --- | --- | --- |
| **R1 — First customer/release** | Adopt a self-hosted internal TLS lifecycle pilot hypothesis; select the actual organization through customer evidence. | Produces a bounded outcome and coexistence path. It may not suit a Windows-first or hardware-custody buyer. | Pilot charter, documented current workflow/setup cost, customer acceptance of limitations, renewal and deployment demonstrated on representative services. | Hypothesis now; customer validation before commercial scope freeze; value demonstrated before pilot success. |
| **R2 — CA engine ownership** | Default to a small FireCA-owned issuance/lifecycle core using Bouncy Castle, subject to a short EJBCA comparison before locking core contracts. | Avoids introducing another policy/database authority into the initial architecture. FireCA still assumes substantial CA-service engineering responsibility. | Decision matrix and representative synthetic workflow; actual dependency terms and relevant commercial conditions examined. | Comparison early; explicit ownership ADR before substantive issuance implementation. |
| **R3 — Signer authority** | Accept a trusted-platform model initially: platform operators, PostgreSQL administrators and control-plane authorization are trusted. The signer enforces constrained operations and tenant bindings, without claiming independent approval against their compromise. | Practical for a new product and honest about shared custody. Some customers will require an earlier stronger boundary. | Privilege matrix, customer acceptance, adversarial tenant/job/export/provider-handle tests, explicit compromise exclusions. | Threat boundary now; enforcement before usable release and pilot. |
| **R4 — Fencing/history/recovery** | One shared active signer, authoritative PostgreSQL state, manual fenced takeover, and separate guarantees for admission, provider execution, persistence and release. Quarantine issuers after uncertain historical rollback. | Sacrifices automatic availability to avoid unsupported distributed guarantees. No exactly-once provider execution promise. | Pause/partition/failure tests; offline rollback drill; old signer physically excluded; no release of uncommitted results. | Contract before distributed signing; evidence before pilot; automatic HA later. |
| **R5 — Custody/bootstrap** | Encrypted key payloads in PostgreSQL; operator-provisioned local wrapping material mounted only into the signer; automatic restart unlock within the declared trusted-platform boundary; independent offline recovery copies. | Simple operations and backups. Host/cluster administrators remain capable of compromising software keys. | Cold install/rebuild, restart without internet, wrapping-key rotation interruption, recovery-copy loss, admin/TLS recovery. | Bootstrap and ownership now; implementation before any custody use. |
| **R6 — Windows** | Pursue native XCEP/WSTEP through a customer-local domain integration, initially one forest and intranet-connected clients. Require genuine unattended machine and user enrollment/renewal. | Limits compatibility breadth while preserving native enrollment without a PC agent. Feasibility remains unknown. | Real patched Windows/AD lab, Group Policy events, unattended issuance/renewal, permission denial, actual relying-service use, recorded matrix. | Secure lab immediately; experiment before Windows contracts freeze; full evidence before Windows pilot. |
| **R7 — Hosted private ACME** | Start with DNS-01. Hosted validation initially uses publicly reachable challenge records for permitted names; self-hosted validation may use configured local DNS. Private-only hosted DNS requires a later tenant-local validator. | Avoids a broad cross-network connector obligation in the first release. Hosted private-only namespaces are explicitly unsupported initially. | Independent ACME clients; tenant-bound validation results; overlapping-name, replay, resolver-routing and hostile-input tests. | Challenge/topology contract early; supported mode demonstrated before its release; local hosted validator before claiming that mode. |
| **R8 — Trust/revocation** | Declare supported relying clients, stable customer-controlled publication addresses, complete CRLs initially, and explicit issuer-retirement obligations. | Narrower interoperability promises; some clients require additional mechanisms or application changes. | Independent chain/identity tests; revoked/stale/unreachable-status behavior; cross-tenant rejection; old/new issuer coexistence. | Publication and trust contract before pilot certificates; all selected client behavior before pilot. |
| **R9 — Offline licensing/continuity** | Propose paid self-hosted licenses with continued use of purchased versions; maintenance controls updates/support. Preserve revocation, recovery and export continuity. Give evaluation rights explicitly. | Less technical renewal leverage, stronger customer continuity. Hosted subscription termination needs a separate funded exit contract. | Formal approved terms; local entitlement behavior; offline install/upgrade/restore and expiry/clock/key-rotation tests. | Business choice now; actual agreements before organizational evaluation/pilot; behavior before enforcement. |
| **R10 — Observed lifecycle value** | Include one authorized TLS endpoint observer in the first lifecycle pilot. | Adds a small operational capability earlier; does not promise automatic installation or broad discovery. | Renewed-but-undeployed alert, fresh replacement observation, stale/unreachable distinction, tenant scope tests. | Observation contract alongside ACME; implementation before claiming deployment-health value. |

## R1 — Choose an adoption hypothesis without inventing a customer

The recommended first outcome is:

> An organization can use its existing root to authorize a FireCA issuing authority, enroll a bounded set of internal HTTPS services through ACME, renew certificates, and see whether the replacement is actually deployed.

Use one supported Kubernetes installation profile and an existing customer-controlled root. Start alongside the incumbent CA; do not require an enterprise root replacement or fleet migration. Inventory imports can show useful coexistence without giving FireCA powers over externally issued certificates.

The pilot charter should identify:

- The intended buyer and operator, the existing workflow, and the problem they consider worth paying to fix.
- The initial services, relying clients, trust provisioning, DNS access, custody requirements and deployment constraints.
- Setup effort, current renewal effort and incidents, and agreed success measures.
- The migration/coexistence and exit path.
- Explicit exclusions, especially Windows production enrollment, mandatory hardware assurance, broad discovery and automatic certificate installation.

**Proposed evidence threshold:** Discuss the hypothesis with roughly three relevant prospective organizations, and obtain one concrete design-partner commitment that includes an operator, representative environment and acceptance criteria. These are targets for subsequent work; no outreach is authorized or performed by this review.

Prior experience can supply initial workflow assumptions and sensible interview questions. Separate those assumptions from current customer statements.

A Windows-first or mandatory-HSM buyer need not invalidate FireCA. It changes the pilot’s dependency order. Do not call the API/portal/ACME release that customer’s complete commercial solution when required integrations are still absent.

## R2 — Own a narrow core unless adapting an engine demonstrably reduces the total burden

My proposed default is to keep FireCA’s authoritative issuance decisions, policy, history, jobs and lifecycle state in its PostgreSQL model, using maintained libraries for certificates, ASN.1 and cryptography.

That is a **CA-service ownership decision**, not merely a library selection. It carries responsibility for serial reservations, issuer state, revocation, profile construction, ambiguous attempts and recovery.

Before committing, cap an EJBCA comparison at approximately five engineering days. Smallstep can serve as a capability/operational baseline; adopting it as the primary backend would require revisiting the accepted Java direction and should not happen implicitly.

Evaluate one workflow: two tenant authorities, customer-root-signed intermediates, constrained CSR issuance, revocation/CRL, issuer rollover, and interrupted operation/recovery. Inspect unsupported steps rather than implementing two complete products.

The comparison must answer:

1. Which component owns each issuance decision, serial, revocation and certificate record?
2. Does adaptation introduce two independently changing policy or transaction histories?
3. Can FireCA preserve tenant authorization and prevent callers bypassing it through engine interfaces?
4. What survives an ambiguous engine response?
5. Can the exact distribution run offline with the required custody and Windows surfaces?
6. What are the engineering, support, edition and redistribution obligations?

EJBCA documents multiple CA instances in a shared installation and labels its native Windows enrollment offering Enterprise. Its current Community documentation discourages production use, while its repository includes LGPL terms. Vendor positioning is not itself a legal prohibition; actual release terms and any commercial agreement must be examined. [EJBCA architecture](https://docs.keyfactor.com/ejbca/9.3.2/ejbca-architecture), [Windows enrollment offering](https://docs.keyfactor.com/ejbca/latest/microsoft-auto-enrollment-overview), [Community repository](https://github.com/Keyfactor/ejbca-ce), [license text](https://raw.githubusercontent.com/Keyfactor/ejbca-ce/main/LICENSE).

**Adopt an engine only if** it passes the non-negotiable requirements and removes more operational/security work than its integration, packaging and commercial dependencies introduce. Otherwise record the narrow ownership decision and its limits. An external-engine integration can remain a future connector.

## R3 — State what the signer protects

Use separate deployable control-plane and signer processes, but explicitly include control-plane authorization, PostgreSQL administrators and privileged platform operators inside the initial trust boundary.

The signer should protect against accidental or unauthorized use through ordinary product paths:

- Workers submit references to persisted requests, not arbitrary bytes to sign.
- The signer resolves tenant, authority, key version, profile version and approved input binding.
- Current grants, authority state and operation restrictions are checked at protected admission.
- CA keys support only declared CA operations. Generic application/standalone-key operations cannot select them.
- Ordinary APIs never export CA private keys.
- Profile changes are versioned; previously approved inputs cannot silently change.

The signer **does not independently prove legitimate approval** if a trusted control plane or database administrator manufactures all the records it trusts. An HSM that signs those records does not repair that authorization boundary.

Document powers for authority import, profile/grant changes, application-key export, software-key recovery, issuer suspension and administrative recovery. For imported issuers, verify key/certificate matching and chain/profile constraints and require explicit operator approval of the authority association.

Start with append-only application audit permissions and durable events. Database-superuser tampering remains outside that assurance. An independently administered audit destination can strengthen evidence for customers needing it; a hash chain stored only beside its records does not independently establish historical truth.

**Evidence:** Test tenant substitution, forged context, stale permission caches, grant removal after job creation, mismatched provider handles, inappropriate CA-key operations, and exports through both synchronous and background paths. Confirm the pilot customer accepts the remaining shared compromise boundary. If it does not, introduce the particular independent boundary before that pilot.

## R4 — Make execution, issuance and release separate promises

The initial topology should have **one active signer serving many tenants**, with per-authority protected admission and manual takeover. Workers can scale independently; they do not possess CA provider credentials.

A replica count of one is an operational configuration, not proof that an old paused pod cannot execute. Replacement after uncertain node failure must require physical/process exclusion of the old signer before provider material is made available to the replacement.

Define the following contract:

| Boundary | Proposed guarantee |
| --- | --- |
| Admission | Current authority state, grants and ownership generation authorize a persisted operation with immutable inputs. Stale generations cannot admit a new operation. |
| Provider execution | An admitted operation may execute more than once during reconciliation. An already admitted operation may complete after a suspension request. |
| Durable issuance | At most one completed certificate result is selected and committed for the durable request; issuer/serial reservations are unique in the supported database history. |
| Release/publication | Normal interfaces expose only committed results. Stale attempts cannot overwrite a selected result or publish an uncommitted artifact. |
| Suspension | Distinguish “suspension requested” from a completed admission/drain barrier. Do not acknowledge a fully drained signer while its execution state is unknown. |

Persist the serial and exact certificate inputs before invocation. After a sign-success/persistence failure, retry or reconcile the same logical request. Never allocate a fresh serial automatically because the provider timed out.

Different executions of the same immutable certificate inputs may produce different signature bytes. Select one recorded result, retain the attempt history, and handle unknown attempts explicitly. Do not claim exactly-once HSM execution or that database rollback erased a signature.

A database lock alone cannot ensure that an external provider stops immediately when a connection is lost. Therefore an uncertain takeover pauses progress until old execution is excluded. This is an intentional availability tradeoff.

For disaster recovery:

1. Stop new admissions and preserve the last available public status artifacts.
2. Exclude old database and signer instances; invalidate their service credentials.
3. Restore database, encryption metadata and recovery material together.
4. Start under a fresh recovery epoch, with signing disabled.
5. Establish whether issuance, grant, suspension and revocation history is complete.
6. Resume an issuer only after reconciliation; otherwise quarantine it.

PostgreSQL requires exclusion of an old primary after promotion, and asynchronous replication can lose acknowledged history. FireCA must not treat an asynchronous promotion as transparent CA recovery. [PostgreSQL failover](https://www.postgresql.org/docs/current/warm-standby-failover.html), [replication durability](https://www.postgresql.org/docs/current/warm-standby.html#SYNCHRONOUS-REPLICATION).

Initially support durable local commits, base backups and continuous WAL archiving, with manual recovery. Publish measured recovery objectives after drills; do not assert zero-loss disaster recovery merely because WAL archiving is configured.

If complete history cannot be recovered, replacing the issuing key may be necessary, but it does not invalidate the old issuer. Rejection requires parent revocation where relying clients demonstrably enforce it, or an explicit relying-party trust/blocking change. Random serials and a fresh epoch do not recover forgotten revocations.

**Evidence:** Pause before provider invocation, lose the database connection during execution, fail persistence after signing, suspend mid-operation, lose Redis entirely, restore a pre-revocation backup and attempt restart with the old signer still available. Observe outcomes at every boundary.

## R5 — Choose a custody model people can operate

Store encrypted key payloads and their versioned encryption/provider metadata in PostgreSQL initially. This reduces the number of storage systems that must be restored consistently.

Use maintained authenticated encryption with record binding that includes tenant, logical key identity, version and purpose. Use separately encrypted data keys wrapped by a versioned deployment wrapping key.

For the first deployment:

- The customer/operator provisions wrapping material as a protected local secret mounted only into the signer.
- The signer automatically unlocks on restart.
- Ordinary control-plane and worker identities cannot read that mount.
- Two independently stored offline recovery copies are available, with documented custodians and access procedures.
- CA recovery/export is a separate privileged operational procedure, not an ordinary download API.

This places automatic unlock within the already declared trusted-platform boundary. It does not claim resistance to a hostile host or Kubernetes administrator. Requiring human unlock after every pod restart should be an explicit alternative operating mode, not an accidental default that breaks unattended renewal.

Rotation must be recoverable: keep old wrapping versions until all live records and retained backups have a supported recovery path. Test interruption halfway through rewrapping and restoring a backup encrypted under the old version.

Bootstrap also needs a selected default. Use a local operator command to establish the first administrator through a short-lived, single-use credential. Provision service TLS and trust out of band using customer certificates or a separate installation trust mechanism. Do not require the production CA to function before an operator can reach its recovery controls. Local identity must remain available when an external identity provider is unreachable.

**Evidence:** A different operator rebuilds into a fresh offline environment using only the documented package, backup and recovery material; a remaining copy works after loss of one recovery copy; restart, admin recovery, TLS renewal and retired-issuer CRL signing succeed.

Hardware custody should move earlier only when the selected customer requires it. Such requirements and any necessary assurance certification remain unresolved facts.

## R6 — Treat Windows as a real integration experiment

The first proposed Windows topology is a customer-local domain integration serving XCEP/WSTEP to native clients in a single forest. Test Windows-integrated authentication and directory-derived identity through that topology. This is a hypothesis, not proof that Java-hosted Kerberos or a connector will meet all native client requirements.

Prefer a supported domain service account with explicitly scoped privileges. If protocol/authentication work needs a Windows service, permit that component while keeping FireCA’s primary backend Java. Do not substitute a PC inventory agent.

The feasibility lab should contain a patched domain controller, a domain-joined Windows client and representative machine/user profiles. Capture exact builds and patch levels instead of claiming general “Windows support.”

Required evidence:

- Group Policy causes initial machine enrollment and user enrollment without an enrollment wizard or FireCA PC agent.
- Background renewal occurs without a human submitting another CSR.
- Restart and temporarily unavailable gateways recover correctly.
- Permission removal, disabled identities and identity renames have documented effects.
- Directory identity determines requested names; caller-supplied fields cannot override it.
- The issued certificate is used by the intended relying service.
- If domain certificate authentication is included, strong mapping works on patched controllers.

Microsoft’s autoenrollment model includes Group Policy and local key/certificate stores; enrollment acquisition is only part of that system. Strong mapping is an additional requirement for relevant domain authentication uses, not every Windows server-TLS certificate. [Native autoenrollment](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cersod/dd492d51-9c18-4d52-a8db-e9cfe35a80b2), [enrollment authentication](https://learn.microsoft.com/en-us/windows-server/identity/ad-cs/certificate-enrollment-web-service), [domain authentication mapping](https://support.microsoft.com/en-us/servicing/os/windows-server/2022/05/kb5014754-certificate-based-authentication-changes-on-windows-domain-controllers).

Record exclusions such as DCOM parity, cross-forest arrangements, internet initial enrollment and key archival. These can wait without removing the eventual native enrollment requirement.

If the lab is unavailable, ordinary build/recovery/ACME work may proceed. Windows feasibility remains unmet, and Windows contracts or compatibility claims must remain provisional.

## R7 — Make the initial ACME network contract deliberately small

Choose **DNS-01 as the first challenge**.

For self-hosting, use an operator-configured validation realm with a local resolver and allowed namespace. For initial hosting, support private service endpoints whose challenge records are publicly reachable, including delegated `_acme-challenge` records where the selected DNS setup permits it. The application endpoint itself need not become public.

Do not initially advertise hosted validation for private-only DNS names. That mode requires a customer-local validator or another explicit network arrangement.

Account binding and challenge success must both be subordinate to an enrollment grant. External Account Binding associates an ACME account with an external account; requested identifiers still need authorization. RFC 8555 separately identifies network-validation and SSRF risks. [Account binding](https://www.rfc-editor.org/rfc/rfc8555.html#section-7.3.4), [SSRF considerations](https://www.rfc-editor.org/rfc/rfc8555.html#section-10.4).

Bind validation records/cache entries to tenant, authority, account, identifier, challenge, policy version, validation realm and expiry. Resolver/connector selection must be server-controlled.

Test independent ACME clients, final CSR/order identifier matching, cross-tenant result reuse, stale challenges and two local validation realms using the same name with different answers. DNS delegation and resolver recursion must have bounded, documented behavior.

For a future hosted local validator, use an authenticated outbound channel and narrowly typed challenge work. The connector’s authority, permitted namespaces and network scope must be separate from observation permissions. A compromised customer validator remains a customer-side issuance-authorizing actor within its approved scope.

HTTP-01 can follow when its routing and redirect/rebinding constraints are demonstrated. DNS-only support avoids introducing that HTTP fetch surface into the first release.

## R8 — Make trust and revocation promises about actual applications

Choose stable, customer-controlled CRL publication DNS names before pilot certificates are issued. Separate publication from the administrative portal’s deployment hostname.

Use complete CRLs initially and retain the required old issuer keys and state. OCSP can wait if the selected relying-client contract is satisfied.

Proposed starting defaults for the TLS pilot are 30-day leaf validity, frequent CRL publication and a 24-hour CRL freshness window. These are hypotheses to test and adjust, not SLOs. Publish the actual maximum rejection delay for each supported application, including cache/reload behavior.

Do not infer that a working TLS handshake or certificate parser proves revocation checking. For example, Go’s standard certificate verifier explicitly omits revocation checks. [Go verification behavior](https://pkg.go.dev/crypto/x509#Certificate.Verify).

For clients without effective status checking, explain the residual exposure up to certificate expiry and obtain explicit customer acceptance. If the pilot requires faster compromised-key rejection, choose/configure clients that enforce it or implement the necessary integration before the pilot.

Define trust by issuer/trust domain and application identity. A shared relying service must not map the same subject from two customer issuers to the same tenant identity accidentally. Trust-anchor configuration and application policy govern validation; FireCA tenant identifiers are not an automatic X.509 boundary. [RFC 5280 trust inputs](https://www.rfc-editor.org/rfc/rfc5280.html#section-6.2).

Before retirement/offboarding, establish the latest certificate expiry and retain publication through that date plus the documented safety/cache margin. Suspension of leaf issuance must not unintentionally suspend CRL generation. CRL freshness must be evaluated using `thisUpdate`/`nextUpdate`, and restored systems must not publish an older status artifact as current. [CRL freshness](https://www.rfc-editor.org/rfc/rfc5280.html#section-5.1.2.5).

**Evidence:** Test valid and revoked credentials, unavailable and stale CRLs, cross-tenant identities, issuer rollover, tenant exit and old-issuer status continuity with the selected independent clients.

## R9 — Prefer commercial continuity over certificate outages

My proposed business default is:

> Paid self-hosted organizational entitlement permits continued operation of the purchased software versions. A maintenance term determines eligibility for later versions and support.

This preserves paid organizational use for every category, including government, education and charities. It changes neither the personal-use boundary nor separately negotiated hosting/resale rights.

A signed local license can describe deployment entitlement and maintenance eligibility without forcing existing issuance to stop when maintenance ends. Legitimate purchased-version operation therefore survives procurement delays, vendor unavailability and offline disaster recovery.

This trades some technical subscription leverage for lower operational risk and easier customer acceptance. It should be weighed explicitly against pricing objectives.

For hosting, recommend a separate contract: clear notices, a proposed 90-day transition period, bounded renewal continuity for existing assets, export assistance, and status publication through the last issued certificate’s defined retention period. The cost and custody obligations must be funded and agreed; source availability alone cannot guarantee a hosted service survives vendor failure.

Exports should include certificate/inventory/lineage/audit data, profiles and status artifacts. Software CA-key transfer requires a privileged, protected procedure. Non-exportable HSM keys cannot be promised as downloadable exit artifacts; offer continued status service or reissuance under a replacement authority.

Before organizational evaluation, prepare explicit signed evaluation terms for the named entity and environment. Synthetic evaluations can be time limited. Real operational certificates require a continuity arrangement before they are issued.

Also settle the licensor, formal personal-use grant, modification/redistribution treatment and contributor permissions before accepting outside contributions. These are drafting requirements; this proposal grants none of those rights.

For offline packaging, deliver OCI image archives, deployment templates, vendored dependencies, migration/backup tools, checksums/signatures, verification keys, SBOM/notices and locally served documentation/assets. State prerequisites such as an already installed supported Kubernetes cluster and local registry.

**Evidence:** Deny external networking and install, restart, upgrade, recover and restore using only supplied artifacts and declared local services. Test maintenance expiry, clock anomalies, license replacement and verification-key rotation. Invalid licenses should fail entitlement checks visibly without deleting keys, corrupting history, blocking incident recovery or falsely presenting fresh revocation status.

## R10 — Observe one real deployment early

Move one TLS endpoint observer into the first lifecycle pilot.

Identify endpoints by host/address, protocol, port and SNI. Record observation time, source, presented certificate fingerprint and result independently from assignment and issuance.

A proposed five-minute poll and fifteen-minute freshness threshold are experiment defaults. A fresh observation of the old certificate after renewal must produce “replacement not deployed.” A failed connection must produce unknown/unreachable state, and an old observation must become stale.

The observer should run in the customer environment when reachability requires it. It may share packaging with future collectors, but observation credentials must not implicitly authorize enrollment or arbitrary network access.

**Evidence:** Renew a certificate and deliberately leave it uninstalled; observe the alert. Install the replacement using the selected client’s ordinary deployment mechanism; observe the correct certificate and alert resolution. Then interrupt observation and verify stale state. Include cross-tenant target rejection.

This supports a claim of observed deployment state. It does not establish autonomous installation, broad discovery or every protocol/store.

## Additional individual details retained from the reports

The consolidated table captures the major findings, but several subsidiary points deserve explicit deliverables:

| Individual detail | Proposed resolution |
| --- | --- |
| Cold-start administrator and service-TLS circular dependency, emphasized by B6 | Independent local bootstrap/recovery command and installation trust provisioning; demonstrated in the cold rebuild. |
| Audit evidence against powerful operators, emphasized by A2 | State database/platform operator trust explicitly; offer independently administered audit export when required; make no anti-DBA claim for local audit tables. |
| Revocation after issuer retirement, offboarding or subscription end | Separate issuance suspension from status signing; record last-expiry publication obligations and exit ownership. |
| Directory rename/disable and grant/cache invalidation | Test current authorization at protected admission and document existing-certificate effects separately from new enrollment denial. |
| Wrapping rotation versus retained backups | Preserve old wrapping versions or migrate backups; test recovery from both sides of interrupted rotation. |
| Actual deployment/support cost and team capacity | Pilot charter includes operator labor, incident response responsibility and supported infrastructure; avoid a pilot requiring capabilities the team cannot support. |
| Browser and temporary key custody | Keep CSR-first flow initially. Browser keys and managed keys retain their distinct permissions and phased delivery. Temporary generation stays deferred until retention/retry/backup behavior is precise. |
| Edition positioning versus legal rights | Record verified artifact terms separately from vendor recommendations and feature availability. No inference that “not recommended for production” means legally forbidden. |

## A small next phase with gates

Cap the next phase at **approximately four weeks of engineering effort**, excluding delays obtaining customer or lab access. The cap is a review point, not a promise that all experiments will pass.

1. **Decision package and prerequisites.** Produce a pilot hypothesis charter, engine comparison plan, trusted-actor/privilege matrix, signing/suspension state diagram, bootstrap/recovery outline and proposed commercial continuity rules. Secure the Windows lab. Select and pin supported runtime/dependency versions before a runnable scaffold.
2. **Bounded engine comparison.** Resolve ownership using the representative workflow and exact artifact/commercial conditions. Permit a reversible development scaffold; avoid finalizing issuance contracts before this decision.
3. **Two focused experiment tracks.** First, a two-tenant synthetic CA/ACME/revocation slice with one endpoint observation and injected persistence/takeover failures. Second, the real native Windows experiment. These are experiments, not production gateways.
4. **Offline operating drill.** Package the selected deployment, rotate wrapping material, rebuild into a fresh environment, restore an earlier backup, and demonstrate the quarantine/reconciliation path. Record elapsed times and missing prerequisites.
5. **Evidence review and roadmap revision.** Record proposed ADRs and update affected domain/architecture contracts and milestone gates. Decide which buyer can be supported by the demonstrated boundary.

The exit gates are:

- **Architecture gate:** Explicit CA ownership, trusted actors, signer safety promises and bootstrap/recovery model.
- **Feasibility gate:** Independent ACME evidence, real Windows evidence or a clearly unresolved Windows blocker, and no unsupported recovery/execution claims.
- **Product gate:** At least one plausible customer outcome whose required capabilities fit the proposed first release.
- **Organizational pilot gate:** Approved entitlement/continuity terms, accepted custody boundary, selected-client trust/revocation evidence, offline recovery and the complete required API/basic portal/ACME release.

Passing the feasibility phase authorizes planning the initial usable release. It does not itself satisfy that release or certify a production CA.

## What can be decided now, and what needs evidence

**Decide now:** the narrow pilot hypothesis; trusted-platform initial boundary; one shared signer with manual fenced takeover; PostgreSQL key-payload storage; local restart unlock and offline recovery ownership; DNS-01-first support boundaries; stable publication-address requirement; one early observer; and the proposed purchased-version continuity business model.

**Decide after experiments:** engine adoption, exact signer implementation, safe recovery completion criteria, Windows authentication/connector topology, client revocation behavior, custody-provider capabilities, and supported deployment/runtime matrix.

**Decide with customer and commercial evidence:** the actual first pilot, acceptable migration effort and shared-custody risk, HSM/assurance requirements, supported incident-response commitments, willingness to pay, hosted exit obligations and formal license terms.

A failed experiment should narrow a support claim or change the delivery order. A stop/rethink decision is appropriate if the available customers require an unaffordable combination of Windows breadth, custody assurance and operating support, or if engine rights/integration cannot support the intended distribution. None of those outcomes has yet been demonstrated.
