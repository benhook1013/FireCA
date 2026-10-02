# 0006: Enrollment topology, observations and relying-party contract

- Status: accepted
- Date: 2 October 2026
- Findings: R6, R7, R8 and R10.

## ACME decision

Implement standard DNS-01 first. Self-hosted validation uses operator-configured local DNS contexts and permitted namespaces. Initial hosted validation can use publicly reachable challenge records for authorized names while application endpoints remain private.

Private-only hosted DNS requires an authenticated, scoped outbound tenant-local validator before that mode is advertised. Design its typed task/result boundary early; its implementation follows the first self-hosted slice. Validation and observation can share transport, with separate grants and message types.

Bind task/cache/result state to tenant, authority, profile/policy version, account-key identity, order/authorization, identifier, challenge/token/expected value, validation context and expiry. Server-side policy selects resolver/connector scope. EAB account association and a successful challenge do not replace identifier authorization. Enforce final CSR/order identifier agreement.

Start with exact DNS identifiers. Wildcards, IP identifiers, HTTP-01 and other challenges require a recorded use case and supported validation behavior. DNS alias/delegation behavior is bounded and tested. DNS-01 uses TXT evidence and does not inherently require an accessible application endpoint. [RFC 8555](https://www.rfc-editor.org/rfc/rfc8555.html#section-8.4)

## Native Windows decision

Pursue XCEP/WSTEP through a customer-local domain integration, initially one forest, intranet-connected native clients and one recorded supported configuration. A Windows-appropriate server connector may perform integrated authentication and directory lookup while FireCA's primary backend remains Java.

This is the selected experiment path, not a compatibility claim. Secure a real patched Windows/AD lab early. Test unattended machine and user enrollment/renewal through Group Policy, reboot/logon, policy changes, permission removal, directory disable/rename and gateway outages. Use directory-derived stable identities; caller fields cannot override them. Demonstrate actual use by the intended relying service.

For domain certificate authentication, test current strong mapping and required trust registration. That condition does not apply to every server-TLS profile. No mandatory FireCA PC agent; optional inventory agents do not satisfy enrollment. DCOM parity, cross-forest, internet initial enrollment and key archival are deferred surfaces.

A missing lab leaves this gate visibly unmet; it does not prevent unrelated CA/ACME engineering. A Windows pilot requires complete native and intended-use evidence. [Microsoft autoenrollment](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cersod/dd492d51-9c18-4d52-a8db-e9cfe35a80b2), [domain authentication mapping](https://support.microsoft.com/en-us/servicing/os/windows-server/2022/05/kb5014754-certificate-based-authentication-changes-on-windows-domain-controllers)

## Observation and trust decision

Bring one explicitly registered TLS endpoint observer into the first lifecycle release. Identify host/address, protocol, port and SNI; retain source, timestamp, certificate fingerprint and reachability. Separate assignments, issuance and observations. A replacement left undeployed produces a distinct condition; unreachable/stale evidence cannot mark a deployment healthy.

Choose a representative TLS client and a configured mTLS verifier before pilot certificates. Record trust installation, identity/issuer mapping, usages, revocation checking, cache/reload behavior and status failure handling. Shared verifiers must preserve authorized trust-domain/issuer context when mapping repeated subject identities.

Use complete CRLs initially. Require stable customer-controlled self-hosted publication names with issuer-version paths independent of pod/portal hostnames. Keep old issuer keys, CRL numbers, history and publication through the last outstanding certificate expiry plus recorded retention/cache margin. Suspension, offboarding and maintenance expiry do not silently remove status service.

## Experimental defaults

| Setting | Initial synthetic TLS value |
| --- | --- |
| Leaf validity | 30 days |
| Renewal begins | 10 days before expiry |
| Scheduled CRL regeneration | Hourly, with additional generation on accepted revocation |
| CRL nextUpdate interval | 24 hours from thisUpdate |
| Revocation-to-publication target | Within five minutes under the measured normal operating profile |
| TLS observation poll | Every five minutes |
| Observation becomes stale | After fifteen minutes without a successful observation |

These are accepted starting experiment settings, not production SLOs or universal profile requirements. The 24-hour CRL window accommodates short service interruptions while frequent publication enables configured clients to refresh sooner. Publication latency does not establish client rejection latency.

Before a pilot, measure and record the supported client's actual maximum rejection delay and stale/unavailable-status behavior. A stricter risk/availability requirement changes the profile and operating prerequisites. Clients without effective status checking retain exposure through certificate expiry; accept that explicitly or select/configure a suitable verifier. Short lifetime is not immediate revocation.

CRL freshness uses thisUpdate/nextUpdate; issuer publication alone does not prove a client enforces it. [RFC 5280](https://www.rfc-editor.org/rfc/rfc5280.html#section-5.1.2.5)

## Validation remaining

Independent ACME clients; overlapping tenant names/DNS views; resolver/connector substitution; expired/replayed results; namespace denial; CSR/order mismatch; real unattended Windows events and intended use; wrong-tenant identity rejection; revoked/stale/missing-status behavior; rollover/retirement/exit; SNI selection and a renewed certificate deliberately left undeployed.
