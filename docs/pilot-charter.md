# First outcome and pilot hypothesis

- Status: accepted engineering focus; commercial hypothesis unvalidated
- Decision: [0004](decisions/0004-delivery-and-ca-ownership.md)
- Actual buyer/design partner: not selected
- Windows lab and production infrastructure access: not verified

## Outcome

An organization uses its existing root to authorize a FireCA issuing authority, enrolls internal HTTPS services through API/ACME, renews certificates and sees whether replacements reached their assigned endpoints.

The first engineering slice runs self-hosted on one supported container/Kubernetes profile, serves two synthetic tenants with independent authorities, and observes registered TLS endpoints. Use client-generated keys and CSR enrollment. Bootstrap an administrator and service TLS without requiring the production issuer.

## Buyer and environment hypothesis

The initial buyer is an infrastructure/platform team with internal TLS renewal and visibility problems, including restricted-network organizations. No customer commitment, willingness to pay or universal government assurance requirement is inferred.

Assume for the first slice: customer-owned root, local DNS validation, explicitly installed trust, accepted trusted-platform software custody, and local operation without internet. A commercial pilot must confirm each assumption. Bring a required HSM, stronger isolation or complete native Windows support forward for a buyer that needs it.

## Coexistence and trial sequence

1. Import existing public certificate/inventory data without requiring its private keys.
2. Obtain customer authorization for a separate issuing authority using a customer-signed issuer CSR.
3. Move one noncritical internal service; retain the incumbent hierarchy and a documented exit path.
4. Renew while deliberately leaving the replacement undeployed; detect that condition, install it, and confirm by observation.
5. Exercise selected-client revocation/rollover and the offline operating/recovery procedure.

Broad discovery, automatic installation, complete AD CS replacement and general secrets are subsequent outcomes. Native Windows remains required and has an early parallel real-client experiment.

## Measures to establish

| Measure | Evidence |
| --- | --- |
| Setup and coexistence effort | Recorded installation/trust/DNS/issuer configuration steps and elapsed operator time |
| Renewal effort | Existing-process baseline compared with the demonstrated FireCA/client workflow |
| Undeployed-replacement detection | Issuance, observation and alert timestamps; correct endpoint/SNI evidence |
| Recovery burden | Fresh rebuild and rollback/quarantine drill with elapsed time and operator prerequisites |
| Operational acceptance | Named operator accepts supported custody, revocation, offline and support boundaries |
| Commercial relevance | Actual buyer accepts the outcome, switching effort and explicit paid entitlement |

Customer qualification is needed before commercial validation, not before every synthetic coding experiment. Any outreach is a separate explicitly authorized action; none has been performed.

## Organizational pilot entry

Require a named entity/operator/environment, recorded current workflow and acceptance criteria, explicit paid/evaluation agreement, supported-client trust/revocation evidence, custody acceptance, tested offline recovery, and the complete API/basic portal/ACME release. Add complete Windows/HSM/isolation evidence whenever that pilot depends on it.
