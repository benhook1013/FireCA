# Architecture decisions

Decision records preserve choices that have been explicitly agreed. Proposed details in the design documents remain open to implementation evidence.

| Record | Status | Decision |
| --- | --- | --- |
| [0001: Platform and storage](0001-platform-and-storage.md) | Accepted | Java/Spring Boot, Bouncy Castle, PostgreSQL authority, Redis coordination, container deployments |
| [0002: Trust and custody](0002-trust-and-custody.md) | Accepted | Private customer CAs, external public providers, CSR-first enrollment, independent key/certificate resources |
| [0003: Commercial model](0003-commercial-model.md) | Accepted policy | Source availability, free personal non-commercial use, paid organizational use without category exemptions |

New records should describe the trigger, concrete decision, consequences, and validation or unresolved implementation details. A decision being accepted does not mean its implementation is complete.
