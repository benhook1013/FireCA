# Standards and implementation references

These primary references guide implementation and interoperability work. A reference being listed does not imply that its entire protocol or optional feature set is committed for FireCA. Check supported versions and current requirements when implementing integrations.

## Certificate and key formats

- [RFC 5280: X.509 certificates, CRLs, and path validation](https://www.rfc-editor.org/rfc/rfc5280.html)
- [RFC 2986: PKCS#10 certificate signing requests](https://www.rfc-editor.org/rfc/rfc2986.html)
- [RFC 5958: Asymmetric key packages / updated PKCS#8 structures](https://www.rfc-editor.org/rfc/rfc5958.html)
- [RFC 7292: PKCS#12 personal identity exchange](https://www.rfc-editor.org/rfc/rfc7292.html)
- [RFC 8555: ACME](https://www.rfc-editor.org/rfc/rfc8555.html)

## Native Windows enrollment

- [Microsoft: Certificate enrollment methods](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cersod/444f0375-3cf6-4bdf-b41b-18eed3ba6b54)
- [Microsoft: Autoenrollment in a domain environment](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cersod/dd492d51-9c18-4d52-a8db-e9cfe35a80b2)
- [Microsoft: XCEP/WSTEP domain enrollment example](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-cersod/e35f4aaa-9087-4513-845a-aabb7eb12418)
- [Microsoft: MS-WSTEP protocol](https://learn.microsoft.com/en-us/openspecs/windows_protocols/ms-wstep/)
- [Microsoft: Configure Certificate Enrollment Policy Web Service](https://learn.microsoft.com/en-us/windows-server/identity/ad-cs/configure-certificate-enrollment-policy-web-service)

## Libraries and infrastructure

- [Bouncy Castle Java documentation](https://www.bouncycastle.org/documentation/documentation-java/)
- [Bouncy Castle formats and standards](https://www.bouncycastle.org/documentation/specification_interoperability/)
- [Java PKCS#11 integration guide](https://docs.oracle.com/en/java/javase/25/security/pkcs11-reference-guide1.html)
- [Spring Boot](https://spring.io/projects/spring-boot/)
- [PostgreSQL locking](https://www.postgresql.org/docs/current/explicit-locking.html)
- [Redis distributed locking and lease limitations](https://redis.io/docs/latest/develop/clients/patterns/distributed-locks/)

## CA engine evaluation

- [EJBCA architecture and multiple-authority support](https://docs.keyfactor.com/ejbca/9.3.2/ejbca-architecture)
- [Smallstep CA concepts](https://smallstep.com/docs/step-ca/certificate-authority-core-concepts/)

Existing engines are evaluation candidates, not selected dependencies. Assess feature editions, custody/provider contracts, isolation, interoperability, and licensing before adopting one beneath FireCA.

## Public trust and licensing context

- [Mozilla root-store policy](https://www.mozilla.org/en-US/about/governance/policies/security-group/certs/policy/)
- [CA/Browser Forum current TLS baseline requirements](https://cabforum.org/working-groups/server/baseline-requirements/requirements/)
- [Open Source Definition](https://opensource.org/osd)
- [PolyForm Noncommercial text](https://polyformproject.org/licenses/noncommercial/1.0.0)
- [PolyForm license-text modification rules](https://github.com/polyformproject/polyform-licenses/blob/1.0.0/README.md)

FireCA's own public CA operation is outside the accepted scope. The licensing references explain why existing options were evaluated; none has been adopted as FireCA's formal software license.
