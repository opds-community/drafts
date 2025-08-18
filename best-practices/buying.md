# Best Practices: Buying Publications with OPDS 2.0

While buying was one of the original core use case for OPDS, it has become less prevalent as mobile platforms restricted the ability to use third-party payment systems.

With increased interest from vendors building eReaders and eTablets, plus a legal pushback forcing Apple/Google to open-up, there's a renewed focus this use case.

This document covers best practices that catalogs and clients should be aware of when implementing this use case.

## 1. Buy Links

OPDS 2.0 requires publications to include at least one [Acquisition Link](../opds-2.0.md#53-acquisition-links). 

For publications that can be purchased, this means using `http://opds-spec.org/acquisition/buy` as the `rel` value of a Link Object.

Since buying can be a complex operation that can require the user to accept specific terms and set up a payment option, most catalogs will rely on a browser to finalize the transaction.

This means that buy link usually rely on `text/html` in `type` plus [`indirectAcquisition`](https://drafts.opds.io/opds-2.0#53-acquisition-links) to indicate to the user the format and/or DRM of what they're buying.

**Example: Buy link for an LCP protected EPUB**

```json
{
  "href": "https://example.com/buy",
  "rel": "http://opds-spec.org/acquisition/buy",
  "type": "text/html",
  "properties": {
    "price": {
      "value": 7.99,
      "currency": "EUR"
    },
    "indirectAcquisition": [
      {
        "type": "application/vnd.readium.lcp.license.v1.0+json",
        "child": [{"type": "application/epub+zip"}]
      }
    ]
  }
}
```

## 2. Price and Currency

OPDS 2.0 supports a [`price`]() object in the `properties` of a link where:

* `value` contains the price (expressed using a number)
* and `currency` contains the currency (using a three letter code)

Since price, taxes and currency can be affected by the payment and invoicing information of the user, it's recommanded to authenticate the user whenever possible. 

If not, the catalog may default to geolocation using the client's IP address for example.

Optional authentication can be handled using an `authenticate` property that points to an [Authentication Document](../authentication-for-opds-1.0.md#2-authentication-document) either on:

* a link to an OPDS feed
* or a `self` link for a publication

**Example: `self` link with optional authentication**

```json
{
  "href": "https://example.com/publication",
  "rel": "self",
  "type": "application/opds-publication+json",
  "properties": {
    "authenticate": "https://example.com/authentication"
  }
}
```

## 3. Bookshelf

Allowing users to read on any device is one of the core principles of OPDS.

For catalogs that allow users to buy books, this means that all purchases must show up in an OPDS bookshelf where they can be freely downloaded again.

To maximize the discoverability of the user's bookshelf, catalogs must include a link to this bookshelf in their [Authentication Document](../authentication-for-opds-1.0.md#2-authentication-document).

**Example: Authentication Document with a link to a bookshelf**

```json
{
  "id": "https://example.com/authentication",
  "title": "Retailer A",
  "description": "Enter your username and password to access your books purchased at Retailer A.",
  "links": [
    {
      "rel": "http://opds-spec.org/shelf", 
      "href": "https://example.com/user/shelf", 
      "type": "application/opds+json"
    }
  ],
  "authentication": [
    {
      "type": "http://opds-spec.org/auth/basic"
    }
  ]
}
```

In addition, it's also recommended to include a link to this bookshelf in all OPDS feeds returned by a catalog.


**Example: Link to a bookshelf from an OPDS feed**

```json
{
  "rel": "http://opds-spec.org/shelf", 
  "href": "https://example.com/user/shelf", 
  "type": "application/opds+json",
  "properties": {
    "authenticate": "https://example.com/authentication"
  }
 }
```

It's recommended to support both [facets](../opds-2.0.md#24-facets) and [search](../opds-2.0.md#3-search) scoped to the bookshelf rather than the entire catalog.

## 4. Callbacks

Once the payment has been completed, a catalog will usually display download links for the newly purchased publications.

In addition to these links, a catalog may also display a callback using the `opds://` URI scheme. 

This callback should either point to a specific publication or to a user's bookshelf and may require the user to authenticate if they haven't previously done so.

**Example: Link to a bookshelf from an HTML page**

```html
<a href="opds://example.com/user/shelf">Download in your reading app</a>
```

Through these callbacks, OPDS clients should:

- allow the user to download their publications
- and automatically discover new OPDS bookshelves

## Appendix A - Catalog Examples

* TBD

## Appendix A - Error Handling

TODO
