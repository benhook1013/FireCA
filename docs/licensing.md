# Licensing policy

**Status:** accepted business policy; formal license terms pending. This document is not a license grant.

## Accepted policy

FireCA is intended to be source-available with free personal non-commercial use and paid organizational use. It is not intended to use an open-source license that permits unrestricted commercial deployment or competing hosting.

| Use | Intended entitlement |
| --- | --- |
| Individual personal, hobby, and non-commercial learning use | Free personal-use license |
| Internal business deployment | Paid organizational license |
| Government, education, research institutions, charities, NGOs, and other public-interest organizational deployment | Paid organizational license; no category-based exemption |
| Official hosted FireCA | Paid service subscription |
| Third-party commercial hosting or managed service offering | Explicit commercial service-provider rights |
| Resale or embedding FireCA in a product | Explicit commercial redistribution/embedding rights |

An individual running FireCA on behalf of an employer or institution is using it for that organization. That is not the intended free personal-use category. An ordinary internal-use license must not automatically grant resale, hosted-service, or embedding rights.

The policy does not assign prices or promise that every commercial agreement has identical terms.

## License selection

Stock PolyForm Noncommercial is not a match: it grants general non-commercial use and expressly permits several organizational categories, including government and education. Merely deleting the organizational examples would not narrow its general permission to personal use. [PolyForm Noncommercial](https://polyformproject.org/licenses/noncommercial/1.0.0)

If an adapted license text is used, it needs its own name and accurate terms. PolyForm's modification rules require removing its name and project-domain references from modified license text. [PolyForm license-text rules](https://github.com/polyformproject/polyform-licenses/blob/1.0.0/README.md)

The current root `LICENSE` is a copyright/reservation notice while formal terms are prepared. It does not yet grant the intended free personal-use permissions. Public repository availability and the platform's own terms do not finalize FireCA's software licensing.

## Formal terms to prepare

- The licensor identity and applicable software/documentation coverage.
- The precise personal-use grant and boundary for work on behalf of organizations.
- Organizational entitlement and internal deployment rights.
- Service-provider, resale, embedding, and redistribution rights.
- Treatment of evaluation, personal contributions, modifications, and source redistribution.
- Contributor permissions compatible with the intended commercial licensing rights.
- Attribution, warranty, support, termination, and other operative provisions.

Do not represent the policy as a finalized enforceable agreement or adopt an institutional-exemption license as a shortcut.

## Product implementation

Commercial disconnected deployments should verify signed license files locally, without mandatory online activation or periodic callbacks. The official hosted service can enforce its subscription through its own control plane.

The policy does not yet define software behavior on license expiry. Specify continuity, renewal, administration, recovery, and data-export behavior before implementing checks. Existing certificate cryptographic validity is determined by the certificate and relying-party validation, not by a FireCA subscription flag.

Pricing, enforcement mechanics, and formal terms are separate from the tenant/authority model. License checks must not become an undocumented dependency on the official hosted service.
