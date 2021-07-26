# Overview

* **Specification:** Pushed Authorization Requests (PAR)
* **Baseline Version:** [Pushed Authorization Requests Draft 01](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-par-01)
* **Target Version:** [Pushed Authorization Requests Draft 08](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-par-08)

This document summarises the changes between Pushed Authorization Requests Draft 01 and Pushed Authorization Requests Draft 08 and the impacts to the Consumer Data Standards.

Impact analysis is compared against v1.11.0 of the Consumer Data Standards.

# Summary of key changes

At the time of review, the PAR specification is currently draft version 08. Because the PAR specification remains in draft, there are likely future draft revisions that will be published. One consideration the DSB is seeking feedback from the community is whether the data standards should continue to specify an explicit draft version (08) or refer to PAR as being what ever the latest revision is. This would have the advantage that whatever the most recent version is, implementations can rely upon it and potentially use the version provided out of the box by their IAM vendor provided that vendor remains up to date with the most recent version. The downside is that the implementation of new draft versions would be immediate and there would be no future dated obligation if a newer draft version were to create breaking changes.

## Requires community feedback

- Should the CDS explicitly define the `request_uri` must only be used once and cannot be replayed?
- Should the CDS explicitly define the upper lifetime of the `request_uri`?
- Determine the appropriate HTTP status code and oAuth error code to respond with when the `request_uri` is re-used. Currently presumed this is a 400 (Bad Request) and oAuth error `invalid_request` or `access_denied`.
- Implications with recently introduced CDR Register standards for `sector_identifier_uri` is unknown
- Introduces the new OIDD metadata parameter `require_pushed_authorization_requests`. Recommended that DHs can choose whether to require this and if so ADRs MUST only use PAR if true. This would give DHs more discretion over security and simplification of implementation. The CDS doesn't need to override or preclude this. If there is a desire to simplify the CDS, it may be preferred to limit the CDS to always require PAR for better interoperability in a many-to-many ecosystem and reduce interoperability choices.

## Clients (ADRs)

- &sect; 4.: Clients MUST only use a "request_uri" value once
- &sect; 2.: If the Authorisation Server includes `require_pushed_authorization_requests` = `true` in their OIDD, then the client MUST use PAR and MUST NOT pass the request object to the authorisation endpoint

## OpenID Provider / Authorisation Server (Data Holders)

- &sect; 4.: Data Holders should respond with an error if the `request_uri` is re-used
- &sect; 5.: If the Authorisation Server MAY include `require_pushed_authorization_requests` in their OIDD (defaults to `false` otherwise)
- &sect; 5.: If the Authorisation Server sets `require_pushed_authorization_requests` = `true`, they MUST reject request objects passed to the authorisation endpoint
- &sect; 3.: Drops the requirement of "401 Unauthorized" where the client signature, "client_id" or "iss" fail validation
- &sect; 3.: DH may respond with invalid_client or invalid_authorization error response as per [RFC6749 section 5.2](https://datatracker.ietf.org/doc/html/rfc6749#section-5.2).

## Conformance Testing

- &sect; 2.: It may be worthwhile for completeness for the CTS to assert correct values in the "token_endpoint_auth_method" and "token_endpoint_auth_method_supported" values
- &sect; 3.: DH may respond with `invalid_client` or `invalid_authorization` error response as per [RFC6749 section 5.2](https://datatracker.ietf.org/doc/html/rfc6749#section-5.2).
* **NOTE:** This may result in some implementation and CTS changes or at the very least regression testing by DHs and ADRs

# Analysis

## 1.  Introduction

* The Draft 08 Introduction provides a more detailed explanation behind the intent and use of PAR but has no material differences that change implementation.

### 1.1.  Introductory Example

* No material differences

### 1.2.  Conventions and Terminology

* No material differences

## 2.  Pushed Authorization Request Endpoint

* &sect; 2. &‌para; 1: requires HTTPS
* &sect; 2. &‌para; 2: Authorisation servers SHOULD include their PAR endpoint using the OIDD "pushed_authorization_request_endpoint" parameter. **NOTE:** CDS requires that this MUST be published in the OIDD. This removes ambiguity in the CDR's many-to-many environment

* &sect; 2. &‌para; 4: Introduces client authentication can be negotiated via "token_endpoint_auth_methods_supported" (currently supported in the CDS) or "token_endpoint_auth_method" (this also existed in PAR Draft 01)

**NOTE:** For completeness, it may be beneficial for the CTS to provide test cases that verify correct implementation for client and server of the "token_endpoint_auth_methods_supported" and "token_endpoint_auth_method" parameters including where both are provided.

CDS uses "private_key_jwt". Currently the behaviour is ambiguous if the AS provides different values in these two metadata parameters and one is not "private_key_jwt".

### 2.1.  Request

* No material differences

### 2.2.  Successful Response

* &sect; 2.2.: Drops mention that the `request_uri` is intended for one time use. There is currently no clause in FAPI 1.0 that restricts this meaning that Authorisation Servers MAY implement the `request_uri` in such a way that it can be re-used within its lifetime or be recycled. This is still cryptographically bound to the oAuth client however there may be issues where it could be replayed within a short period of time.  

**Old clause**

> Since the request URI can be replayed, its lifetime SHOULD be short
> and preferably limited to one-time use.

* **NOTE:** Should the CDS prevent this, or is this replay not seen as an issue - it may be useful where the client attempts to go through the authorisation flow but encounters a technical issue and can replay the `request_uri` without re-staging it though this seems to be of little benefit. It is pre-authentication so again, there is limited opportunity for malicious use/replay attack. The only question is whether a simplified authentication flow may be impacted if the consumer is not required to authenticate.
* Further to this, there is a change in &sect; 4. &para; 3 which details these requirements.

* The PAR spec also doesn't suggest the appropriate error for the AS to respond with when replay is not supported or denied. Presumably this is a 400 (Bad Request) per &sect; 2.3.

### 2.3.  Error Response

* No material differences

### 2.4.  Management of Client Redirect URIs

**New section**

* Allows for the provision of per-request `redirect_uris` that have not been previously registered with the Authorisation Server.

> The authorization server MAY allow such
> clients to specify "redirect_uri" values that were not previously
> registered with the authorization server.

* This is not currently permitted in the CDR which requires valid `redirect_uris` to be registered.
* It is worth reviewing this in light of other provisions such as `sector_identifier_uri` and may also have implications for PPID generation if this allowance is adopted or considered in a future iteration of the standards.
* This may provide (with consideration) a way to deal with ADR SaaS / Outsourced Service Provider arrangements where the client is managed by a trusted third-part of the ADR

## 3.  The "request" Request Parameter

* The following sections have been removed:
  * 3.1.  Error responses for Request Object
    * 3.1.1.  Authentication Required

* Drops the requirement of "401 Unauthorized" where the client signature, "client_id" or "iss" fail validation
* This means that [RFC6749 section 5.2](https://datatracker.ietf.org/doc/html/rfc6749#section-5.2) takes effect.
* **NOTE:** This may result in some implementation and CTS changes or at the very least regression testing by DHs and ADRs

## 4.  Authorization Request

* No material differences

* &sect; 4. &para; 3: Implies that `request_uri` should only be treated as a one-time code. However it does allow for it to be re-used. The spec makes in mandatory that clients only use the `request_uri` once.

> Since parts of the authorization request content, e.g. the
> "code_challenge" parameter value, are unique to a particular
> authorization request, the client MUST only use a "request_uri" value
> once.  Authorization servers SHOULD treat "request_uri" values as
> one-time use but MAY allow for duplicate requests due to a user
> reloading/refreshing their user-agent.

## 5.  Authorization Server Metadata

* Introduces a new "require_pushed_authorization_requests" parameter. For the CDS this would default to "false" because it is not mandatory. Presently, the request object can be passed by value for authorisation requests unless it is an amending consent flow where the cdr_arrangement_id is passed.

## 6.  Client Metadata

**New section**

* Introduces a new "require_pushed_authorization_requests" parameter.

## 7.  Security Considerations

* No differences

### 7.1.  Request URI Guessing

* No material differences

### 7.2.  Open Redirection

* No material differences

### 7.3.  Request Object Replay

* No material differences

### 7.4.  Client Policy Change

* No material differences

### 7.5.  Request URI Swapping

**New section**

* Clients SHOULD use PKCE
* Additionally request_uri is cryptographically bound to the client and the client is authenticated at the PAR endpoint before the AS returns the request_uri so this attack should be obviated

> Clients SHOULD make use of PKCE
> [RFC7636], a unique "state" parameter [RFC6749], or the OIDC "nonce"
> parameter [OIDC] in the pushed request object to prevent this attack.

## 8.  Privacy Considerations
* No material differences

## 9.  Acknowledgements

* Not applicable

## 10. IANA Considerations
### 10.1.  OAuth Authorization Server Metadata

* Adds the following registered property" `require_pushed_authorization_requests` - this will need to be considered if the CDS mandated authorisation requests always use PAR or DHs can choose not to support request objects sent by value delivered to the authorisation endpoint. There may be advantaged for DHs to secure & simplify their implementations to only support PAR and perform request object validation at the PAR endpoint after client authentication as opposed to receiving the request object at the authorisation endpoint.

**Feedback welcome from DHs and ADRs**

### 10.2.  OAuth Dynamic Client Registration Metadata

* Refer to &sect; 10.1.

### 10.3.  OAuth URI Registration

* Refer to &sect; 10.1.

## 11. Normative References

* Depends on the most recent draft of JWT Secured Authorization Requests (JAR): draft-ietf-oauth-jwsreq-34, 8 April 2021.
* Previously Draft 20, 21 October 2019
* Drops explicit reference to OIDC and RFC6749 and moves them to the informative references

## 12. Informative References

* No material differences
