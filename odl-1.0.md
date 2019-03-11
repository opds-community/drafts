[![OPDS Logo](https://opds.io/img/logo.png)](https://opds.io)

# Open Distribution to Libraries 1.0

**This version:**

* [https://drafts.opds.io/odl-1.0](https://drafts.opds.io/odl-1.0)

**Editors:**

* Hadrien Gardeur ([Feedbooks](http://www.feedbooks.com))

## Status of this Document

This document is a draft of the 1.0 version of the ODL specification.

## 1. Overview

### 1.1 Purpose and Scope

*This section is informative*

In order to distribute digital publications from multiple sources, a library needs to be able to easily harvest metadata, check the status and lend such publications to patrons.

This specification, Open Distribution to Libraries 1.0 (referenced as ODL in the rest of this specification) defines a standard way to:

* distribute metadata about a publication
* provide the rights and terms associated to each copy that the library acquired
* check the status of each copy
* lend a copy of a digital publication
* check the status of that loan

### 1.2 Terminology

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

### 1.3. XML Namespace

The namespace name [REC-xml-names] for new elements defined in this specification is: `http://opds-spec.org/odl`

## 2. ODL Feed

### 2.1 Relationship to OPDS

Open Distribution to Libraries is based on the Open Publication Distribution System [[OPDS](#normative-references)] specification and fully compatible with both OPDS 1.2 and 2.0.

All of the information (metadata, licenses and links) are distributed through an OPDS Feed.

An ODL Feed  <em class="rfc">must</em> be a valid OPDS Acquisition Feed as defined in [OPDS] with one difference:

* the [[OPDS](#normative-references)] specification states that: "Each OPDS Catalog Entry Document  <em class="rfc">must</em> include at least one Acquisition Link, which is an atom:link element with a relation that begins "http://opds-spec.org/acquisition"."
* in the context of an Open Distribution to Libraries Feed this is changed to: "Each OPDS Catalog Entry Document  <em class="rfc">must</em> include at least one Open-Access Acquisition Link (http://opds-spec.org/acquisition/open-access) or one License that contains a Checkout Link."


### 2.2 Additional requirements

In addition to the [[OPDS](#normative-references)] requirements for an Acquisition Feed, this specification defines the following requirements for a valid ODL Feed:

* each Acquisition Entry  <em class="rfc">must</em> include at least one License containing a Checkout Link
* in the case where an Acquisition Entry has both an `atom:summary` and an `atom:content` it  <em class="rfc">must</em> include both in its Partial Catalog Entry
* if an OPDS Catalog Entry Document has a Partial Catalog Entry it  <em class="rfc">should</em> contain as much metadata as possible and assume that not all clients will access the Complete Catalog Entry

## 3. Licenses

### 3.1. Describing a License

A License <em class="rfc">must</em> contain the following metadata:

| OPDS 2.0 | OPDS 1.2 | Semantics | Format |
| -------- | -------- | --------- | ------ |
| `identifier` | `dcterms:identifier` | A unique identifier for this Copy | URI |
| `format` |  `dcterms:format` | Format of the publication associated to the license | MIME type |
| `created` |  `created` | Date and time when the license was issued | ISO 8601 |

In addition to these metadata, a License  <em class="rfc">must</em> also contain two links:

* a [Checkout Link](#4-checkouts)
* a [Copy Status Link](#351-copy-status-document)

A License <em class="rfc">should</em> also contain the following metadata:

| OPDS 2.0 | OPDS 1.2 | Semantics | Format |
| -------- | -------- | --------- | ------ |
| `terms` |  `odl:terms` | Terms associated to the Copy | [See Section 3.2](#32-the-terms-element) |
| `protection` |  `odl:protection` | Protection associated to the Publication associated to the Copy | [See Section 3.3](#33-the-protection-element) |

Finally, a License  <em class="rfc">may</em> also contain the following metadata:

| OPDS 2.0 | OPDS 1.2 | Semantics | Format |
| -------- | -------- | --------- | ------ |
| `price` |  `opds:price` | Price at which the library acquired the Copy | As defined in [[OPDS](#normative-references)] and [[OPDS-2](#normative-references)]|
| `source` |   `dcterms:source` | Source of the license | URI |

### 3.2 The `terms` element

The `terms` element  <em class="rfc">may</em> contain the following elements:

| Name | Value | Format | Default value |
| ---- | ----- | ------ | ------------- |
| `total_checkouts` | Number of Checkouts before a Copy expires | Integer | Unlimited |
| `expires` | Expiration date of the Copy | ISO 8601 | None |
| `concurrent_checkouts` | Number of concurrent Checkouts allowed | Integer | Unlimited |
| `maximum_checkout_length` | Maximum length in time allowed for a single Checkout in seconds | Integer | Unlimited |

### 3.3 The `protection` element

The `protection` element  <em class="rfc">may</em> contain the following elements:

| Name | Value | Format | Default value |
| ---- | ----- | ------ | ------------- |
| `dcterms:format` | Format of the DRM used to protect the Publication | MIME type | None |
| `devices` | Number of devices allowed for a single Checkout | Integer | Unlimited |
| `copy` | Indicates if the protection allows the user to copy content from the Publication | Boolean | True |
| `print` | Indicates if the protection allows the user to print content from the Publication | Boolean | True |
| `tts` | Indicates if the protection allows the user to use text to speech | Boolean | True |

### 3.4 Examples

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

*Example 2: Simple license in OPDS 2.0*

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
        "total_checkouts": 30,
        "expires": "2016-04-25T12:25:21+02:00",
        "concurrent_checkouts": 10,
        "max_checkout_length": 5097600
      },
      "protection": {
        "format": "application/vnd.adobe.adept+xml",
        "devices": 6,
        "copy": false,
        "print": false,
        "tts": false
      }
    },
    "links": [
      {
        "rel": "http://opds-spec.org/acquisition/borrow",
        "href": "http://www.example.com/get{?id,checkout_id,expires,patron_id,notification_url}",
        "type: "application/vnd.readium.license.status.v1.0+json",
        "templated": true
      },
      {
        "rel": "self",
        "href": "http://www.example.com/status/294024",
        "type: "application/vnd.odl.info+json"
      },
    ]
  }
]
```

### 4. License Info Document

In order to provide information about the current status of a License, this specification defines the License Info Document which  <em class="rfc">must</em> be:

* a valid [[JSON](#normative-references)] document
* identified by the `application/vnd.odl.license+json` media type

The License Info Document <em class="rfc">must</em> contain the following keys:

| Key | Semantics | Format |
| --- | --------- | ------ |
| `status` | Indicates the status of a license | Boolean |
| `checkouts` | List of currently active Checkouts | See below |

The `checkouts` key is an array in which each element  <em class="rfc">must</em> have the following keys:

| Key | Semantics | Format |
| --- | --------- | ------ |
| `id` | Unique identifier for a Checkout | String |
| `href` | URI where the LCP Status Document for this specific Checkout is available | URI |
| `expires` | Expiration date and time for the Checkout using UTC.| ISO 8601 |
| `patron_id` | Unique identifier for the patron | String |

The License Info Document  <em class="rfc">may</em> also have the following key/value pairs:

| Name | Value | Format | Default value |
| ---- | ----- | ------ | ------------- |
| `expiration_date` | Date when the License expires | ISO 8601 | None |
| `total_checkouts_left` | Total number of checkouts left before the License expires | Integer | Unlimited |
| `concurrent_checkouts_available` | Total number of concurrent Checkouts available | Integer | Unlimited |

#### 4.1. Linking to the License Info Document

A `copy` element can link to a Copy Status Document by using a `link` element with the following requirements:

* its `rel` value  <em class="rfc">must</em> be `self`
* its `type` value  <em class="rfc">must</em> be `application/vnd.odl.info+json`

#### 4.2. Example

```json
{
   "expired": false,
   "expiration_date": "2015-04-03T11:47:54Z",
   "checkout_available": false,
   "total_checkouts_left": 16,
   "concurrent_checkouts_available": 0,
   "checkouts": [
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
```

## 5. Checkouts

### 5.1 The `tlink` element

In order to introduce the ability to check out a Publication, this specification introduces a new element based on the `link` element in [[Atom](http://tools.ietf.org/html/rfc4287)]: the `tlink` element.

The `tlink` element inherits the semantics and syntax of the link element with the following modification to [section 4.2.7.1.  The "href" Attribute](https://tools.ietf.org/html/rfc4287#section-4.2.7.1):

"The "href" attribute contains the link's IRI. atom:link elements  <em class="rfc">must</em> have an href attribute, whose value  <em class="rfc">must</em> be a IRI reference [[RFC3987](http://tools.ietf.org/html/rfc3987)] or a IRI template [[RFC6570](http://tools.ietf.org/html/rfc6570)]."

### 5.2 Checkout Link

A `tlink` element identified by the "http://opds-spec.org/acquisition/borrow" relationship, points to a templated URI to create a Checkout.

Such a `tlink` is called a Checkout Link.

This specification defines a set of well-known parameters associated to this URI template.

A Checkout Link  <em class="rfc">must</em> have the following parameters:

| Parameter | Semantics | Format |
| --------- | --------- | ------ |
| `id` | Unique identifier for the Copy | String |
| `checkout_id` | Unique identifier for the Checkout | String |
| `patron_id` | Unique identifier for the patron | String |

A Checkout Link  <em class="rfc">may</em> also have the following parameters:


| Parameter | Semantics | Format | Default value |
| --------- | --------- | ------ | ------------- |
| `expires` | Expiration date for the Checkout | ISO 8601 | None |
| `notification_url` | URL where the library will be notified that the status of the Checkout changed | URI | None |

*Here's an example of such a templated link:*

```xml
<odl:tlink rel="http://opds-spec.org/acquisition/borrow" 
           href="http://library_example.com/loan{?id,checkout_id,expires,patron_id,notification_url}" 
           type="application/vnd.readium.license.status.v1.0+json" />
```

### 5.3. Interacting with a Checkout Link

In order to create a Checkout, an ODL client  <em class="rfc">must</em> send a `POST` request to the Checkout Link with all the required parameters.

In the case of a successful interaction with a Checkout Link, the server  <em class="rfc">must</em> return a valid LCP Status Document [[LCP-Status](https://docs.google.com/document/d/1ErBf0Gl32jNH-QVKWpGPfZDMWMeQP7dH9YY5g7agguQ/edit?usp=sharing)] with a `201` ("Created") HTTP status code.

If a Checkout with the same `id` and `checkout_id` was created before, the server  <em class="rfc">must</em> return a `303` ("See Other") HTTP status code, with the Location header pointing to the LCP Status Document [[LCP-Status](https://docs.google.com/document/d/1ErBf0Gl32jNH-QVKWpGPfZDMWMeQP7dH9YY5g7agguQ/edit?usp=sharing)] that was previously created.

The server  <em class="rfc">must</em> NOT update the Checkout with the new parameters sent by the client in this case.

The LCP Status Document  <em class="rfc">must</em> contain at least two links:

* a link identified by the self relationship, that points back to this LCP Status Document
* at least one link to acquire the Checkout's license file, identified by the license relationship

A server  <em class="rfc">must</em> respond with a Problem Details JSON Object [[Problem-Details](https://tools.ietf.org/html/draft-ietf-appsawg-http-problem-00)] whenever a `4xx` or a `5xx` HTTP status code is sent back to the client.

This specification defines the following list of supported types:

| Type | Description | HTTP Status Code |
| ---- | ----------- | ---------------- |
| http://opds-spec.org/odl/error | Generic error when a specific type can't be tied to the error. | 4xx, 5xx |
| http://opds-spec.org/odl/error/checkout/id | Incorrect or missing Copy identifier when creating a Checkout. | 400 |
| http://opds-spec.org/odl/error/checkout/checkout_id | Incorrect or missing Checkout identifier when creating a Checkout. | 400 |
| http://opds-spec.org/odl/error/checkout/patron_id | Incorrect or missing patron identifier when creating a Checkout. | 400 |
| http://opds-spec.org/odl/error/checkout/expires | Incorrect or missing expiration date when creating a Checkout. | 400 |
| http://opds-spec.org/odl/error/checkout/notification_url | Incorrect notification URL (usually means that it's not a URL). | 400 |
| http://opds-spec.org/odl/error/checkout/expired | The Copy has expired and a Checkout cannot be created. | 403 |
| http://opds-spec.org/odl/error/checkout/unavailable | The Copy has reached its concurrent Checkouts limit and is currently unavailable. | 403 |

### 5.4. Status of a Checkout

A client can get at anytime the status of a Checkout by doing an HTTP `GET` request to the `self` link included in the LCP Status Document initially returned after checking out a Publication.

The LCP Status Document contains a `status` element that provides the current status of the Checkout along with a timestamp.

## 6. Notifications

Notifications  <em class="rfc">should</em> be sent for the following status changes:

* `active`
* `returned`
* `cancelled`
* `revoked`

A notification  <em class="rfc">may</em> be sent for the following status change:

* `expired`

In order to notify such changes, the source for the ODL feed  <em class="rfc">must</em>:

* send a `POST` request to the notification URI provided through the Checkout Link
* include a minimal LCP Status Document in the body of the request

The minimal LCP Status Document  <em class="rfc">must</em> contain at least the following elements:

* `id`
* `status`

*Example of a minimal LCP Status Document used for a notification:*

```json
{
  "id": "3c649cda-d8c5-4d48-bd80-85aff3fcb734",
  "status": "returned"
}
```

A successful request  <em class="rfc">must</em> return a `204` ("No Content") HTTP status code, with no additional body content to the response.

If another HTTP status is returned, additional attempts to send a notification  <em class="rfc">should</em> be made.

## 7. Authentication

In order to secure the ability to harvest Licenses and create Checkouts, an ODL server  <em class="rfc">should</em> use either Basic Authentication or a Bearer Token to limit the access to the ODL feed.

All interactions with an ODL server <em class="rfc">must</em> use TLS 1.0 or later.

<style>
.rfc {
    color: #d55;
    font-variant: small-caps;
    font-style: normal;
}
</style>

## Normative References

* [JSON] [The application/json Media Type for JavaScript Object Notation (JSON)](https://www.ietf.org/rfc/rfc4627).
* [OPDS] [OPDS Catalog 1.2](https://specs.opds.io/opds-1.2)
* [OPDS-2] [OPDS Catalog 2.0](https://drafts.opds.io/opds-2.0)
* [RFC2119] [Key words for use in RFCs to Indicate Requirement Levels](https://tools.ietf.org/html/rfc2119).
* [URI-Template] [URI Template](https://tools.ietf.org/html/rfc6570).
