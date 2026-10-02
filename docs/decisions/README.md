# Architecture decisions

Decision records preserve choices that have been explicitly agreed. Proposed details in the design documents remain open to implementation evidence.

| Record | Status | Decision |
| --- | --- | --- |
| [0001: Platform and storage](0001-platform-and-storage.md) | Accepted | Java/Spring Boot, Bouncy Castle, PostgreSQL authority, Redis coordination, container deployments |
| [0002: Trust and custody](0002-trust-and-custody.md) | Accepted | Private customer CAs, external public providers, CSR-first enrollment, independent key/certificate resources |
| [0003: Commercial model](0003-commercial-model.md) | Accepted policy | Source availability, free personal non-commercial use, paid organizational use without category exemptions |
| [0004: Delivery and CA ownership](0004-delivery-and-ca-ownership.md) | Accepted | Internal TLS lifecycle first, FireCA-owned core, bounded engine comparison and early observation |
| [0005: Software signing and recovery](0005-software-signing-and-recovery.md) | Accepted | Trusted-platform tier, one shared signer, explicit execution/release guarantees, automatic or manual unlock and safe recovery |
| [0006: Enrollment, observation and trust](0006-enrollment-observation-and-trust.md) | Accepted | DNS-01 topology, native Windows experiment, early observer, CRLs and measured relying-client behavior |
| [0007: License and exit continuity](0007-license-and-exit-continuity.md) | Accepted policy | Continued purchased-version operation, maintenance separation, offline entitlement and 90-day hosted transition |

New records should describe the trigger, concrete decision, consequences, and validation or unresolved implementation details. A decision being accepted does not mean its implementation is complete.
