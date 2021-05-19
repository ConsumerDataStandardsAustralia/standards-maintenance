# Normative references analysis

**Review Date:** May 2021

## Overview

The following document contains a review of the normative and informative references the Consumer Data Standards rely upon. It provides a gap analysis of the existing normative and informative references in version 1.9.0 of the Consumer Data Standards compared to the latest versions of those references. Where a referenced RFC has been superseded with a newer RFC, this analysis is also provided.

This review is to be conducted bi-annualy under the DSB's Future Plan [DSB Item - Normative Standards Review](https://github.com/ConsumerDataStandardsAustralia/future-plan/issues/34).

A previous external FAPI specification analysis was conducted by the OpenID Foundation in January 2020. It can be found [here](https://bitbucket.org/openid/fapi/src/master/cds-spec-analysis/).

## Summary of changes
This document will summarise the key differences of the proposed Consumer Data Standards when compared against the normative references adopted. The Consumer Data Standards are the underlying technical standards to deliver on the Australian Government's Consumer Data Right legislation, which was passed 1 August 2019.

Non-breaking and breaking changes will be documented here. Where changes result in breaking changes to Data Recipients and/or Data Holders, consultation will be conducted on these changes to specifically address the transition arrangements, technical standards solution and the obligation dates to be considered.

## Outcomes

Performing this review achieves several outcomes, notably:
* Ensures the Consumer Data Standards are current and maintain interoperability with supported international standards
* Identify any breaking changes due to the uplift to current international standards and determine transition stages and obligation dates arising from those changes to transition the ecosystem in a stable and reliable way
* Reduce legacy maintenance costs and technical debt
* Maintain improved vendor support for implementers
* Maintain interoperability within the CDR and internationally

## Normative References

The following normative references were current at the time of review. For each reference, the version relied upon in the Consumer Data Standards as well as the most current version available of the reference are listed.

| **Reference**  | **Description**  | **Baseline Version** | **05-2021 Version** | **Spec Diff** | **Spec Analysis** |
| --- | --- | --- | --- | --- | --- |
| <a id="FAPI-R"></a>**[FAPI-R]**    | Financial-grade API - Part 1: Read Only API Security Profile:                                                     |[Draft-06](https://openid.net/specs/openid-financial-api-part-1-ID2.html) | [1.0 Final - March 12, 2021](https://openid.net/specs/openid-financial-api-part-1-1_0.html) | pending | pending |
| <a id="FAPI-RW"></a>**[FAPI-RW]**  | Financial-grade API - Part 2: Read and Write API Security Profile |[Draft-06](https://openid.net/specs/openid-financial-api-part-2-ID2.html) | [1.0 Final - March 12, 2021](https://openid.net/specs/openid-financial-api-part-2-1_0.html) | pending | pending |
| <a id="JSON"></a>**[JSON]**        | The JavaScript Object Notation (JSON) Data Interchange Format | [Dec 2017](https://tools.ietf.org/html/rfc8259) | No change | N/A | N/A |
| <a id="JWA"></a>**[JWA]**          | JSON Web Algorithms (JWA) | [May 2015](https://tools.ietf.org/html/rfc7518) | No change |N/A | N/A |
| <a id="JWK"></a>**[JWK]**          | JSON Web Key (JWK) | [May 2015](https://tools.ietf.org/html/rfc7517) | No change |N/A | N/A |
| <a id="JWT"></a>**[JWT]**          | JSON Web Token (JWT) | [May 2015](https://tools.ietf.org/html/rfc7519) | No change |N/A | N/A |
| <a id="JWS"></a>**[JWS]**          | JSON Web Signature (JWS) | [Feb 2016](https://tools.ietf.org/html/rfc7797) | No change |N/A | N/A |
| <a id="JWE"></a>**[JWE]**          | JSON Web Encryption (JWE) | [May 2015](https://tools.ietf.org/html/rfc7516) | No change |N/A | N/A |
| <a id="MTLS"></a>**[MTLS]**        | OAuth 2.0 Mutual TLS Client Authentication and Certificate Bound Access Tokens | [Feb 2020](https://tools.ietf.org/html/rfc8705) | No change |N/A | N/A |
| <a id="OAUTH2"></a>**[OAUTH2]**    | The OAuth 2.0 Authorization Framework | [Oct 2012](https://tools.ietf.org/html/rfc6749)| No change |N/A | N/A |
| <a id="OIDC"></a>**[OIDC]**        | OpenID Connect Core 1.0 incorporating errata set 1 | [Nov 2014](http://openid.net/specs/openid-connect-core-1_0.html) | No change |N/A | N/A |
| <a id="OIDD"></a>**[OIDD]**        | OpenID Connect Discovery 1.0 incorporating errata set 1 | [Nov 2014](http://openid.net/specs/openid-connect-discovery-1_0.html)| No change |N/A | N/A |
| <a id="TDIF"></a>**[TDIF]**        | Digital Transformation Agency - Trusted Digital Identity Framework | [Apr 2019](https://www.dta.gov.au/our-projects/digital-identity/trusted-digital-identity-framework) | **_in review_** | pending | pending |
| <a id="RFC2119"></a>**[RFC2119]**  | Key words for use in RFCs to Indicate Requirement Levels | [RFC2119 - Mar 1997](https://tools.ietf.org/html/rfc2119) | [RFC8175 - May 2017](https://datatracker.ietf.org/doc/html/rfc8174) | [diff-rfc2119-rfc8175](./raw/diff-rfc2119rfc8174.txt) | [analysis-rfc2119-rfc8174-20210519](./analysis/analysis-rfc2119-rfc8174-20210519.md) |
| <a id="RFC7009"></a>**[RFC7009]**  | OAuth 2.0 Token Revocation | [Aug 2013](https://tools.ietf.org/html/rfc7009) | No change |N/A | N/A |
| <a id="RFC7523"></a>**[RFC7523]**  | JSON Web Token (JWT) Profile for OAuth 2.0 Client Authentication and Authorization Grants | [May 2015](https://tools.ietf.org/html/rfc7523) | No change |N/A | N/A |
| <a id="RFC7662"></a>**[RFC7662]**  | OAuth 2.0 Token Introspection | [RFC7662 - Oct 2015](https://tools.ietf.org/html/rfc7662) | [RFC8996 - March 2021](https://datatracker.ietf.org/doc/html/rfc8996) | pending | pending |
| <a id="RFC6750"></a>**[RFC6750]**  | The OAuth 2.0 Authorization Framework: Bearer Token Usage | [Oct 2012](https://tools.ietf.org/html/rfc6750) | No change |N/A | N/A |
| <a id="PAR"></a>**[PAR]**          | OAuth 2.0 Pushed Authorization Requests | [Draft 01 - Feb 2020](https://tools.ietf.org/html/draft-ietf-oauth-par-01) | [Draft 08 - May 2021](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-par-08) |pending | pending |


## Informative References

The following informative references were current at the time of review. For each reference, the version relied upon in the Consumer Data Standards as well as the most current version available of the reference are listed.

| **Reference**  | **Description** | **Baseline Version** | **05-2021 Version** |**Spec Diff** | **Spec Analysis** |
| --- | --- | --- | --- | --- | --- |
| <a id="BCP195"></a>**[BCP195]**   | Recommendations for Secure Use of Transport Layer Security (TLS) and Datagram Transport Layer Security (DTLS) | [May 2015](https://tools.ietf.org/html/bcp195) | [March 2021](https://tools.ietf.org/html/bcp195) | pending | pending |
| <a id="RFC7231"></a>**[RFC7231]**   | Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content | [June 2014](https://tools.ietf.org/html/rfc7231)  | [Updated by RFC2817](https://datatracker.ietf.org/doc/html/rfc2817)| pending | pending |
| <a id="CDR"></a>**[CDR]**      | Consumer Data Right: | [Link](https://www.accc.gov.au/focus-areas/consumer-data-right) | [**New Link**](https://www.cdr.gov.au) |N/A | N/A |
| <a id="FAPI"></a>**[FAPI]**      | Financial-Grade API - Home Page | [Link](https://openid.net/wg/fapi/) | No change |N/A | N/A |
| <a id="RFC4122"></a>**[RFC4122]**  | A Universally Unique Identifier (UUID) URN Namespace | [July 2005](https://tools.ietf.org/html/rfc4122) | No change |N/A | N/A |
| <a id="X.1254"></a>**[X.1254]**   | X.1254 - Entity authentication assurance framework | [Sept 2012](https://www.itu.int/rec/T-REC-X1254-201209-I/en] | [Sept 2020](https://www.itu.int/rec/T-REC-X.1254-202009-I/en)| N/A | N/A |
