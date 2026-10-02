# Initial independent adversarial reviews

**Privacy editing note:** Personal/local context is omitted. Baseline IDs and evidence links refer to privacy-sanitized equivalents of the original reviewed commits; technical findings are retained.

- Date: 2 October 2026
- Baseline: [e5126db1271c80c810ae81ffec52b1a953eff7dd](https://github.com/benhook1013/FireCA/tree/e5126db1271c80c810ae81ffec52b1a953eff7dd)
- Reviewers: two fresh subagents, each started without inherited chat history
- Independence: each reviewed the repository and primary references without consulting the other reviewer; the parent collected progress and final reports
- Scope: product direction, architecture, delivery order, and operational assumptions in a documentation-only proposal
- Status: completed design reviews; recommendations remain proposals, not adopted architecture changes

The full reports are [reviewer A](reviewer-a.md) and [reviewer B](reviewer-b.md). Repository evidence links point to the frozen baseline so later documentation edits do not move their targets. This synthesis is the parent agent's interpretation of the two reviews.

## Overall assessment

Both reviewers support a bounded feasibility phase and call for revision of delivery gates before broad implementation or a customer pilot. Neither recommends discarding the accepted Java/Spring Boot, Bouncy Castle, PostgreSQL/Redis, private-PKI, CSR-first, or source-available commercial direction.

Reviewer A permits a minimal experimental scaffold while requiring consequential boundaries before substantive backend contracts. Reviewer B is more explicit about making the engine choice and securing the Windows experiment before scaffolding. The common practical next step is a small set of decisions and experiments, not expanding the feature list.

No application behavior, security guarantee, customer demand, or interoperability was demonstrated by these reviews. Agreement between reviewers is evidence of recurring concerns, not independent experimental validation.

## Consolidated objections

| ID | Concern | Review evidence | Decision or evidence needed | Timing |
| --- | --- | --- | --- | --- |
| R1 | The first paying customer and useful release are unspecified | A1; B1 | Select one pilot outcome, required features, migration/coexistence path, and measurable benefit; align its release gate | Before substantive product contracts; validate before pilot |
| R2 | Owning the CA engine may consume the effort intended for differentiation | A1; B2 | Bounded build-versus-adapt comparison including custody, transactions, standards, editions, and redistribution rights | Before locking issuance/provider contracts |
| R3 | An isolated signer has no declared independent approval authority | A2; B4 | Define trusted operators/components and what must survive their compromise; show who can approve/change policy/use keys | Contract before role boundaries; enforcement before pilot |
| R4 | Generation fencing and CA history can be undermined by restore, promotion, and delayed side effects | A3; B5/B6 | Define provider execution versus durable issuance versus release guarantees; define safe restore and old-signer exclusion | Before distributed signing; demonstrate before pilot |
| R5 | Software custody has no selected bootstrap, unlock, and recovery operating model | A4; B6 | Choose recovery material ownership, restart behavior, wrapping-key handling, rotation recovery, and restoration procedure | Before custody implementation/pilot |
| R6 | Native enrollment may pass a manual acquisition test without proving unattended autoenrollment or intended use | A7; B3 | Real Group Policy machine/user enrollment and renewal; supported authentication/profile matrix; actual relying-service use | Feasibility before freezing Windows contracts; full evidence before Windows pilot |
| R7 | Hosted private ACME lacks tenant-specific validation reachability and routing | A5 | Select challenges and private-network topology, bind validation results to tenant/request, and test overlapping namespaces and hostile routing | Before gateway/connector contracts; demonstrate before hosted ACME pilot |
| R8 | Revocation and independent trust depend on explicit relying-party behavior and publication lifetime | A6; B7 | Trust provisioning/identity mapping, stable status URLs, freshness/failure behavior, issuer retirement and client matrix | Trust contract early; verify before pilot certificates |
| R9 | Disconnected paid operation lacks license-expiry, maintenance, recovery, and exit continuity | A8; B8 | Explicit pilot entitlement and continuity behavior; offline install/upgrade/restore and status publication tests | Before organizational pilot and license enforcement |
| R10 | Deployment-health value may arrive after the initial lifecycle release | A sequencing note; B1 | Include one real endpoint/store observation path in a lifecycle pilot, or narrow its advertised outcome | Before claiming observed deployment-health value |

## Concrete scenarios worth testing

### Approval abuse despite isolated key custody

A compromised control plane changes a profile or manufactures an approved request in the shared database. A signer can enforce key non-export and still sign that request. Resolve which components are trusted to authorize tenant CA use and whether the signer has any independently established constraints. Both reviewers identify this distinction.

### Restoring a database does not restore the outside world

After certificates have been issued and revoked, an operator restores an older database while preserving the CA key. Active clients can still present later certificates, but restored records, revocations, and ownership counters may have forgotten them. PostgreSQL documents potential transaction loss under asynchronous replication and the need to exclude an old primary after failover. The effect on FireCA's CA history is the reviewers' architectural inference. [Replication](https://www.postgresql.org/docs/current/warm-standby.html#SYNCHRONOUS-REPLICATION), [failover](https://www.postgresql.org/docs/current/warm-standby-failover.html)

### Native acquisition is only part of Windows compatibility

Test unattended enrollment and renewal after policy changes and restart, not only a manual native request. If the selected profile is used for domain certificate authentication, also test current mapping requirements; that condition does not apply to every Windows TLS certificate. [Microsoft autoenrollment](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cersod/dd492d51-9c18-4d52-a8db-e9cfe35a80b2), [certificate authentication mapping](https://support.microsoft.com/en-us/servicing/os/windows-server/2022/05/kb5014754-certificate-based-authentication-changes-on-windows-domain-controllers)

### A hosted validator cannot automatically reach a private service

Hosted FireCA needs a deliberate way to verify a challenge inside each customer's private network. Challenge-related requests must not become unrestricted cross-tenant network access. Account association is not itself identifier authorization. [ACME protocol and security considerations](https://www.rfc-editor.org/rfc/rfc8555.html)

### Expired entitlement can create a later operational failure

If license expiry stops renewal or revocation publication, the eventual effect is broader than blocking new administration. Decide continuity and recovery behavior before implementing enforcement. This concern does not change the accepted paid-organizational-use policy.

## What differs between the reviews

- A gives hosted private ACME validation its own major objection and identifies trust-anchor/identity mapping across tenants.
- B separately examines the exact fencing promise at provider execution, result persistence, and publication; it also foregrounds cold-start administrative/service-TLS bootstrap.
- Both require actual unattended Windows evidence; B foregrounds intended authentication use, while A explicitly warns against treating a native manual enrollment test as autoenrollment proof.
- A emphasizes offline installation and maintenance artifacts. B emphasizes evaluation rights, exit obligations, and revocation behavior in specific relying clients.
- Their disagreement is mainly how much to decide before scaffolding, not a fundamental disagreement over the chosen stack.

## Calibration and fixed choices

- The reviews establish missing documentation/evidence, not that customer knowledge or commercial demand does not exist. Record any relevant existing experience in the pilot charter without exposing confidential customer information.
- Do not infer that every government deployment requires an HSM or a particular assurance certification. Ask what the selected pilot actually requires.
- Shared software custody can be an explicit accepted initial trust boundary. A stronger compromise guarantee needs a different independently enforceable boundary; it is not achieved merely by calling the signer isolated.
- Domain certificate-authentication mapping is conditional on the Windows use case. Native enrollment remains mandatory; complete AD CS parity has not been committed.
- Existing-engine vendor recommendations and edition descriptions are inputs to evaluation, not proof that a particular license legally forbids production. Verify actual dependency terms before selection.
- API/portal/ACME can be a useful engineering release without being the selected customer's complete commercial release. State which outcome is being delivered.
- General secrets, additional collectors, broad HSM coverage, portal styling, and final resource names can remain later work. Safe manual recovery and pilot-relevant revocation behavior cannot wait for a scale milestone.

## Proposed minimum next work

1. Write a short first-pilot charter: buyer/use case, environment, required assurance and integration, measurable outcome, and coexistence/migration path. Choose hypotheses where customer evidence is not yet available.
2. Record a bounded engine comparison and ownership decision using one synthetic two-authority issuance/revocation/rollover workflow. Do not build two complete products for the comparison.
3. Write the initial signer and custody/recovery contracts: trusted actors, approval authority, key unlock/recovery, interrupted-operation outcomes, suspension semantics, and safe database restore.
4. Secure the real Windows/AD lab and define unattended enrollment/renewal tests. In parallel where practical, define the hosted private-ACME validation topology and one observable certificate deployment.
5. Before an organizational pilot, demonstrate the selected relying-client revocation/rollover behavior and disconnected restore, and establish pilot entitlement plus license-expiry/exit continuity.

These are proposed follow-ups from the critique. No original ADR, product commitment, or implementation milestone has been silently changed. No additional broad specification was required to obtain this initial critique.
