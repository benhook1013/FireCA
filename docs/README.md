# FireCA documentation

This is the initial design baseline, recorded on 2 October 2026. There is no application implementation yet.

## Reading order

| Document | Purpose |
| --- | --- |
| [Product scope](product-scope.md) | Customers, use cases, feature commitments, and exclusions |
| [Architecture](architecture.md) | Service responsibilities, custody boundaries, storage, coordination, and deployment |
| [Domain model](domain-model.md) | Resources, relationships, authorization, and certificate/deployment lifecycle |
| [Roadmap](roadmap.md) | Dependency order, milestones, acceptance criteria, and outstanding choices |
| [Licensing policy](licensing.md) | Free personal use, paid organizational use, and licensing work remaining |
| [Decisions](decisions/README.md) | Accepted decisions and their rationale |
| [References](references.md) | Primary standards and implementation documentation |
| [Initial adversarial reviews](reviews/2026-10-02-initial/README.md) | Two independent fresh-agent reviews, full reports, synthesis, and proposed follow-ups |
| [Proposed resolutions](reviews/2026-10-02-resolutions/README.md) | Two fresh Sol High proposals covering all findings, comparison, evidence gates, and unresolved choices |

## Status terminology

- **Accepted:** agreed product or platform direction.
- **Proposed:** an implementation design to evaluate and validate.
- **Implemented:** present in the repository as working code or a completed artifact.
- **Verified:** supported by a recorded check against the actual implementation.

The stack, core scope, trust/custody direction, native Windows requirement, and commercial policy are accepted. Resource names, service packaging, and exact concurrency mechanisms below are proposed until implemented and validated. Only the documentation and repository bootstrap are currently implemented.
