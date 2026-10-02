# Implementation process

- Status: accepted process following user-delegated adjudication on 2 October 2026
- Decisions: [ADR index](decisions/README.md)
- Delivery sequence: [roadmap](roadmap.md)
- Execution record: [delivery status](delivery-status.md)

## Work cycle

For each bounded work item, identify the customer/engineering outcome, affected accepted contract and verification evidence. Choose the smallest implementation that meets those contracts. Update code, relevant contracts and evidence together.

Record runnable commands and actual outcomes. A planned test, self-parsed certificate, manual Windows request, Kubernetes replica count or valid license signature does not establish the broader gate it resembles.

Routine reversible implementation, synthetic experiments and documentation proceed within accepted scope. Do not repeatedly ask the user to choose ordinary implementation details. Document consequential decisions as ADRs. Evidence that invalidates an accepted choice requires a visible superseding decision and updated affected gates.

## Feasibility checkpoints

| Checkpoint | Work and required exit evidence | Dependency |
| --- | --- | --- |
| F0: Adjudicated design | Accepted ADRs 0004–0007, pilot hypothesis, process and finding/evidence map | Documentation checkpoint; complete when repository checks pass |
| F1: Runtime and engine comparison | Record supported runtime/build/library versions, module boundaries and identity bootstrap; compare one EJBCA workflow with the selected FireCA-owned responsibilities, exact terms and failure behavior | Before executable scaffold: runtime record. Before core issuance contracts freeze: engine comparison |
| F2: Synthetic lifecycle slice | Two tenants/authorities, external-root workflow, constrained CSR/API/ACME issuance, revocation/rollover, endpoint observation and meaningful tenant/failure cases | Minimal scaffold permitted during F1; uses M1–M5 modules incrementally |
| F3: Native Windows feasibility | Real patched domain/client matrix, domain-local XCEP/WSTEP, unattended machine/user enrollment and renewal, permission/identity/outage tests and intended use | Secure lab early; independent of unrelated F2 progress; before Windows contracts freeze/pilot |
| F4: Operating drill | Network-denied install/restart/upgrade/rebuild, wrapping rotation, lost recovery copy, old-signer exclusion, incomplete-history restore/quarantine, entitlement and status continuity | Before usable CA release/organizational pilot for the chosen profile |

F2 and F3 may run in parallel as work streams. F3 remains unmet if the lab is unavailable. A synthetic prototype can use compressed validity/timing to demonstrate events; record that substitution and separately test realistic settings before asserting production behavior.

Keep engine comparison bounded to one candidate, two authorities and the representative workflow. Record supported, unsupported and unresolved behavior rather than implementing a second complete product. Review the result before expanding scope. Time estimates from reviews are not release commitments.

## Evidence record

Place each experiment's record in `docs/evidence/<experiment-id>/README.md` when it actually runs. Record:

- Outcome, finding IDs, ADR/gate, tested commit, commands and expected/actual results.
- Exact implementation/client/dependency versions, deployment topology and clock/network assumptions.
- Synthetic identity/material provenance and how results were independently checked.
- For failures: injection point, provider execution uncertainty, database state, published result and recovery.
- Limitations, remaining cases and a disposition: pass, fail, blocked or unsupported for the declared profile.

Only commit explicitly synthetic, documented fixtures. Never commit live keys, credentials, tokens, private bundles, customer traces or database dumps. Evidence records can link safely redacted artifacts; sensitive artifacts remain outside the public repository.

Write commands and paths relative to the checkout. Redact personal identities, home directories, workstation identifiers and private conversational background from records and generated reviewer reports. Inspect staged content and author/committer metadata before publishing; local setup instructions belong outside the public repository.

Update [delivery status](delivery-status.md) with evidence links and residual gaps. Accepted design, implemented code and verified behavior are separate states. Do not close a finding solely because an ADR names its mitigation.

## Contract-specific evidence

| Area | Required independent/failure evidence |
| --- | --- |
| CA/crypto | Independent clients, constrained CSR/extension behavior, issuer/key matching, complete chains and intended usages |
| Tenancy | Substituted tenant/resource/provider handles; queued/retried operations; grant removal and stale caches; export/observation denials |
| Signing | Admission/provider/persistence/release pauses, database partition/ambiguous commit, suspension, repeated work and no uncommitted-result exposure |
| Recovery | Old signer exclusion, pre-revocation restore, complete-history assessment, quarantine/distrust fallback, wrapping/backups and offline bootstrap |
| ACME | Independent enrollment/renewal/revocation clients; identifier grants/order-CSR agreement; overlapping DNS views, replay and connector context |
| Windows | Unattended Group Policy machine/user issuance/renewal and actual relying use; manual enrollment is diagnostic evidence only |
| Lifecycle | Issued replacement left uninstalled; fresh correct-SNI observation; stale/unreachable state and durable alert/delivery behavior |
| Trust/status | Wrong-tenant identity, revoked/stale/unavailable status, measured publication versus client rejection delay, rollover/exit retention |
| Commercial offline | Valid perpetual-version operation after maintenance ends, invalid-file recovery, offline artifacts/verification-key transition and bounded hosted exit |

## Release and pilot gates

The first usable CA release requires HTTP API, basic local portal, standard ACME, inventory/one TLS observer, tenant authorization, software custody, selected-client trust/status behavior and the demonstrated operating/recovery profile. Browser/custodial flows follow their declared phased criteria and cannot weaken CSR or CA-key boundaries.

A Windows pilot also requires F3 and the supported production matrix. A hosted private-only DNS offer requires its scoped validator. An HSM/isolation-required pilot requires that provider/boundary first. Broader HA, algorithms, stores and connectors expand supported profiles rather than silently broadening claims.

Before the first usable release, obtain independent implementation/evidence review from at least two fresh subagents, using Sol High unless the user directs otherwise. Give both the same frozen implementation and evidence; preserve reports and adjudicate findings. Repeat at a later consequential gate when new trust, signing, recovery or protocol behavior warrants it. This documentation adjudication does not substitute for that implementation review.

A named organizational pilot additionally needs the charter's buyer/operator acceptance, required integrations/assurance, explicit paid entitlement, continuity terms and measurable baseline. Synthetic engineering can proceed before those commercial prerequisites; production deployment cannot be represented as authorized by the current reservation notice.

## Current next action

Record the supported runtime/build/library baseline and engine comparison plan (F1); identify the actual Windows lab dependency (F3). Then develop the bounded synthetic slice. No experiment or runnable application is complete at this documentation checkpoint.
