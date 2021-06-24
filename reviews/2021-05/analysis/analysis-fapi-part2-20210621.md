# Overview

* **Specification:** Financial-grade API Security Profile 1.0 - Part 2: Advanced
* **Baseline Version:** [FAPI Draft06, Part 2](https://openid.net/specs/openid-financial-api-part-2-wd-06.html)
* **Target Version:** [FAPI 1.0 Final, Advanced](https://openid.net/specs/openid-financial-api-part-2-1_0.html)

This document summarises the changes between FAPI Draft 06 Part 2 and FAPI 1.0 Final Part 2 and the impacts to the Consumer Data Standards.

Impact analysis is compared against v1.10.0 of the Consumer Data Standards.

# Summary of key changes

## Clients (ADRs)

* TBA

## OpenID Provider / Authorisation Server (Data Holders)

* Shall support PKCE when supporting PAR
* Shall only support S256 as the code challenge method
* TBA

## Trust Authority (CDR Register)

* TBA

# Analysis

## 1. Scope

No differences.

## 2. Normative references

FAPI 1.0 Final Part 2 adds the following normative references, not present in Draft 06:
- RFC7591 - OAuth 2.0 Dynamic Client Registration Protocol
- RFC7592 - OAuth 2.0 Dynamic Client Registration Management Protocol
- BCP195 - Recommendations for Secure Use of Transport Layer Security (TLS) and Datagram Transport Layer Security (DTLS)
- PAR - OAuth 2.0 Pushed Authorization Requests
- JAR - OAuth 2.0 JWT Secured Authorization Request


## 3. Terms and definitions
FAPI 1.0 Final Part 2 adds the following:

- ISO29100 ("Information technology — Security techniques — Privacy framework")

## 4.  Symbols and Abbreviated terms

No material differences.

## 5.  Advanced security profile

No differences.

### 5.1.  Introduction

* Moves statements related to ID tokens as detached signatures to section 5.1.1
* Still requires s_hash
* Permits JARM, which is detailed in 5.1.2

#### 5.1.1.  ID Token as Detached Signature

No differences.

#### 5.1.2.  JWT Secured Authorization Response Mode for OAuth 2.0 (JARM)

* Moves majority of the statements from section 5.2.5 of Draft 06 into this section
* Defers some statements to the JARM spec - notably recommending the AuthZ Server should advertise supported response modes using the `response_modes_supported` metadata parameter

### 5.2.  Advanced security provisions

No differences.

#### 5.2.1.  Introduction

No material differences.

#### 5.2.2.  Authorization server

Changes from Draft 06:

- Part 1 section 5.2.2 clause 7 is not required to be supported (code challenge method for PKCE)

> shall require RFC7636 with S256 as the code challenge method;

- Clause 1: remains effectively the same
- Clause 2: Drops allowed support of "code id_token token" for response_type
- Clause 2: For JARM, response_mode is `jwt`
- Clause 3: moved to 5.2.2.1
- Clause 4: moved to 5.2.2.1
- Clause 5: Requires sender constrained tokens
**NOTE:** Currently required, no impact to CDS
- Clause 6: Requires MTLS for sender constrained tokens.
**NOTE:** No impact to CDS
- Clause 7: Withdraws requirement of LoA 3 for write access.
**NOTE:** This is good in that it helps the CDS align to the Future Directions report of principles based authentication and leveraging risk-based analysis of each action (be it read or write)
- Clause 8: moved to 5.2.2.1
- Clause 9: moved to 5.2.2.1
- Clause 10: Change to requirement implies the AuthZ Server shouldn't reject authorisation requests where parameters are presented outside the request object but SHALL NOT rely on or use any parameters presented outside the request object:

> shall only use the parameters included in the signed request object passed via the request or request_uri parameter;

- Clause 11: Allows AuthZ Servers to support PAR
**NOTE:** No direct impact to the CDS however FAPI 1.0 depends upon the newest reference to PAR (currently draft 08)

- Clause 12: Withdrawn, not relevant to CDS as this was related to public clients

- Clause 13:
> shall require the request object to contain an `exp` claim that has a **lifetime of no longer than 60 minutes after the `nbf` claim**

(emphasis added)

**Breaking Change** - most likely a config change to implementations but some IAM vendors don't currently cater for OOTB configuration of the `exp` validation lifetime.

- Clause 14: No change
- Clause 15: **NOTE:** new clause.

> shall require the `aud` claim in the request object to be, or to be an array containing, the OP's Issuer Identifier URL

**Breaking Change** - what was a SHOULD in [OIDC] is now a SHALL (must). The audience must be the OP's issuer identifier URL or an array that contains the OP's issuer identifier URL

[OIDC]:
> The `aud` value SHOULD be or include the OP's Issuer Identifier URL.

FAPI 1.0:
> shall require the `aud` claim in the request object to be, or to be an array containing, the OP's Issuer Identifier URL

- Clause 16: **NOTE:** new clause.

Precludes public clients. This does not impact the CDS which currently does not support public clients.

- Clause 17: **NOTE:** new clause.

  - Sets validation time requirement on the `nbf` claim:

  > shall require the request object to contain an `nbf` claim that is no longer than 60 minutes in the past

  - **Breaking Change**
    - ADRs must provide the `nbf` claim with a value no longer than 60 minutes prior to the authorisation request
    - DHs must validate that the `nbf` claim's value is no longer than 60 minutes from receipt of the authorisation request

- Clause 18: **NOTE:** new change.
  - Requires ADRs to use PKCE where PAR is used for authorisation requests
> shall require PAR requests, if supported, to use PKCE (RFC7636) with S256 as the code challenge method.

   - **Breaking Change** Currently PAR is used by the CDS without the need for PKCE. We either need to transition clients towards PKCE and, at least, as a minimum support a period of transition where the ID Token is used as a detached signature rather than PKCE as an alternative.
   - Moving towards PKCE support will be a key step in a transition towards FAPI 2.0 supportability
   - [PKCE](https://datatracker.ietf.org/doc/html/rfc7636) offers some significant advantages vs hybrid flow most notably simplifying the client implementation
   - It may also be worth considering what is required to support ADR software products that require interactivity without browsers or are otherwise using constrained input devices. See [RFC8628](https://datatracker.ietf.org/doc/html/rfc8628)
   - **NOTE:** there is a current issue with loss of authorisation code in exchange for tokens causing real production issues in the CDR. Could the introduction of PKCE and the code verifier method alleviate this? Because the `code_challenge_method` is bound to the authorisation code can the `authorization_code` be re-usable where it has not been successfully exchanged. Within a window - say 60 seconds or 5 minutes, the authorisation code can be replayed with the code verifier (as proof the client requesting tokens is the client that initiated the request) and the `authorization_code` is expired upon an `authorization_code` lifetime. If the `authorization_code` is replayed the AuthZ Server would issue the same tokens it did (or thought it had) issued the first time.

#### 5.2.2.1.  ID Token as detached signature

No material changes. No impacts to the CDS.

#### 5.2.2.2.  JARM

- Impacts to the CDS. Currently the CDS does not support JARM. This was originally a finding of the [Fortian review](https://github.com/ConsumerDataStandardsAustralia/standards-maintenance/issues/7) and has also been recommended by the OIDF.

- This change should be adopted. In doing so, there would be breaking change and a transition period required.

#### 5.2.3.  Confidential client

- Collapses some of the original (Draft 06) public client statements into the confidential client section.

- Clause 1: No change from 5.2.4.1
- Clause 2: No change
- Clause 3: Relased LoA requirement - effectively delegates to the standards specific to the jurisdiction. No changes to the CDS are required because LoAs for read access are already stated.
- Clause 4: moved to 5.2.3.1 - only applies when JARM is not used
- Clause 5: withdrawn - ADRs must still validate that the acr represents an LoA of sufficient strength as defined by the CDS for the action being taken
- Clause 6: withdrawn - Currently the CDS defines authentication methods within the bounds of the Data Holder - clients cannot send or negotiate authentication methods
- Clause 7: moved to 5.2.3.1 - only applies when JARM is not used
- Clause 8: No change
- Clause 9: **NEW** No change unless using PAR, in which case the additional claims outside the request object aren't required. For backwards compatibility, it doesn't hurt if the client continues to send these to the authorisation endpoint.
- Clause 10: **NEW** Constrains the `aud` claim value to only be the OP's issuer identifier URL. **NOTE:** this conflicts with 5.2.2 clause 13.  **Breaking Change**
- Clause 11: **NEW** Client requirement for lifetime of the `exp`. Note that to avoid rejection from the AuthZ Server, it is advisable that a value slightly shorter than 60 min is chosen otherwise there is a likelihood of AuthZ Server rejection at the boundary of the time limit. **Breaking Change**
- Clause 12: **NEW** Says this has moved to 5.2.3.1 but there wasn't a clause 12 in Draft 06
- Clause 13: **NEW** Says this has moved to 5.2.3.1 but there wasn't a clause 12 in Draft 06
- Clause 14: **NEW** Clients must send an nbf claim in the requst. **Breaking change**
- Clause 15: **NEW** Requires PKCE for PAR using code challenge method S256. **Breaking change** In PAR Draft 01 this was implied but not mandated.
- Clause 16: **NEW** Client must always send the client_id to the authorisation endpoint even when using PAR. This is not an impact to the CDS - it was already covered by the CDS.

#### 5.2.3.1.  ID Token as detached signature

No material changes.

#### 5.2.3.2.  JARM

When "`response_type` value `code` is used in conjunction with the `response_mode` value `jwt`" then the client must verify the authorisation response in accordance with the [JARM] spec.

**Breaking Change** Will impact ADR client implementations.

#### 5.2.4.  (withdrawn)

Covered by 5.2.3.1

#### 5.2.5.  (withdrawn)

Covered by 5.2.3.2

## 6.  Accessing protected resources (using tokens)

No changes.

### 6.1.  Introduction

No changes.

### 6.2.  Advanced access provisions

No changes.

### 6.2.1.  Protected resources provisions

No material changes.

### 6.2.2.  Client provisions

No material changes.

## 7.  (Withdrawn)

Request object endpoint is retired and replaced by PAR. No impact to the CDS.

## 8.  Security considerations

No changes.

### 8.1.  Introduction

No material changes.

### 8.2.  Uncertainty of resource server handling of access tokens

- Support for oAuth token binding [OAUTB] has been dropped. This doesn't affect the CDS.

### 8.3.  Attacks using weak binding of authorization server endpoints

No changes.

#### 8.3.1.  Introduction

No material changes.

#### 8.3.2.  Client credential and authorization code phishing at token endpoint

* Drops reference to [OAUTB], othewise no material changes

#### 8.3.3.  Identity provider (IdP) mix-up attack

- Provides reference to prior oAuth security analysis (2016)
- Adds reference to JARM, not just Hybrid Flow

#### 8.3.4.  (removed)

- The removal of this section does not give rise to impacts to the CDS.
#### 8.3.5.  Access token phishing

No material changes.

### 8.4.  Attacks that modify authorization requests and responses

No changes.

#### 8.4.1.  Introduction

No material changes.

#### 8.4.2.  Authorization request parameter injection attack

No material changes.

#### 8.4.3.  Authorization response parameter injection attack

- Adds JARM

### 8.5.  TLS considerations

- 8.5 (1): Where TLS 1.3+ is used, no cipher constraints are defined. For TLS 1.2 and below, the contrained list of ciphers remains. No impact to the CDS
- 8.5 (3): Requires a key length of **at least** 2048 when `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256` or `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384` are used. **Breaking Change** This may impact implementation choices currently chosen by Data Holders. Requires confirmation

### 8.6.  Algorithm considerations

No change.

#### 8.6.1.  Encryption algorithm considerations
**New section**

- 8.6.1 (1): Clients and Authorisation Servers are not allowed to use the `RSA1_5` algorithm

### 8.7.  Incomplete or incorrect implementations of the specifications

No material changes.

### 8.8.  Session Fixation
**New section**

No material impact. The CDR also adds additional controls by requiring only _accredited_ third parties to be allowed to initiate authorisation requests to DHs. [PAR] increases this security for Authorisation Servers because client authentication can be executed before receipt of the authorisation request.

### 8.9.  JWKS URIs
**New section**

- 8.9 (1) requires that jwks_uri endpoints shall be served over TLS. No impact to the CDS
- 8.9 (2) recommends that JOSE headers for x5u and jku should not be used. **Breaking Change** May impact some implementations - requires verification
- 8.9 (3) does not allow the same kid to be used by multiple keys within a JWKS

### 8.10.  Multiple clients sharing the same key
**New section**

- Clarifies the risks of using certificates issued at the organisation level to protect many clients (ADR software products). **Impact** It may be worth adding a statement in the CDR Register that ADR clients are recommended to have separate certificates for each software product. Consultation should be conducted as to whether this be made a MUST, not just a SHOULD


### 8.11.  Duplicate Key Identifiers
**New section**

- No impact to the CDR Register or CDS. A similar statement is currently used by the CDR Register. **Impact** The CDR Register standards would be best to remove their statements to similar effect and reference section 8.11 of FAPI Advanced.

## 9.  Privacy considerations

Informational, no material impact.

### 9.1.  Introduction

Informational, no material impact.
