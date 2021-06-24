# Overview

* **Specification:** Financial-grade API Security Profile 1.0 - Part 1: Baseline
* **Baseline Version:** [FAPI Draft06, Part 1](https://openid.net/specs/openid-financial-api-part-1-wd-06.html)
* **Target Version:** [FAPI 1.0 Final, Baseline](https://openid.net/specs/openid-financial-api-part-1-1_0.html)

This document summarises the changes between FAPI Draft 06 Part 1 and FAPI 1.0 Final Part 1 and the impacts to the Consumer Data Standards.

Impact analysis is compared against v1.10.0 of the Consumer Data Standards.

# Summary of key changes

## Clients (ADRs)

* **token response `scope` value** shall verify that the scope received in the token response is either an exact match, or contains a subset of the scope sent in the authorization request
* **Value of Content-Type header** shall send the Content-type HTTP header Content-Type: application/json if applicable;

## OpenID Provider / Authorisation Server (Data Holders)
* **TLS and DNSSEC considerations** Endpoints for the use by web browsers should use mechanisms to ensure that connections cannot be downgraded using TLS Stripping attacks
* **TLS and DNSSEC considerations** all endpoints should additionally use DNSSEC to protect against DNS spoofing attacks that can lead to the issuance of rogue domain-validated TLS certificates
* **Data Holder Registration** Organizations who need to support multiple "brands" with individual authorization endpoints from a single Authorization Server deployment shall use a separate `issuer` per brand.
* **Validaton of Content-Type header** shall send the Content-type HTTP header Content-Type: application/json if applicable;
* **`x-fapi-customer-ip-address`** shall not reject requests with a `x-fapi-customer-ip-address` header containing a valid IPv4 or IPv6 address.

## Trust Authority (CDR Register)
* **Data Holder Registration** Organizations who need to support multiple "brands" with individual authorization endpoints from a single Authorization Server deployment shall use a separate `issuer` per brand.

# Analysis

## 1. Scope

No differences.

## 2. Normative References

The 1.0 Final spec adds two additional normative references:
* OIDD - [OpenID Connect Discovery 1.0 incorporating errata set 1](http://openid.net/specs/openid-connect-discovery-1_0.html)
* RFC7231 - [Hypertext Transfer Protocol (HTTP/1.1): Semantics and Content](https://tools.ietf.org/html/rfc7231)

**NOTE:** Both are currently references in the CDS as normative and informative references respectively. These changes arising in v1.0 Final spec create no additional impact to the CDS.

## 3. Terms and definitions

No material differences.

## 4. Symbols and abbreviated terms

## 5.  Baseline security profile

### 5.1.  Introduction

No material differences. **NOTE:** Part 1 is now defined as baseline security profile rather than the read-only profile to better describe the core set of security provisions applicable to read and write InfoSec.

### 5.2. Baseline security provisions

No differences.

#### 5.2.1. Introduction

No material differences.

#### 5.2.2. Authorization server
* Delegates required levels of assurance to the downstream jurisdiction / standards implementation where previously this was LoA2 or above. **NOTE:** **This will not impact the CDS which requires LoA2 or above**
* Changes the use of the word 'consent' to be 'approval' noting consent has legal connotations. **NOTE:** **This will not impact the CDS**
* Previous authorisation codes MUST be rejected (previously this was a should). **NOTE:** **This will not impact the CDS, already required**
> shall reject an authorization code (Section 1.3.1 of <a href='https://tools.ietf.org/html/rfc6749'>RFC6749</a>) if it has been previously used

* Qualifies the requirements to follow [OIDD] for discovery metadata. **NOTE:** **This will not impact the CDS, already required**
* Recommends the use of refresh tokens rather than the practice of long-lived access tokens for public and private clients. **NOTE:** **This will have not impact the CDS, already required**

#### 5.2.2.1.  Returning authenticated user's identifier
Was "5.2.2.1. Returning authenticated user's identifier Authorization server" in Draft 06

No material differences.

#### 5.2.2.2.  Client requesting openid scope
**New section**

> If the client requests the openid scope, the authorization server
> - shall require the nonce parameter defined in Section 3.1.2.1 of OIDC in the authentication request.

**NOTE:** This was previously covered in Draft 06 Part 1 in section "5.2.3 Public Client" which section "5.2.4 Confidential Client" inherits.

#### 5.2.2.3.  Clients not requesting openid scope
**New section**

No applicable.

#### 5.2.3.  Public client
**Note:** This section is required as the baseline for confidential clients.

- Requirement #5 has been withdrawn:

> shall adhere to the best practice stated by [BCP212];

- Adds two new requirements:

> 10. shall verify that the scope received in the token response is either an exact match, or contains a subset of the scope sent in the authorization request; and
> 11. shall only use Authorization Server metadata obtained from the metadata document published by the Authorization Server at its well known endpoint as defined in OIDD or RFC8414.

- Requires token response scope list to be validated as a subset or equal to the scopes requested in the authorisation request

#### 5.2.4.  Confidential client

No differences.

## 6.  Accessing Protected Resources
No differences.

### 6.1.  Introduction
No differences.

### 6.2.  Baseline access provisions
No differences.

#### 6.2.1.  Protected resources provisions
Content-type header requirement has changed from `Content-Type: application/json; charset=UTF-8` to `Content-Type: application/json`.

The CDS requires conformance to [RFC7231] which means the content type's media type must be `application/json` but `Content-Type` may include wildcard, and charsets. The interpretation is clear in the CDS.

**CDS requirement**

Adds additional requirement:

> shall not reject requests with a x-fapi-customer-ip-address header containing a valid IPv4 or IPv6 address.

* This means that DHs cannot reject requests based on the contents of the x-fapi-customer-ip-address is a valid IPv4 or IPv6. That said, this may be a value inspected by the DHs WAF and rejections made based on its contents or the IP address of the requesting client for one or more security reasons.

#### 6.2.2.  Client provisions

No material differences.


## 7.  Security considerations

No differences.

### 7.1.  TLS and DNSSEC considerations

* FAPI 1.0 Final includes statements regarding prevention of TLS stripping attacks.

> Endpoints for the use by web browsers should use mechanisms to ensure that connections cannot be downgraded using TLS Stripping attacks. A preloaded HTTP Strict Transport Security policy (see PRELOAD and RFC6797) can be used for this purpose. Some top-level domains, like .bank and .insurance, have set such a policy and therefore protect all second-level domains below them.
>
> For a comprehensive protection against network attackers, all endpoints should additionally use DNSSEC to protect against DNS spoofing attacks that can lead to the issuance of rogue domain-validated TLS certificates.
>
> NOTE: Even if an endpoint uses only organization validated (OV) or extended validation (EV) TLS certificates, rogue domain-validated certificates can be used to impersonate the endpoints and conduct man-in-the-middle attacks. CAA records RFC8659 can help to mitigate this risk.

* Expected that the CDS will defer to FAPI 1.0 specs in this regard. DH feedback is warranted to understand any impacts DHs foresee to existing implementations.

### 7.2.  Message source authentication failure

No material differences.

### 7.3.  Message integrity protection failure

No material differences.

### 7.4.  Message containment failure

No differences.

#### 7.4.1.  Authorization request and response

No differences.

#### 7.4.2.  Token request and response

No differences.

#### 7.4.3.  Resource request and response

No material differences.

### 7.5.  Native Apps

No material differences.

### 7.6.  Incomplete or incorrect implementations of the specifications

No material differences.

### 7.7.  Discovery & Multiple Brands
**New section**

* Multiple brands as separate tenants under one Authorization Server must use separate issuers. This may be done at the domain or path level.

## 8.  Privacy considerations

Recommendations for privacy impact assessments have been removed.

### 8.1.  Introduction

Provides useful statements regarding privacy, security and consent. No material differences related to the standards.

# Impact analysis to the Consumer Data Standards

* TBA

# Recommendations

* TBA
