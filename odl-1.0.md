# Open Distribution to Libraries 1.0


In order to distribute digital publications from multiple sources, a library needs to be able to easily harvest metadata and lend such publications to patrons.

This specification, Open Distribution to Libraries 1.0 (referenced as ODL in the rest of this specification) defines a standard way to:

* distribute metadata about a publication
* provide the rights and terms associated to each license that the library acquired
* check the status of each license
* lend a digital publication
* check the status of that loan

**This version:**

* [https://drafts.opds.io/odl-1.0](https://drafts.opds.io/odl-1.0)

**Editors:**

* Hadrien Gardeur ([De Marque](http://www.demarque.com))

## Status of this Document

This document is a draft of the 1.0 version of the ODL specification.

## 1. Overview

### 1.1. Terminology

*OPDS terms*

This specification adopts terms defined in [[OPDS](#normative-references)]. Important terms used include:

<dl>
	<dt>Acquisition Feed</dt>
	<dd>An Atom Feed whose Atom Entries are exclusively OPDS Catalog Entries.</dd>

  <dt>Acquisition Link</dt>
  <dd>An atom:link element with a relation that begins with <code>http://opds-spec.org/acquisition</code> and refers to the Resource which holds the content of the described Publication or the Resource through which it may be acquired for any OPDS Catalog Entry. See the Sections Acquisition Relations and Acquiring Publications. They are serialized as OPDS Catalog Feed Documents.</dd>

  <dt>Complete Catalog Entry</dt>
  <dd>An OPDS Catalog Entry that includes all known metadata about the described Publication and is referenced by a Partial Catalog Entry.</dd>

  <dt>Navigation Feed</dt>
  <dd>An Atom Feed whose Atom Entries are not OPDS Catalog Entries but instead links to other Navigation Feeds, Acquisition Feeds, or other Resources to establish a hierarchical, browsable presentation of the OPDS Catalog.</dd>

  <dt>OPDS Catalog Entry</dt>
  <dd>An Atom Entry that provides a representation of an available Publication and includes an Acquisition Link. They are serialized as OPDS Catalog Entry Documents.</dd>

 <dt>Partial Catalog Entry</dt>
 <dd>An OPDS Catalog Entry that includes the minimal required metadata about the described Publication but no other metadata and links to the Complete Catalog Entry.</dd>

  <dt>Publication</dt>
  <dd>An electronic document, typically available as a single file. OPDS Catalogs are agnostic about the particular format of Publications.</dd>

</dl>

*ODL terms*

In addition this specification defines the following terms:

<dl>

  <dt>Checkout Link</dt>
  <dd>A URI template from which a library can generate a loan for a patron.</dd>

  <dt>License</dt>
  <dd>A set of terms and rights and a Checkout Link from which a loan can be created.</dd>

  <dt>License Info Document</dt>
  <dd>A JSON document providing additional information about the current status of a license.</dd>

 <dt>ODL Feed</dt>
 <dd>An OPDS Feed from which a library can harvest metadata, copies and links.</dd>
 
</dl>

### 1.2. XML Namespace

In order to provide compatibility with [[OPDS](#normative-references)], this specification introduces the `odl` XML namespace: `http://drafts.opds.io/odl-1.0#`.

### 1.3. Conformance Statements

The keywords <strong class="rfc">must</strong>, <strong class="rfc">must not</strong>, <strong class="rfc">required</strong>, <strong class="rfc">shall</strong>, <strong class="rfc">shall not</strong>, <strong class="rfc">should</strong>, <strong class="rfc">should not</strong>, <strong class="rfc">recommended</strong>, <strong class="rfc">may</strong>, and <strong class="rfc">optional</strong> in this document are to be interpreted as described in [[RFC2119](#normative-references)].

All sections of this specification are normative except where identified by the informative status label "This section is informative". The application of informative status to sections and appendices applies to all child content and subsections they may contain.

All examples in this specification are informative.

## 2. ODL Feed

Open Distribution to Libraries is based on the Open Publication Distribution System specification and fully compatible with both  [[OPDS](#normative-references)] and [[OPDS-2](#normative-references)].

All of the information are distributed through an OPDS Feed.

### 2.1. OPDS 1.2

If the ODL Feed is serialized using [[OPDS](#normative-references)]:

* It <em class="rfc">must</em> be a valid OPDS Acquisition Feed as defined in  [[OPDS](#normative-references)] with one difference:

  * the [[OPDS](#normative-references)] specification states that: "Each OPDS Catalog Entry Document  <em class="rfc">must</em> include at least one Acquisition Link, which is an atom:link element with a relation that begins "http://opds-spec.org/acquisition"."
  * in the context of an Open Distribution to Libraries Feed this is changed to: "Each OPDS Catalog Entry Document  <em class="rfc">must</em> include at least one Open-Access Acquisition Link ([http://opds-spec.org/acquisition/open-access](http://opds-spec.org/acquisition/open-access)) or a License."

* In the case where an Acquisition Entry has both an `atom:summary` and an `atom:content` it  <em class="rfc">must</em> include both in its Partial Catalog Entry
* If an OPDS Catalog Entry Document has a Partial Catalog Entry it  <em class="rfc">should</em> contain as much metadata as possible and assume that not all clients will access the Complete Catalog Entry

### 2.1. OPDS 2.0

If the ODL Feed is serialized using [[OPDS-2](#normative-references)]:

* It <em class="rfc">must</em> be a valid OPDS Feed as defined in [[OPDS-2](#normative-references)] with one difference:
  * The requirement for the presence of an Acquisition Link is relaxed
  * Instead, each Publication listed in `publications` <em class="rfc">must</em> either contain a `licenses` subcollection or an Open-Access Acquisition Link ([http://opds-spec.org/acquisition/open-access](http://opds-spec.org/acquisition/open-access))
* It <em class="rfc">must</em> contain `publications`
* It <em class="rfc">must not</em> contain `groups`, `facets` or `navigation`

## 3. Licenses

### 3.1. Serialization

Licenses are serialized using:

* `odl:license` in [[OPDS](#normative-references)] where `odl:license` is repeated for each License available
* `licenses` in [[OPDS-2](#normative-references)] which contains one or more Full Collections (a Collection that contains both `metadata` and `links`)

In [[OPDS](#normative-references)], both the description and the links associated to a License are listed directly inside `odl:license`.

In [[OPDS-2](#normative-references)], the description of the License is expressed in `metadata` while all interactions (License Info Document and Checkouts) are listed under `links`.

### 3.2. Describing a License

A License <em class="rfc">must</em> contain the following metadata:

| OPDS 2.0 | OPDS 1.2 | Semantics | Format |
| -------- | -------- | --------- | ------ |
| `identifier` | `dcterms:identifier` | Unique identifier for the License | URI |
| `format` |  `dcterms:format` | Format of the publication associated to the License | MIME type |
| `created` |  `created` | Date and time when the License was issued | ISO 8601 |

A License <em class="rfc">should</em> also contain the following metadata:

| OPDS 2.0 | OPDS 1.2 | Semantics | Format |
| -------- | -------- | --------- | ------ |
| `terms` |  `odl:terms` | Terms associated to the License | [See Section 3.3](#33-terms) |
| `protection` |  `odl:protection` | Protection associated to the Publication associated to the License | [See Section 3.4](#34-protection) |

Finally, a License  <em class="rfc">may</em> also contain the following metadata:

| OPDS 2.0 | OPDS 1.2 | Semantics | Format |
| -------- | -------- | --------- | ------ |
| `price` |  `opds:price` | Price at which the library acquired the License | As defined in [[OPDS](#normative-references)] and [[OPDS-2](#normative-references)]|
| `source` |   `dcterms:source` | Source of the license | URI |

### 3.3. Terms

The `terms` element  <em class="rfc">may</em> contain the following elements:

| OPDS 2.0 | OPDS 1.2 | Semantics | Format | Default Value |
| -------- | -------- | --------- | ------ | ------------- |
| `checkouts` |  `total_checkouts` | Number of Checkouts before a License expires | Integer | Unlimited |
| `expires` | `expires` | Expiration date of the License | ISO 8601 | None |
| `concurrency` | `concurrent_checkouts` | Number of concurrent Checkouts allowed | Integer | Unlimited |
| `length` | `maximum_checkout_length` | Maximum length in time allowed for a single Checkout in seconds | Integer | Unlimited |

### 3.4. Protection

The `protection` element  <em class="rfc">may</em> contain the following elements:

| OPDS 2.0 | OPDS 1.2 | Semantics | Format | Default Value |
| -------- | -------- | --------- | ------ | ------------- |
| `format` | `dcterms:format` | Format of the DRM used to protect the Publication | MIME type | None |
| `devices` | `devices` | Number of devices allowed for a single Checkout | Integer | Unlimited |
| `copy` | `copy` | Indicates if the protection allows the user to copy content from the Publication | Boolean | True |
| `print` | `print` | Indicates if the protection allows the user to print content from the Publication | Boolean | True |
| `tts` | `tts` | Indicates if the protection allows the user to use text to speech | Boolean | True |

### 3.5. Examples

*Example 1: Simple license in OPDS 1.2*

```xml
<odl:license>
  <dcterms:identifier xsi:type="dcterms:URI">urn:uuid:f7847120-fc6f-11e3-8158-56847afe9799</dcterms:identifier>
  <dcterms:format>application/epub+zip</dcterms:format>
  <opds:price currencycode="USD">7.99</opds:price>
  <created>2014-04-25T12:25:21+02:00</created>

  <odl:terms>
    <odl:total_checkouts>30</odl:total_checkouts>
    <odl:expires>2016-04-25T12:25:21+02:00</odl:expires>
    <odl:concurrent_checkouts>10</odl:concurrent_checkouts>
    <odl:max_checkout_length>5097600</odl:max_checkout_length>
  </odl:terms>

  <odl:protection>
    <dcterms:format>application/vnd.adobe.adept+xml</dcterms:format>
    <odl:devices>6</odl:devices>
    <odl:copy>false</odl:copy_paste>
    <odl:print>false</odl:print>
    <odl:tts>false</odl:tts>
  </odl:protection>

  <odl:tlink rel="http://opds-spec.org/acquisition/borrow" 
             href="http://www.example.com/get{?id,checkout_id,expires,patron_id,notification_url}" 
             type="application/vnd.readium.license.status.v1.0+json" />

  <link rel="self" 
        href="http://www.example.com/status/294024" 
        type="application/vnd.odl.info+json" />
</odl:license>
```

*Example 2: License in OPDS 2.0 with two DRM options available*

```json
"licenses": [
  {
    "metadata": {
      "identifier": "urn:uuid:f7847120-fc6f-11e3-8158-56847afe9799",
      "format": "application/epub+zip",
      "price": {
        "currency": "USD",
        "value": 7.99
      },
      "created": "2014-04-25T12:25:21+02:00",
      "terms": {
        "checkouts": 30,
        "expires": "2016-04-25T12:25:21+02:00",
        "concurrency": 10,
        "length": 5097600
      },
      "protection": {
        "format": [
          "application/vnd.adobe.adept+xml", 
          "application/vnd.readium.lcp.license.v1.0+json"
        ],
        "devices": 6,
        "copy": false,
        "print": false,
        "tts": false
      }
    },
    "links": [
      {
        "rel": "http://opds-spec.org/acquisition/borrow",
        "href": "http://www.example.com/get{?id,checkout_id,expires,patron_id,passphrase,hint,hint_url,notification_url}",
        "type": "application/vnd.readium.license.status.v1.0+json",
        "templated": true
      },
      {
        "rel": "self",
        "href": "http://www.example.com/status/294024",
        "type": "application/vnd.odl.info+json"
      },
    ]
  }
]
```

## 4. License Info Document


In order to provide information about the current state of a License, this specification defines the License Info Document. 

Each License in an ODL Feed <em class="rfc">must</em> contain a link to a License Info Document where:

* its `rel` value  <em class="rfc">must</em> be `self`
* its `type` value  <em class="rfc">must</em> be `application/vnd.odl.info+json`

A License Info Document <em class="rfc">must</em>:

* be a valid [[JSON](#normative-references)] document
* return the `application/vnd.odl.info+json` media type in its HTTP headers

### 4.1. Syntax

The License Info Document <em class="rfc">must</em> contain the following keys:

| Key | Semantics | Format |
| --- | --------- | ------ |
| `identifier` | Unique identifier for the License | URI |
| `status` | Indicates the status of a license | `preorder`, `available` or `unavailable` |
| `checkouts` | Provides information about the availability of checkouts | Checkouts Object |

The Checkouts Object  <em class="rfc">must</em> have the following keys:

| Key | Semantics | Format |
| --- | --------- | ------ |
| `left` | Total number of checkouts left for a License | Integer |
| `available` | Number of concurrent checkouts currently available | Integer |
| `active` | Contains one or more active loans associated to the License | One or more Loan Object |

The Loan Object <em class="rfc">must</em> have the following keys:

| Key | Semantics | Format |
| --- | --------- | ------ |
| `href` | Location of the LCP License Status Document associated to the loan | URI |
| `id` | Unique identifier for the loan | String |
| `patron_id` | Unique identifier for the patron | String |
| `expires` | Date and time for the expiration of the loan | ISO 8601 |

In addition, the License Info Document <em class="rfc">may</em> also include the information available for a License in an ODL Feed:

| Key | Semantics | Format |
| --- | --------- | ------ |
| `format` | Format of the publication associated to the License | MIME type |
| `created` | Date and time when the License was issued | ISO 8601 |
| `terms` | Terms associated to the License | [See Section 3.3](#33-terms) |
| `protection` | Protection associated to the Publication associated to the License | [See Section 3.4](#34-protection) |
| `price` | Price at which the library acquired the License | As defined in [[OPDS-2](#normative-references)]|
| `source` | Source of the license | URI |

### 4.2. Example

*Example 3: a License Info Document with 18 total remaining checkouts, 8 available checkouts and 2 active ones*

```json
{
  "identifier": "3363f6c2-ed7d-11e3-b722-56847afe9799"
  "status": "available",
  "checkouts": {
    "left": 18,
    "available": 8,
    "active": [
      {
        "href": "https://www.example.com/3363f6c2-ed7d-11e3-b722-56847afe9799?transaction_id=36563230-0ef4-4659-a70e-cd6dcd0aeb9e",
        "patron_id": "f7847120-fc6f-11e3-8158-56847afe9799",
        "expires": "2014-06-24T11: 47: 54Z",
        "id": "36563230-0ef4-4659-a70e-cd6dcd0aeb9e"
      },
      {
        "href": "https://www.example.com/3363f6c2-ed7d-11e3-b722-56847afe9799?transaction_id=36563230-0ef4-4659-a70e-cd6dcd0aeb9d",
        "patron_id": "f7847120-fc6f-11e3-8158-56847afe9799",
        "expires": "2014-06-24T11: 47: 54Z",
        "id": "36563230-0ef4-4659-a70e-cd6dcd0aeb9d"
      }
    ]
  }
}
```

## 5. Checkouts

A Checkout Link is a URI template that returns a Readium License Status Document, through which a library can obtain a publication.

Each License in an ODL Feed <em class="rfc">must</em> contain a Checkout Link where:

* its `rel` value  <em class="rfc">must</em> be `http://opds-spec.org/acquisition/borrow`
* its `type` value  <em class="rfc">must</em> be `application/vnd.readium.license.status.v1.0+json`

### 5.1. Supporting URI Templates in OPDS 1.2

In order to check out a Publication, this specification introduces a new element based on the `link` element in [[RFC4287](#normative-references)]: the `tlink` element.

The `tlink` element inherits the semantics and syntax of the link element with the following modification to [section 4.2.7.1.  The "href" Attribute](https://tools.ietf.org/html/rfc4287#section-4.2.7.1):

"The "href" attribute contains the link's IRI. atom:link elements  <em class="rfc">must</em> have an href attribute, whose value  <em class="rfc">must</em> be a IRI reference [[RFC3987](#normative-references)] or a IRI template [[RFC6570](#normative-references)]."

### 5.2. URI Template of a Checkout Link

This specification defines a set of well-known parameters associated to the URI template of a Checkout Link.

A Checkout Link  <em class="rfc">must</em> have the following parameters:

| Parameter | Semantics | Format |
| --------- | --------- | ------ |
| `id` | Unique identifier for the License | String |
| `checkout_id` | Unique identifier for the Checkout | String |
| `patron_id` | Unique identifier for the patron | String |

A Checkout Link  <em class="rfc">may</em> also have the following parameters:

| Parameter | Semantics | Format | Required if present? |
| --------- | --------- | ------ | -------------------- |
| `expires` | Expiration date for the Checkout | ISO 8601 | Yes |
| `hint` | Hint displayed to the user when opening an LCP protected publication | String | Yes |
| `hint_url` | Hint URL available to the user when opening an LCP protected publication | URL | Yes |
| `notification_url` | URL where the library will be notified that the status of the Checkout changed | URL | No |
| `passphrase` | Hash of the passphrase that the patron will need to enter for an LCP protected publication | String | Yes |

### 5.3. Example

*Example 4: Checkout Link in OPDS 1.2*

```xml
<odl:tlink rel="http://opds-spec.org/acquisition/borrow" 
           href="http://library_example.com/loan{?id,checkout_id,expires,patron_id,notification_url}" 
           type="application/vnd.readium.license.status.v1.0+json" />
```

*Example 5: Checkout Link in OPDS 2.0*

```json
"links": [
  {
    "rel": "http://opds-spec.org/acquisition/borrow",
    "href": "http://library_example.com/loan{?id,checkout_id,expires,patron_id,notification_url}",
    "type": "application/vnd.readium.license.status.v1.0+json",
    "templated": true
  }
]
```

### 5.4. Interacting with a Checkout Link

**Expected Behavior**

* In order to create a Checkout, an ODL client  <em class="rfc">must</em> send a `POST` request to the Checkout Link with all the required parameters.
* In the case of a successful interaction with a Checkout Link, the server  <em class="rfc">must</em> return a valid Readium License Status Document as defined in [[LSD](#normative-references)] with a `201` HTTP status code.
* If a Checkout with the same `id` and `checkout_id` was created before, the server  <em class="rfc">must</em> return a `303` HTTP status code, with the `Location` header pointing to the Readium License Status Document that was previously created.<br />The server  <em class="rfc">must not</em> update the Checkout with the new parameters sent by the client in this case.

The Readium License Status Document returned by the server <em class="rfc">must</em> contain at least two links:

* a link identified by the `self` relation, that points back to this LCP Status Document
* at least one link to acquire the Checkout's license file, identified by the `license` relation

**Failure Modes**

A server  <em class="rfc">must</em> respond with a Problem Details JSON Object as defined in [[RFC7807](#normative-references)] whenever a `4xx` or a `5xx` HTTP status code is sent back to the client.

This specification defines the following list of supported types:

| Type | Description | HTTP Status Code |
| ---- | ----------- | ---------------- |
| http://opds-spec.org/odl/error | Generic error when a specific type can't be tied to the error. | 4xx<br /> 5xx |
| http://opds-spec.org/odl/error/checkout/id | Incorrect or missing License identifier. | 400 |
| http://opds-spec.org/odl/error/checkout/checkout_id | Incorrect or missing Checkout identifier. | 400 |
| http://opds-spec.org/odl/error/checkout/patron_id | Incorrect or missing patron identifier. | 400 |
| http://opds-spec.org/odl/error/checkout/expires | Incorrect or missing expiration date. | 400 |
| http://opds-spec.org/odl/error/checkout/notification_url | Incorrect notification URL (usually means that it's not a URL). | 400 |
| http://opds-spec.org/odl/error/checkout/passphrase | Incorrect or missing passphrase. | 400 |
| http://opds-spec.org/odl/error/checkout/hint | Incorrect or missing hint. | 400 |
| http://opds-spec.org/odl/error/checkout/hint_url | Incorrect or missing URL hint. | 400 |
| http://opds-spec.org/odl/error/checkout/expired | The License has expired. | 403 |
| http://opds-spec.org/odl/error/checkout/unavailable | The License has reached its concurrent or total Checkouts limit. | 403 |

### 5.4. Status of a Checkout

A client can get at anytime the status of a Checkout by doing an HTTP `GET` request to the `self` link included in the Readium License Status Document initially returned after checking out a Publication.

The Readium License Status Document contains a `status` element and a `timestamps` element that are both relevant to tracking changes.

## 6. Notifications

Notifications  <em class="rfc">should</em> be sent for the following status changes of a checkout:

* `active`
* `returned`
* `cancelled`
* `revoked`

A notification  <em class="rfc">may</em> be sent for the following status change:

* `expired`

In order to notify such changes, the source for the ODL feed  <em class="rfc">must</em>:

* send a `POST` request to the notification URI provided through the Checkout Link
* include a minimal Readium License Status Document in the body of the request

The minimal LCP Status Document  <em class="rfc">must</em> contain at least the following elements:

* `id`
* `status`

*Example 6: Minimal Readium License Status Document used for a notification:*

```json
{
  "id": "3c649cda-d8c5-4d48-bd80-85aff3fcb734",
  "status": "returned"
}
```

A successful request  <em class="rfc">must</em> return a `204` HTTP status code, with no additional body content to the response.

If another HTTP status is returned, additional attempts to send a notification  <em class="rfc">should</em> be made.

## 7. Authentication

In order to secure the ability to harvest Licenses and create Checkouts, an ODL server  <em class="rfc">should</em> use either Basic Authentication or a Bearer Token to limit the access to the ODL feed.

All interactions with an ODL server <em class="rfc">must</em> use TLS 1.2 or later.

## Normative References

* [JSON] [The application/json Media Type for JavaScript Object Notation (JSON)](https://www.ietf.org/rfc/rfc4627).
* [LSD] [Readium License Status Document](https://readium.org/lcp-specs/lsd.html).
* [OPDS] [OPDS Catalog 1.2](https://specs.opds.io/opds-1.2)
* [OPDS-2] [OPDS Catalog 2.0](https://drafts.opds.io/opds-2.0)
* [RFC2119] [Key words for use in RFCs to Indicate Requirement Levels](https://tools.ietf.org/html/rfc2119).
* [RFC3987] [Internationalized Resource Identifiers (IRIs)](https://tools.ietf.org/html/rfc3987).
* [RFC4287] [The Atom Syndication Format](https://tools.ietf.org/html/rfc4287).
* [RFC6570] [URI Template](https://tools.ietf.org/html/rfc6570).
* [RFC7807] [Problem Details for HTTP APIs](https://tools.ietf.org/html/rfc7807).
* [URI-Template] [URI Template](https://tools.ietf.org/html/rfc6570).

<style>
.rfc {
    color: #d55;
    font-variant: small-caps;
    font-style: normal;
    font-weight: normal;
}
</style>