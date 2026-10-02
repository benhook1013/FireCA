# Delivery and evidence status

Last updated: 2 October 2026. This is the execution record, not a claim that the planned application exists.

## Current phase

| Item | Status | Evidence or next action |
| --- | --- | --- |
| Repository/bootstrap and design | Complete documentation artifact | Public repository, existing baseline and review commits |
| Initial independent reviews | Complete design review | [Two original reports](reviews/2026-10-02-initial/README.md) |
| Independent resolution proposals | Complete design review | [Two Sol High reports](reviews/2026-10-02-resolutions/README.md) |
| F0 adjudication/process | Complete documentation checkpoint | [ADRs 0004–0007](decisions/README.md), [process](implementation-process.md), [pilot hypothesis](pilot-charter.md) |
| F1 supported runtime/scaffold baseline | Not started | Record actual supported versions/build/modules before executable scaffolding |
| F1 engine comparison | Not started | One EJBCA representative workflow, responsibility/terms/failure comparison |
| F2 synthetic lifecycle slice | Not started | Two tenants/authorities, CSR/API/ACME, CRL, rollover, observer and failure cases |
| F3 real Windows lab | Availability unverified | Record lab owner/access, supported builds and experiment configuration |
| F3 unattended enrollment evidence | Not started | Real clients and intended service; no mocked/manual-only completion |
| F4 offline operating/recovery drill | Not started | Run chosen-profile install/rebuild/history/entitlement/status scenarios |
| First usable CA release | Not implemented | [M5 acceptance](roadmap.md#m5-initial-usable-ca-release) |
| Actual customer/pilot | Not selected | Qualify [charter](pilot-charter.md) hypothesis before commercial validation |
| Formal license/evaluation/contributor terms | Pending | [Licensing policy](licensing.md) is accepted intent, not a license grant |

## Finding resolution and closure

| Finding | Accepted design disposition | Required evidence | Verification status |
| --- | --- | --- | --- |
| R1 | [0004](decisions/0004-delivery-and-ca-ownership.md): internal TLS hypothesis and coexistence | Actual buyer/operator baseline, requirements and value | Open; no selected buyer |
| R2 | [0004](decisions/0004-delivery-and-ca-ownership.md): FireCA-owned core and bounded comparison | F1 engine/responsibility/rights matrix | Open |
| R3 | [0005](decisions/0005-software-signing-and-recovery.md): trusted-platform tier and privilege boundaries | Tenant/job/export/provider tests and pilot trust acceptance | Open |
| R4 | [0005](decisions/0005-software-signing-and-recovery.md): execution/result distinction and recovery quarantine | Pause/partition/rollback/exclusion drills | Open |
| R5 | [0005](decisions/0005-software-signing-and-recovery.md): PostgreSQL payloads, unlock profiles and recovery | Fresh offline bootstrap/rebuild/rotation/copy-loss drill | Open |
| R6 | [0006](decisions/0006-enrollment-observation-and-trust.md): domain-local native path | F3 unattended machine/user and intended-use evidence | Open; lab unverified |
| R7 | [0006](decisions/0006-enrollment-observation-and-trust.md): DNS-01 contexts; hosted validator gate | Independent clients, DNS overlap/replay/routing and CSR agreement | Open |
| R8 | [0006](decisions/0006-enrollment-observation-and-trust.md): client trust, CRLs and issuer retention | Actual rejection/failure/rollover/exit behavior | Open |
| R9 | [0007](decisions/0007-license-and-exit-continuity.md): perpetual entitled-version use and bounded hosted exit | Formal terms and F4 entitlement/maintenance/offline/exit drills | Open |
| R10 | [0004](decisions/0004-delivery-and-ca-ownership.md), [0006](decisions/0006-enrollment-observation-and-trust.md): early TLS observer | Issued-but-undeployed/stale/correct-SNI observations | Open |

Implementation owners are the active development agent and project owner for external/customer/legal inputs. Update each row with concrete evidence when available; do not fabricate an owner, lab, commercial agreement or completed result to clear a gate.

Documentation verification for F0 checked 30 UTF-8 files, 90 local links, one local anchor, 60 frozen line references and all ten finding dispositions. Technical findings are retained; personal context and historical references use privacy-sanitized equivalents. Application/interoperability/failure drills remain unrun.
