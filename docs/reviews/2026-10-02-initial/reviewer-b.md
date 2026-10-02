# Independent adversarial review B

**Privacy editing note:** Personal/local context is omitted. Baseline IDs and evidence links refer to privacy-sanitized equivalents of the original reviewed commits; technical findings are retained.

- Date: 2 October 2026
- Reviewer: fresh subagent `/root/adversarial_review_b`
- Conversation inheritance: none
- Reviewed baseline: `e5126db1271c80c810ae81ffec52b1a953eff7dd`
- Method: independent, read-only review; no consultation with reviewer A
- Editing note: report arguments are preserved; repository links use immutable baseline permalinks.

**Assessment: revise the delivery strategy while retaining the accepted product direction.** FireCA has a coherent private-PKI foundation, but the repository does not yet establish a commercially useful first release or an operationally defensible CA service.

I reviewed the requested documents and all three ADRs independently. HEAD is `e5126db1271c80c810ae81ffec52b1a953eff7dd`; the working tree is clean. There is no application code. These are objections to the proposal, not demonstrated implementation defects.

1. **The first paid adoption case remains unclear, and the delivery order may postpone the strongest value.**

   **Evidence:** [Product scope](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/product-scope.md#L5) combines private CA operations, lifecycle management, eventual standalone keys, and secrets. [M5](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/roadmap.md#L66) delivers API, portal, and ACME; Windows and deployed-certificate observations arrive at [M6](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/roadmap.md#L81), while HSMs and stronger isolation arrive at [M8](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/roadmap.md#L105).

   **Failure scenario:** An organization already has certificate issuance. Its expensive problem is discovering deployments, safely replacing certificates, or maintaining Windows enrollment. FireCA’s first release asks it to adopt another CA before those benefits arrive. A workload-focused buyer can already obtain a private CA and ACME from projects such as [step-ca](https://github.com/smallstep/certificates). A restricted-network buyer might instead require HSM custody or operational separation before purchasing. Neither buyer necessarily matches M5.

   **Resolution:** Select one initial organizational buyer and one measurable outcome. Demonstrate a coexistence path using customer-signed issuers or external inventory, with a concrete setup/migration cost and improvement over its current process. Reorder the usable release around that outcome while preserving the eventual commitments. A few prospective buyers’ willingness to run a paid-category pilot would be stronger evidence than another capability list.

   **Timing:** **Before scaffolding:** choose the initial adoption hypothesis. **Before pilot:** demonstrate its value and switching burden. Broader keys/secrets expansion can remain **later**.

2. **The proposal may silently become a new CA-engine project without establishing why that ownership is necessary.**

   **Evidence:** [Architecture](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/architecture.md#L7) selects Bouncy Castle and Java providers; [issuance](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/domain-model.md#L69), revocation, issuer versions, interrupted operations, and custody are FireCA responsibilities. Existing engines are only [evaluation candidates](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/references.md#L30).

   **Failure scenario:** The team spends its early capacity reproducing serial allocation, revocation publication, issuer rollover, provider integration, and enrollment machinery. The inventory and operational experience that might differentiate FireCA receive less attention. Maintained crypto libraries reduce cryptographic implementation risk; they do not supply this whole service.

   **Resolution:** Make an explicit build-versus-adapt decision using one representative workflow: two authorities, external-root signing, authorized issuance, revocation, rollover, and recovery. Compare ownership cost, tenant enforcement, extensibility, offline packaging, dependency rights, and edition costs. Retain Java and FireCA’s policy/lifecycle ownership where appropriate. [EJBCA documents multiple CA instances in one installation](https://docs.keyfactor.com/ejbca/9.3.2/ejbca-architecture), but its [current Community README](https://github.com/Keyfactor/ejbca-ce) describes CE as intended for learning/testing/prototyping and directs production deployments to Enterprise. Adoption therefore needs a concrete edition and commercial assessment.

   **Timing:** **Before scaffolding:** record a bounded engine evaluation and its decision. Full production integrations belong **before pilot**, not in the initial comparison.

3. **Windows acquisition success would still leave Windows usefulness unproven.**

   **Evidence:** [M1](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/roadmap.md#L15) requires policy discovery, enrollment, and renewal; [M6](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/roadmap.md#L83) completes directory identities and profiles. The Windows/AD test environment’s availability is explicitly [unverified](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/roadmap.md#L25).

   **Failure scenario:** A native client obtains a certificate through a configured enrollment wizard, but unattended Group Policy enrollment or renewal fails. Alternatively, the certificate installs successfully yet cannot authenticate to the intended domain service. Microsoft’s [autoenrollment description](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cersod/dd492d51-9c18-4d52-a8db-e9cfe35a80b2) involves local policy, certificate/key stores, Group Policy, and enrollment services. For uses involving domain certificate authentication, [current strong-mapping enforcement](https://support.microsoft.com/en-us/servicing/os/windows-server/2022/05/kb5014754-certificate-based-authentication-changes-on-windows-domain-controllers) can reject an otherwise valid certificate.

   **Resolution:** Define the first Windows use and supported configuration narrowly. Test background enrollment and renewal, actual use by the intended relying service, directory permission denial, identity rename/disable, and recovery after connector interruption. If domain certificate authentication is included, explicitly test its mapping requirements. This does not require committing to complete AD CS parity.

   **Timing:** **Before scaffolding:** secure the lab and select the target Windows scenario. **Before pilot:** produce native-client and intended-use evidence. Additional Windows surfaces can remain **later**.

4. **Shared software custody may exclude intended paying customers before the product has a viable isolation tier.**

   **Evidence:** [ADR 0001](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/decisions/0001-platform-and-storage.md#L22) correctly acknowledges the shared compromise boundary. [Architecture](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/architecture.md#L73) defers dedicated signers/HSM partitions; [M3’s isolation criterion](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/roadmap.md#L45) exercises two tenants’ enrollment, inventory, and key access.

   **Failure scenario:** An enrollment authorization defect exposes another tenant’s authority. More broadly, compromise of the shared signer reaches multiple customer CA keys. A control-plane compromise could also manufacture apparently approved requests unless the signer’s independent checks and credentials constrain that path. Encrypting tenant payloads separately does not establish which of these attacks the deployment survives.

   **Resolution:** State the initial deployment’s threat boundary and operator powers plainly. Select pilot customers who accept that boundary. Establish which component can approve issuance, change profiles/grants, unwrap keys, and invoke each authority; exercise cross-tenant identifiers, jobs, exports, and provider handles. If the chosen buyer requires compartmentalization, move the relevant isolation capability earlier instead of relying on a later milestone.

   **Timing:** **Before scaffolding:** choose the trust boundary and component responsibilities. **Before pilot:** verify authorization paths and confirm customer acceptance. Optional isolation tiers remain **later** only for customers whose requirements allow that.

5. **Signer fencing needs a defined safety promise before the distributed design can be judged achievable.**

   **Evidence:** [Architecture](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/architecture.md#L52) requires generation checks at signing and explicitly warns about a pause before the side effect. It also declines exactly-once HSM execution at [line 56](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/architecture.md#L56). [M1](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/roadmap.md#L20) requires a design; [M3](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/roadmap.md#L50) requires verified stale-worker behavior.

   **Failure scenario:** A signer validates generation 12, pauses, and resumes after generation 13 takes ownership or the authority is suspended. It still has access to the provider and can produce a signature. Rejecting its later database commit addresses result publication but does not erase that signature. Conversely, two provider executions for the same approved certificate inputs need not represent two distinct issuance decisions. Those outcomes require different guarantees.

   **Resolution:** Specify whether the safety boundary concerns provider execution, durable issuance, result release, or all three, including already-authorized operations during suspension. Choose the smallest workable initial topology. Workers should obtain protected operations through a signer that controls execution; failover must address the old signer’s continuing access. Use pause/partition tests at authorization, provider invocation, completion, and persistence boundaries.

   **Timing:** **Before scaffolding:** define the safety contract and initial concurrency topology. **Before pilot:** demonstrate interrupted operation and takeover behavior. Scaling can remain **later**.

6. **Bootstrap and disaster recovery are not yet a complete operating model—and database rollback can invalidate the fencing assumptions.**

   **Evidence:** [Custody requirements](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/architecture.md#L71) defer unlock, wrapping-key rotation, backup, restore, and deletion behavior. [M2](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/roadmap.md#L35) requires preservation of metadata/provider references. Credential bootstrap is an [open lifecycle rule](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/domain-model.md#L104); durable ownership generations live in PostgreSQL.

   **Failure scenario:** An offline installation loses its cluster. The operator restores encrypted payloads and database metadata but lacks the wrapping-key recovery material or working administrative identity. In another scenario, PostgreSQL is restored to a point before a revocation or ownership change while certificates issued after that point remain deployed. Restored counters, grants, and revocation state are internally consistent yet disagree with the outside world. PostgreSQL [PITR explicitly restores earlier database state](https://www.postgresql.org/docs/current/continuous-archiving.html); the consequence for CA state is an inference that FireCA must address.

   **Resolution:** Define an initial cold-start and recovery story covering administrator bootstrap, service TLS/trust, software-key unlock, independent recovery material, and restart expectations. Treat CA recovery as reconciliation with irreversible external history. Choose how restoration invalidates old signer access and how missing issuance/revocation history affects continued operation. Run a synthetic cold rebuild and rollback drill.

   **Timing:** **Before scaffolding:** decide bootstrap and recovery ownership. **Before pilot:** complete the offline rebuild/rollback drill. M8 should extend this foundation for additional deployment profiles.

7. **Revocation and issuer retirement need a supported relying-client contract, not merely publication success.**

   **Evidence:** [Architecture](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/architecture.md#L81) preserves old issuer revocation capabilities and recognizes relying-client behavior. [M3](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/roadmap.md#L51) demonstrates CRLs “where supported”; fuller rollover/freshness verification appears at [M8](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/roadmap.md#L113).

   **Failure scenario:** An administrator revokes a compromised mTLS credential and publishes a fresh CRL, while the application continues accepting it. Go’s [standard `Certificate.Verify` explicitly performs no revocation checking](https://pkg.go.dev/crypto/x509#Certificate.Verify). Separately, a tenant is offboarded or an issuer retired while certificates containing its publication URLs are still valid. Publication infrastructure disappears before those certificates stop being used.

   **Resolution:** Select the first relying clients and state their actual revocation guarantees, including failure behavior when status is unavailable. Choose supported validity/renewal policies accordingly. Define publication URL lifetime, freshness ownership, old-issuer retention, and tenant exit obligations before issuing pilot certificates. Test compromised-key revocation and old/new issuer coexistence using those clients. CRL freshness has an explicit `nextUpdate` obligation in [RFC 5280](https://www.rfc-editor.org/rfc/rfc5280.html#section-5.1.2.5).

   **Timing:** **Before pilot:** establish and demonstrate this contract. Broader client/protocol coverage can remain **later**.

8. **Offline licensing can create a deferred certificate outage unless continuity and exit behavior are settled.**

   **Evidence:** Organizational use is paid under [licensing policy](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/licensing.md#L7); disconnected licenses are locally verified at [line 44](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/licensing.md#L44). Expiry behavior is explicitly unresolved at [line 46](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/docs/licensing.md#L46). The current [LICENSE](https://github.com/benhook1013/FireCA/blob/e5126db1271c80c810ae81ffec52b1a953eff7dd/LICENSE#L3) grants neither the intended personal use nor organizational evaluation rights.

   **Failure scenario:** A disconnected customer cannot complete procurement before its entitlement expires. Existing certificates remain cryptographically valid, but renewal stops; a later outage follows. If expiry also stops CRL signing/publication, relying services may fail earlier or accept stale status. Recovery after a disaster can encounter the same problem with an expired license and unreachable vendor.

   **Resolution:** Preserve paid organizational use, but specify behavior for expiry, subscription cancellation, vendor unavailability, recovery, data/key export, and ongoing revocation service. Give organizational evaluators explicit rights and define contribution/modification terms before accepting outside work. Test license expiry as an operational incident, including recovery from offline backup.

   **Timing:** **Before pilot:** finalize evaluation rights and the continuity/exit contract. **Before implementing license checks:** settle enforcement behavior. Pricing refinement can remain **later**.

Several choices survive scrutiny: avoiding public-root admission, CSR-first enrollment, customer-signed issuing authorities, separate CA/application/standalone-key permissions, immutable certificate history, and separating issuance from observed deployment. PostgreSQL authority with Redis limited to temporary coordination is also a sound direction. Java and maintained PKI libraries are reasonable foundations.

The repository cannot establish willingness to pay, acceptable migration cost, delivery/support capacity, native Windows compatibility, recovery guarantees, provider interoperability, or throughput. Those remain unanswered by documentation, including documentation that names the right mitigations.

The minimum next work is to identify one paid pilot outcome; make the engine decision; secure and scope the Windows experiment; and record the initial signer, recovery, revocation, and licensing continuity contracts. Proceed with a narrow synthetic vertical slice after those choices. A stop decision would be justified if prospective buyers require capabilities that the team cannot bring forward, or if the engine/Windows experiments expose an unaffordable compatibility obligation.
