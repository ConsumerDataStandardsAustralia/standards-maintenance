# Overview

* **Specification:** Financial-grade API Security Profile 1.0 - Part 1: Baseline
* **Baseline Version:** [FAPI Draft06, Part 1](https://openid.net/specs/openid-financial-api-part-1-wd-06.html)
* **Target Version:** [FAPI 1.0 Final, Baseline](https://openid.net/specs/openid-financial-api-part-1-1_0.html)

This document summarises the changes between FAPI Draft 06 Part 1 and FAPI 1.0 Final Part 1 and the impacts to the Consumer Data Standards.

Impact analysis is compared against v1.10.0 of the Consumer Data Standards.

# Summary of key changes

## Clients (ADRs)

* &sect; 5.2.3 (10) **token response `scope` value** shall verify that the scope received in the token response is either an exact match, or contains a subset of the scope sent in the authorization request
* &sect; 6.2.1 (9): **Value of Content-Type header** shall send the Content-type HTTP header Content-Type: application/json if applicable;

## OpenID Provider / Authorisation Server (Data Holders)

The following changes were identified as impacts to Data Holders. The course of action is to be discussed with the community prior to recommendation.

* &sect; 5.2.2. (15): The DH only returns scopes where the list granted is different to the list the client requested
* &sect; 6.2.1. (9): **Validation of Content-Type header** shall send the Content-type HTTP header `Content-Type: application/json` if applicable;
* 6.2.1. (13): **`x-fapi-customer-ip-address`** shall not reject requests with a `x-fapi-customer-ip-address` header containing a valid IPv4 or IPv6 address.
* &sect; 7.1.: **TLS and DNSSEC considerations** Endpoints for the use by web browsers should use mechanisms to ensure that connections cannot be downgraded using TLS Stripping attacks
* &sect; 7.1.: **TLS and DNSSEC considerations** all endpoints should additionally use DNSSEC to protect against DNS spoofing attacks that can lead to the issuance of rogue domain-validated TLS certificates
* &sect; 7.7.: **Data Holder Registration** Organizations who need to support multiple "brands" with individual authorization endpoints from a single Authorization Server deployment shall use a separate `issuer` per brand.

### &sect; 5.2.2 (15) Scope response
There are benefits to *always* returning the list of scopes in the token response because the CDR deals with the phasing of obligations yet uses the Information Security profile to convey some of this context to ADRs. Until such time that RAR is supported, it may be preferable to retain the requirement that scopes are *always* returned to ADR clients. This will benefit ADRs in that there is no breaking change for this.

Community consultation on the CDS requiring scopes to be returned (always) to improve reliability for clients.

### Content-Type header
Determination is either to retain more flexible support as a constraint to FAPI 1 or a breaking change to clients to send Content-Type without charset.

## Trust Authority (CDR Register)
* &sect; 7.7.: **Data Holder Registration** Organizations who need to support multiple "brands" with individual authorization endpoints from a single Authorization Server deployment shall use a separate `issuer` per brand.

**Note:** Although this does not require the AS to return scopes when receiving a call via the backchannel from an integrity protected request, per section 5.1 of RFC6749 (https://datatracker.ietf.org/doc/html/rfc6749#section-5.1) the scope value is only “OPTIONAL, if identical to the scope requested by the client; otherwise, REQUIRED.“ suggesting that at the very least the AS return the granted scopes where they differ to what was requested. In this situation, clients also need to test the absence of the scopes in the response inferring _all_ scopes requested were granted. There is clearly an onus on the DH to only grant the client the scopes that it supports and not additional scopes it doesn't support. With the phasing of obligations in the ecosystem creates something of an undefined behaviour.

For this reason, it is probably worth the CDS explicitly requiring the scopes be returned because this removes ambiguity for clients without having to have conditional logic. The DH can be directed to only respond with scopes that it currently supports per phasing obligations.

**Note 2:** This is no longer an issue when RAR (Rich Authorisation Requests) and Grant Management are supported per FAPI 2.0 because the client has a reliable way to get _all_ of the details of the authorisation from the AS including scopes and other rich authorisation details

**Note 3:** The DSB has an open DP on participant capability discovery that is intended to better support clients discovering obligations and capabilities of the DHs (e.g. phasing obligations). This is not OIDD metadata but instead intends to describe specific capabilities and functionalities of the DH. By introducing this, clients would have a mechanism to ascertain when a DH supports future obligations and can update authorisation requests when new scopes become available (contingent on the consumer's consent).

## Conformance Testing

- &sect; 5.2.2 (15)
- &sect; 6.2.1. (9)
- &sect; 6.2.1. (13)
- &sect; 5.2.3. (10)
- &sect; 7.7.

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

No material differences.
**NOTE:** Part 1 is now defined as baseline security profile rather than the read-only profile to better describe the core set of security provisions applicable to read and write InfoSec.

### 5.2. Baseline security provisions

No differences.

#### 5.2.1. Introduction

No material differences.

#### 5.2.2. Authorization server
* &sect; 5.2.2. (11): Delegates required levels of assurance to the downstream jurisdiction / standards implementation where previously this was LoA2 or above. **NOTE:** **This will not impact the CDS which requires LoA2 or above**
* &sect; 5.2.2. (12): Changes the use of the word 'consent' to be 'approval' noting consent has legal connotations. **NOTE:** **This will not impact the CDS**
* &sect; 5.2.2. (13): Previous authorisation codes MUST be rejected (previously this was a should). **NOTE:** **This will not impact the CDS as it is already required**
> shall reject an authorization code (Section 1.3.1 of <a href='https://tools.ietf.org/html/rfc6749'>RFC6749</a>) if it has been previously used

* &sect; 5.2.2. (22): Qualifies the requirements to follow [OIDD] for discovery metadata. **NOTE:** **This will not impact the CDS, already required**
* Recommends the use of refresh tokens rather than the practice of long-lived access tokens for public and private clients. **NOTE:** **This will have not impact the CDS, already required**

* &sect; 5.2.2 (15): changes the requirement to such that scopes must be returned with the access token if "the request was passed in the front channel and was not integrity protected". This will likely have breaking impacts to ADR clients that rely on the scopes being present when the access token is requested via the back channel. It will mean that clients need to obtain the authorised list of scopes by calling the token endpoint or token introspection endpoint. The point of "integrity protected" also warrants discussion. There are significant benefits in the AS returning the authorised list of scopes to the client to ascertain the final consumer's directives for consent. Where a DH does not support a scope, the list will be a subset of what the client originally requested.

> shall return the list of granted scopes with the issued access token if the request was passed in the front channel and was not integrity protected;

**Note:** Although this does not require the AS to return scopes when receiving a call via the backchannel from an integrity protected request, per section 5.1 of RFC6749 (https://datatracker.ietf.org/doc/html/rfc6749#section-5.1) the scope value is only “OPTIONAL, if identical to the scope requested by the client; otherwise, REQUIRED.“ suggesting that at the very least the AS return the granted scopes where they differ to what was requested. In this situation, clients also need to test the absence of the scopes in the response inferring _all_ scopes requested were granted. There is clearly an onus on the DH to only grant the client the scopes that it supports and not additional scopes it doesn't support. With the phasing of obligations in the ecosystem creates something of an undefined behaviour.

For this reason, it is probably worth the CDS explicitly requiring the scopes be returned because this removes ambiguity for clients without having to have conditional logic. The DH can be directed to only respond with scopes that it currently supports per phasing obligations.

**Note 2:** This is no longer an issue when RAR (Rich Authorisation Requests) and Grant Management are supported per FAPI 2.0 because the client has a reliable way to get _all_ of the details of the authorisation from the AS including scopes and other rich authorisation details

**Note 3:** The DSB has an open DP on participant capability discovery that is intended to better support clients discovering obligations and capabilities of the DHs (e.g. phasing obligations). This is not OIDD metadata but instead intends to describe specific capabilities and functionalities of the DH. By introducing this, clients would have a mechanism to ascertain when a DH supports future obligations and can update authorisation requests when new scopes become available (contingent on the consumer's consent).

#### 5.2.2.1.  Returning authenticated user's identifier
Was "5.2.2.1. Returning authenticated user's identifier Authorization server" in Draft 06

No material differences.

#### 5.2.2.2.  Client requesting openid scope
(*New section*)

> If the client requests the openid scope, the authorization server
> - shall require the nonce parameter defined in Section 3.1.2.1 of OIDC in the authentication request.

**NOTE:** This was previously covered in Draft 06 Part 1 in section "5.2.3 Public Client" which section "5.2.4 Confidential Client" inherits.

#### 5.2.2.3.  Clients not requesting openid scope
(*New section*)

No applicable.

#### 5.2.3.  Public client
**Note:** This section is required as the baseline for confidential clients.

- &sect; 5.2.3. (5): Requirement #5 has been withdrawn:

> shall adhere to the best practice stated by [BCP212];

- Adds two new requirements:

> 10. shall verify that the scope received in the token response is either an exact match, or contains a subset of the scope sent in the authorization request; and
> 11. shall only use Authorization Server metadata obtained from the metadata document published by the Authorization Server at its well known endpoint as defined in OIDD or RFC8414.

- &sect; 5.2.3 (10): Requires token response scope list to be validated as a subset or equal to the scopes requested in the authorisation request

**Note:** Although this now requires clients to explicitly validate the scopes returned 5.2.2 (15) essentially overrides this from the perspective that the CDR uses token responses in the back channel that integrity protected. In this situation, ADR clients would not receive a list of scopes and this leaves consent in a somewhat ambigous state. There is also clearly an onus on the DH to only grant the client the scopes that it supports and not additional scopes it doesn't support. With the phasing of obligations in the ecosystem creates something of an undefined behaviour.

For this reason, it is probably worth the CDS explicitly requiring the scopes be returned because this removes ambiguity for clients without having to have conditional logic. The DH can be directed to only respond with scopes that it currently supports per phasing obligations.

**Note 2:** This is no longer an issue when RAR (Rich Authorisation Requests) and Grant Management are supported per FAPI 2.0 because the client has a reliable way to get _all_ of the details of the authorisation from the AS including scopes and other rich authorisation details

**Note 3:** The DSB has an open DP on participant capability discovery that is intended to better support clients discovering obligations and capabilities of the DHs (e.g. phasing obligations). This is not OIDD metadata but instead intends to describe specific capabilities and functionalities of the DH. By introducing this, clients would have a mechanism to ascertain when a DH supports future obligations and can update authorisation requests when new scopes become available (contingent on the consumer's consent).

#### 5.2.4.  Confidential client

No differences.

## 6.  Accessing Protected Resources
No differences.

### 6.1.  Introduction
No differences.

### 6.2.  Baseline access provisions
No differences.

#### 6.2.1.  Protected resources provisions
- &sect; 6.2.1. (9): Content-type header requirement has changed from `Content-Type: application/json; charset=UTF-8` to `Content-Type: application/json`.

The CDS requires conformance to [RFC7231] which means the content type's media type must be `application/json` but `Content-Type` may include wildcard, and charsets. The interpretation is currently clear in the CDS that a DH can't reject a request where the `Content-Type` value is well-formed according to [RFC7231].

**CDS requirement** which requires consultation.

- &sect; 6.2.1. (13) Adds additional requirement:  

> shall not reject requests with a `x-fapi-customer-ip-address` header containing a valid IPv4 or IPv6 address.

This means that DHs cannot reject requests based on the contents of the `x-fapi-customer-ip-address` is a valid IPv4 or IPv6. That said, this may be a value inspected by the DHs WAF and rejections made based on its contents or the IP address of the requesting client for one or more security reasons.

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
(*New section*)

* Multiple brands as separate tenants under one Authorization Server must use separate issuers. This may be done at the domain or path level.

## 8.  Privacy considerations

Recommendations for privacy impact assessments have been removed.

### 8.1.  Introduction

Provides useful statements regarding privacy, security and consent. No material differences related to the standards.
