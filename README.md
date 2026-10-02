# FireCA

FireCA is a planned API-first, multitenant platform for private PKI, certificate lifecycle management, key custody, and secrets. The same product will support self-hosted deployments and an official hosted service, including deployments in disconnected networks.

**Status:** design and repository bootstrap. This repository currently contains the product and technical design; there is no runnable application yet. Features below describe the intended product.

## Product direction

- Operate many independent customer certificate authorities within shared infrastructure.
- Issue certificates for internal HTTPS, mutual TLS, users, computers, and other devices.
- Support CSR enrollment by default, ACME enrollment, and eventual native Windows autoenrollment.
- Provide a web portal for enrollment, inventory, and certificate downloads.
- Track certificates from FireCA and external issuers, their assignments, renewal history, expiry, and actual deployment observations.
- Support optional browser-generated and server-managed application keys, standalone keys, and general secrets.
- Integrate with external publicly trusted CAs; FireCA will not pursue its own public root-program admission.
- Introduce HSM providers and stronger deployment isolation as the product develops.

## Selected stack

| Component | Decision |
| --- | --- |
| Backend | Java and Spring Boot |
| PKI and cryptographic libraries | Bouncy Castle and Java cryptographic providers |
| Authoritative state | PostgreSQL |
| Temporary coordination and caching | Redis |
| Deployment | Containerized services; Kubernetes is the intended primary deployment target |

Runtime versions, build tooling, and initial service packaging remain implementation decisions. No language runtime, containers, or infrastructure are required to read the design.

## Documentation

Start with the [documentation index](docs/README.md).

- [Product scope and feature list](docs/product-scope.md)
- [Architecture and coordination](docs/architecture.md)
- [Domain model and lifecycle](docs/domain-model.md)
- [Implementation roadmap and acceptance criteria](docs/roadmap.md)
- [Licensing policy](docs/licensing.md)
- [Architecture decisions](docs/decisions/README.md)
- [Standards and integration references](docs/references.md)
- [Initial independent adversarial reviews](docs/reviews/2026-10-02-initial/README.md)
- [Proposed resolutions from two Sol High reviewers](docs/reviews/2026-10-02-resolutions/README.md)

## Licensing status

The agreed direction is **source-available**, with free personal non-commercial use and paid organizational use, including businesses, government, education, charities, and public-interest organizations. Commercial hosting, resale, and embedding require separately defined rights.

The formal personal-use and commercial licenses have not yet been adopted. The [licensing policy](docs/licensing.md) records business intent and does not itself grant those permissions. The current [copyright notice](LICENSE) reserves rights pending publication of formal terms. This project is not presently offered under an open-source license.

## Working on FireCA

Read [AGENTS.md](AGENTS.md) before changing the repository. Update the relevant design and decision records when an implementation changes an agreed contract. Document demonstrated behavior separately from planned capabilities.
