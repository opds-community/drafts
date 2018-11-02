[![OPDS Logo](https://opds.io/img/logo.png)](https://opds.io)

<style>
.rfc {
    color: #d55;
    font-variant: small-caps;
    font-style: normal;
}
</style>

# Authentication for OPDS 1.0

**This version:**

* [https://drafts.opds.io/authentication-for-opds-1.0](https://drafts.opds.io/authentication-for-opds-1.0)

**Editors:**

* Hadrien Gardeur ([Feedbooks](http://www.feedbooks.com))

**Participate:**

* [GitHub opds-community/drafts](https://github.com/opds-community/drafts)
* [File an issue](https://github.com/opds-community/drafts/issues)

## Status of this Document

This document is a draft of the 1.0 version of the Authentication for OPDS specification.

## Table of Contents

- [1. Overview](#1-overview)
  * [1.1. Purpose and Scope](#11-purpose-and-scope)
  * [1.2. Terminology](#12-terminology)
  * [1.3. Notational Conventions](#13-notational-conventions)
- [2. Authentication Document](#2-authentication-document)
  * [2.1. Introduction](#21-introduction)
  * [2.2. Content Conformance](#22-content-conformance)
  * [2.3. Syntax](#23-syntax)
    + [2.3.1. Core Properties](#231-core-properties)
    + [2.3.2. Links](#232-links)
    + [2.3.3. Example](#233-example)
  * [2.4. Authentication Provider](#24-authentication-provider)
  * [2.5. Client](#25-client)
- [3. Authentication Flows](#3-authentication-flows)
  * [3.1. Introduction](#31-introduction)
  * [3.2. Use of TLS](#32-use-of-tls)
  * [3.3. Authentication Object](#33-authentication-object)
    + [3.3.1. Labels](#331-labels)
    + [3.3.2 Links](#332-links)
  * [3.4 Basic Authentication](#34-basic-authentication)
  * [3.5 OAuth 2.0](#35-oauth-20)
    + [3.5.1. Introduction](#351-introduction)
    + [3.5.2. A Shared Client Identifier](#352-a-shared-client-identifier)
    + [3.5.3. Bearer Token](#353-bearer-token)
    + [3.5.4. Security Considerations](#354-security-considerations)
    + [3.5.5. Implicit Grant](#355-implicit-grant)
    + [3.5.6. Resource Owner Password Credentials Grant](#356-resource-owner-password-credentials-grant)
    + [3.5.7. OAuth Response Document](#357-oauth-response-document)
  * [3.6. Extensions](#36-extensions)
- [4. References](#4-references)
  * [4.1. Normative References](#41-normative-references)

## 1. Overview

### 1.1. Purpose and Scope

*This section is informative.*

This specification, Authentication for OPDS Catalogs (1.0), defines a standard way for OPDS Catalog providers and clients to:

  * provide relevant information for clients to display an authentication page
  * expose how a client can authenticate using various Authentication Flows

The primary objective is to allow access to specific feeds (such as bookshelf and subscriptions) along with support for interactions that require authentication (specific acquisition links such as buy, borrow and subscribe).

### 1.2. Terminology

This specification uses the following OPDS terms:

<dl>
  <dt>OPDS Catalog</dt>
  <dd>All of the Atom Feeds (Acquisition and Navigation) and Entries (Partial and Complete) following this specification published together to describe a consolidated group of available Publications.</dd>

  <dt>Acquisition Link</dt>
  <dd>An atom:link element with a relation that begins with "http://opds-spec.org/acquisition" and refers to the Resource which holds the content of the described Publication or the Resource through which it may be acquired for any OPDS Catalog Entry. See the Sections Acquisition Relations and Acquiring Publications. They are serialized as OPDS Catalog Feed Documents.</dd>
</dl>

In addition, it defines the following terms:

<dl>
  <dt>Authentication Document</dt>
  <dd>A discovery document that indicates which Authentication Flows are supported, how to interact with them, and what should be displayed to the user.</dd>

  <dt>Authentication Flow</dt>
  <dd>A series of steps necessary to authenticate the user.</dd>

  <dt>Authentication Page</dt>
  <dd>A page that contains various information provided by the Authentication Document along with input fields based on the Authentication Flow.</dd>

  <dt>Authentication Provider</dt>
  <dd>A server that supports one or more Authentication Flow to authenticate a client.</dd>
</dl>

### 1.3. Notational Conventions

The keywords <em class="rfc">must</em>, <em class="rfc">must not</em>, <em class="rfc">required</em>, <em class="rfc">shall</em>, <em class="rfc">shall not</em>, <em class="rfc">should</em>, <em class="rfc">should not</em>, <em class="rfc">recommended</em>, <em class="rfc">may</em>, and <em class="rfc">optional</em> in this document are to be interpreted as described in [[RFC2119]](https://www.ietf.org/rfc/rfc2119).

All sections of this specification are normative except where identified by the informative status label "This section is informative". The application of informative status to sections and appendices applies to all child content and subsections they may contain.

All examples in this specification are informative.

## 2. Authentication Document

### 2.1. Introduction

*This section is informative.*

The Authentication Document is essentially a service discovery document.

Part of its content is meant to identify how a client will authenticate a user with a Catalog provider:

* a list of supported Authentication Flows
* locations where specific interactions can be made
* a unique identifier for the Catalog

The rest of its content is meant to be displayed to the user:

* title of the service
* a logo
* a text prompt
* alternate labels for fields that will be displayed
* a registration link
* resources where the user can get support

### 2.2. Content Conformance

An Authentication Document <em class="rfc">must</em> meet all of the following criteria:

* It <em class="rfc">must</em> meet the conformance constraints for JSON documents as defined in [[JSON](http://www.ietf.org/rfc/rfc4627)].
* It <em class="rfc">must</em> parse as a single JSON object.
* It <em class="rfc">must</em> be encoded using UTF-8.

Access to an Authentication Document <em class="rfc">must not</em> require any form of authentication.

The MIME media type for an Authentication Document is `application/vnd.opds.authentication.v1.0+json`, and HTTP servers <em class="rfc">must</em> set the `Content-Type` header appropriately.

### 2.3. Syntax

#### 2.3.1. Core Properties

The Authentication Document must contain the following name/value pairs:

|Name|Value|Format/Data Type|
|----|-----|----------------|
|`authentication`|A list of supported Authentication Flows as defined in section [3. Authentication Flows](#3-authentication-flows).|Authentication Object|
|`title`|Title of the Catalog being accessed.|String|
|`id`|Unique identifier for the Catalog provider and canonical location for the Authentication Document.|URL|

In addition, the Authentication Document <em class="rfc">may</em> contain the following name/value pair:

|Name|Value|Format/Data Type|
|----|-----|----------------|
|`description`|A description of the service being displayed to the user.|String|

#### 2.3.2. Links

An Authentication Document <em class="rfc">may</em> also contain a `links` object.

This is used to associate the Authentication Document with resources that are not locally available.  

The `links` object contains one or more link with the following key/values:

|Name|Value|Format/Data Type|Required?|
|----|-----|----------------|---------|
|`href`|Link location|URI or URI Template|Yes|
|`rel`|Relation between the resource and the Authentication Document|String or URI for extensions|Yes|
|`title`|Title of the link|String|No|
|`type`|Expected MIME media type value for the external resources|MIME media type|No, but highly recommended|
|`templated`|Indicates that the href is a URI Template|Boolean|No, default value is "false"|
|`height`|Indicates the height of the linked resource in pixels|Integer|No|
|`width`|Indicates the width of the linked resource in pixels|Integer|No|

This specification introduces the following link relations:

|Relation|Semantics|Required?|
|--------|---------|---------|
|`logo`| Logo associated to the Catalog provider.| No|
|`register`| Location where a user can register.| No|
|`help`| Support resources for the user (either a website, an email or a telephone number).| No|

Link relations <em class="rfc">may</em> also be extended for vendor-specific applications. Such links <em class="rfc">must</em> use a URL instead of a string to identify their link relations.


#### 2.3.3. Example

*This section is informative.*

In the following example, the Authentication Document indicates that the Catalog can support two Authentication Flows: Basic Auth and OAuth 2.0 through the implicit grant.

```json
{
  "id": "http://example.com/auth.json",
  "title": "Public Library",
  "description": "Enter a valid library card number and PIN code to authenticate on our service.",
  "links": [
    {"rel": "logo", "href": "http://example.com/logo.jpg", "type": "image/jpeg", "width": 90, "height": 90},
    {"rel": "help", "href": "mailto:support@example.org"},
    {"rel": "help", "href": "tel:1800836482"},
    {"rel": "help", "href": "http://example.com/support", "type": "text/html"},
    {"rel": "register", "href": "http://example.com/registration", "type": "text/html"}
  ],
  "authentication": [
    {
      "type": "http://opds-spec.org/auth/basic",
      "labels": {
        "login": "Library card",
        "password": "PIN"
      }
    },
    {
      "type": "http://opds-spec.org/auth/oauth/implicit",
      "links": [
        {"rel": "authenticate", "href": "http://example.com/oauth", "type": "text/html"},
        {"rel": "refresh", "href": "http://example.com/oauth/refresh", "type": "application/json"},
      ]
    }
  ]
}
```

### 2.4. Authentication Provider

In order to require the client to authenticate, an Authentication Provider <em class="rfc">must</em> send a `401 Unauthorized` HTTP status code. In this context, the Authentication Provider <em class="rfc">must</em> also serve the Authentication Document as the content of the response along with the proper MIME media type (`application/vnd.opds.authentication.v1.0+json`) in its "Content-Type" header.

The Authentication Provider <em class="rfc">may</em> also use the "Link" header from [[Web-Linking](https://tools.ietf.org/html/rfc5988)] to indicate the location of the Authentication Document. The link-value <em class="rfc">must</em> be identified by the `http://opds-spec.org/auth/document` relationship and `application/vnd.opds.authentication.v1.0+json` MIME media type.

Simple example for Authentication Document discovery using the Link header.

```
Link: <http://example.com/auth_document>;                              
         rel="http://opds-spec.org/auth/document";
         type="application/vnd.opds.authentication.v1.0+json"

```

### 2.5. Client

An OPDS client <em class="rfc">must</em> detect the presence of an Authentication Document, no matter whether it is served as the "Content" of an HTTP response, or in the "Link" header.

Once the client detects the Authentication Document, it <em class="rfc">must</em> select and trigger a specific Authentication Flow listed in the Authentication Document.

For some Authentication Flows, the client <em class="rfc">must</em> display an Authentication Page. An Authentication Page <em class="rfc">must</em> display all the information provided in the Authentication Document, which includes: title, logo, text prompt, labels, registration and support links.

## 3. Authentication Flows

### 3.1. Introduction

*This section is informative.*

In addition to the Authentication Document, this specification also defines multiple scenarios to handle how the client is authenticated.

### 3.2. Use of TLS

All resources <em class="rfc">must</em> use Transport Layout Security (TLS) during each step of an Authentication Flow.

Implementations <em class="rfc">may</em> also support additional transport-layer security mechanisms that meet their security requirements.

### 3.3. Authentication Object

Each Authentication Document contains at least one Authentication Object that describes how a client can leverage an Authentication Flow.

The Authentication Document must contain at least a `type`:

|Name|Value|Format/Data Type|
|----|-----|----------------|
|`type`|A URI that identifies the nature of an Authentication Flow.|URI|

#### 3.3.1. Labels

An Authentication Object <em class="rfc">may</em> also include a `labels` object.

The `labels` object is meant to provide alternate labels for fields that the client will display to the user. All alternate labels <em class="rfc">must</em> be provided as a string.

It <em class="rfc">may</em> contain the following name/value pairs:

|Label|Semantics|Authentication Flows|
|-----|---------|--------------------|
|`login`|Alternate label for a login.|Basic Authentication<br />OAuth with Resource Owner Password Credentials Grant|
|`password`|Alternate label for a password.|Basic Authentication<br />OAuth with Resource Owner Password Credentials Grant|

#### 3.3.2. Links

An Authentication Object <em class="rfc">may</em> also include a `links` object using the same syntax as defined in 2.3.2. Links.

The following link relations are defined in this context:

|Relation|Semantics|Required?|
|--------|---------|---------|
|`authenticate`|Location where a client can authenticate the user with OAuth.|Yes if the Authentication Flow list contains OAuth|
|`refresh`|Location where a client can refresh the Access Token by sending a Refresh Token.|No|

### 3.4. Basic Authentication

In order to identify the use of Basic Authentication as defined in [[RFC2617](http://tools.ietf.org/html/rfc2617)], this specification defines a new `type` value:

|Value|Scope|
|-----|-----|
|`http://opds-spec.org/auth/basic`|The `type` object as defined in [3.3. Authentication Object](#33-authentication-object)|

A client that detects and decides to use Basic Authentication as an Authentication Flow for a catalog <em class="rfc">must</em> display an Authentication Page containing all the information available in the Authentication Document.
The Authentication Page <em class="rfc">must</em> contain two input fields for the username and password. If the Authentication Document includes alternate labels, the Authentication Page <em class="rfc">must</em> display these labels next to the input fields.

An OPDS client <em class="rfc">may</em> store these credentials for future use, but should make its best efforts to protect them.

Basic Authentication is weaker than the other Authentication Flows exposed in this specification, and exposes the client and resource owner to a higher risk. For this reason, both Authentication Provider and client <em class="rfc">should</em> minimize use of this Authentication Flow and utilize other Authentication Flows whenever possible.

### 3.5. OAuth 2.0

#### 3.5.1. introduction

*This section is informative.*

The OAuth 2.0 Authorization Framework is widely used to grant access to protected resources.

It defines several Authentication Flows (called authorization grant types in the OAuth specification) that all follow the same principle: in exchange for an authorization grant, the client receives a token to access protected resources.

This specification defines how a sub-set of OAuth 2.0 (a limited set of authorization grant types) can be used as Authentication Flows.
In addition to limiting the scope of OAuth 2.0, this specification also adds support for endpoint discovery through the use of the Authentication Document.

#### 3.5.2. A Shared Client Identifier

While OAuth favors client registration, the OPDS ecosystem favors discovery and maximum interoperability instead.

For this reason, any Authentication Provider that supports OPDS Authentication 1.0 and exposes an Authentication Flow based on OAuth <em class="rfc">must</em> identify all OPDS clients using the following information :

|Name|Identifier|Callback URI|
|----|----------|------------|
|OPDS client|http://opds-spec.org/auth/client|opds://authorize/|

The unique identifier used in the callback URI <em class="rfc">must</em> have the same value as the identifier provided in the Authentication Document.

Specific OPDS clients <em class="rfc">may</em> also be registered by each Catalog, in order to be further identified.

#### 3.5.3. Bearer Token

All of the OAuth-based Authentication Flows described in this document rely on the `Authorization` header field in HTTP to transmit an Access Token as described in section [2.1. Authorization Request Header Field](https://tools.ietf.org/html/rfc6750#section-2.1) of [[RFC6750](https://tools.ietf.org/html/rfc6750)].

Using a Bearer token to transmit an Access Token.

```http
GET /resource HTTP/1.1
Host: server.example.com
Authorization: Bearer mF_9.B5f-4.1JqM
```

#### 3.5.4. Security Considerations

All clients and Authentication Providers <em class="rfc">should</em> follow the security considerations as detailed in:

* Section [10. Security Considerations](https://tools.ietf.org/html/rfc6749#section-10) of the [[OAuth](https://tools.ietf.org/html/rfc6749)] specification
* Section [5. Security Considerations](https://tools.ietf.org/html/rfc6750#section-5) of the [[RFC6750](https://tools.ietf.org/html/rfc6750)] specification

#### 3.5.5. Implicit Grant

In order to identify the use of an Implicit Grant Authentication Flow (as defined in [[OAuth](https://tools.ietf.org/html/rfc6749)] in section [4.2. Implicit Grant](https://tools.ietf.org/html/rfc6749#section-4.2)), this specification defines a new type value:

|Value|Scope|
|-----|-----|
|`http://opds-spec.org/auth/oauth/implicit`|The `type` object as defined in [3.3. Authentication Object](#33-authentication-object)|

A valid Authentication Object that lists the Implicit Grant as an available Authentication Flow <em class="rfc">must</em> also include a link identified by the `authenticate` relationship.

If a client detects and decides to use this Authentication Flow, it <em class="rfc">must not</em> display an Authentication Page. Instead, it <em class="rfc">must</em> redirect the user to a webview or browser to the location indicated in the link identified by the `authenticate` relationship.

If the authentication is successful, the Authentication Provider <em class="rfc">must</em> provide an Access Token Response with the following restrictions:

* it <em class="rfc">must</em> use the OPDS callback URI as described in [3.5.2. A Shared Client Identifier](#352-a-shared-client-identifier)
* it <em class="rfc">must</em> follow the requirements from the [[OAuth](https://tools.ietf.org/html/rfc6749)] specification as described in [4.2.2. Access Token Response](https://tools.ietf.org/html/rfc6749#section-4.2.2)
* it <em class="rfc">must</em> use the `id` query parameter to indicate the Authentication Document identifier
* it <em class="rfc">must</em> use `bearer` as the value for the `token_type` query parameter

Example of an OPDS callback containing an Access Token

```
opds://authorize/?id=http%3A%2F%2Fexample.org%2Fauth.json&access_token=9b3dc428-df5f-4bd2-9f0d-72497cbf8464&token_type=bearer
```

An OPDS client can then associate this Access Token to an OPDS Catalog and its Authentication Provider using the unique identifier.

Upon receiving this Access Token, the client <em class="rfc">should</em> attempt to authenticate on the same resource that initially prompted the Authentication Document.

#### 3.5.6. Resource Owner Password Credentials Grant
In order to identify the use of a Resource Owner Password Credentials Grant Authentication Flow (as defined in [[OAuth](https://tools.ietf.org/html/rfc6749)] in section [4.3. Resource Owner Password Credentials Grant](https://tools.ietf.org/html/rfc6749#section-4.3)), this specification defines defines a new `type` value:

|Value|Scope|
|-----|-----|
|`http://opds-spec.org/auth/oauth/password`|The `type` object as defined in [3.3. Authentication Object](#33-authentication-object)|

A valid Authentication Object that lists the Resource Owner Password Credentials Grant as an available Authentication Flow <em class="rfc">must</em> also include an `authenticate` link.
It <em class="rfc">may</em> also include a `refresh` link if the Authentication Provider includes a Refresh Token in its Response Document.

A client that detects and decides to use Resource Owner Password Credentials as an Authentication Flow for a catalog <em class="rfc">must</em> display an Authentication Page containing all the information available in the Authentication Document.

The Authentication Page <em class="rfc">must</em> contain two input fields for the username and password. If the Authentication Document includes alternate labels, the Authentication Page <em class="rfc">must</em> display these labels next to the input fields.

An OPDS client <em class="rfc">must not</em> store these credentials for future use.

In case of a successful request, the OAuth Response Document <em class="rfc">must</em> return an Access Token as a bearer token.

#### 3.5.7. OAuth Response Document

> We'll define additional information that can show up in the document (such as LCP Keys or Vendor ID account).

### 3.6. Extensions

>Preference for JWT based extensions.
>
> All extensions identified by URIs (for types, labels and for link relationships)

## 4. References

### 4.1. Normative References

- [[JSON](http://www.ietf.org/rfc/rfc4627)] RFC 4627 - The application/json Media Type for JavaScript Object Notation (JSON).
- [[Web-Linking](https://tools.ietf.org/html/rfc5988)] RFC 5988 - Web Linking.
- [[OAuth](https://tools.ietf.org/html/rfc6749)] RFC 6749 - The OAuth 2.0 Authorization Framework.
