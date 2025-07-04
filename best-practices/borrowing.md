# Best Practices: Borrowing a Publication in OPDS 2.0

With wide adoption from public libraries in multiple countries, borrowing has become one of the most common use-cases for OPDS 2.0.

This document covers best practices that implementers should be aware of when implementing this use case.

## 1. Borrow links

OPDS 2.0 requires publications to include at least one [acquisition link](../opds-2.0.md#53-acquisition-links). 

For publications that can be borrowed, this means using `http://opds-spec.org/acquisition/borrow` as the `rel` value of a Link Object.

While the specification is neutral about what acquisition links should return, this document recommends returning an OPDS publication:

```json
{
  "href": "https://example.com/borrow",
  "rel": "http://opds-spec.org/acquisition/borrow",
  "type": "application/opds-publication+json"
}
```

Returning an OPDS publication provides several benefits over returning the publication itself:

- it allows catalogs to provide multiple formats and/or DRM for the publication
- if the publication is protected by Readium LCP, it also allows [the auto-discovery of the user's passphrase](https://readium.org/lcp-specs/notes/lcp-key-retrieval.html#including-a-hashed-passphrase-in-an-opds-publication)
- and can include additional information, such as the expiration date for a loan

In order to properly set expectations for clients, borrow links should also include which publication formats will be available using [`indirectAcquisition`](https://drafts.opds.io/opds-2.0#53-acquisition-links).

For example, in the case of a publication available either as EPUB or PDF protected by LCP:

```json
{
  "href": "https://example.com/borrow",
  "rel": "http://opds-spec.org/acquisition/borrow",
  "type": "application/opds-publication+json",
  "properties": {
    "indirectAcquisition": [
      {
        "type": "application/vnd.readium.lcp.license.v1.0+json",
        "child": [{"type": "application/epub+zip"}, {"type": "application/pdf"}]
      }
    ]
  }
}
```

A successful interaction would therefore include two acquisition links in the OPDS publication returned by the catalog:

```json
{
  "href": "https://example.com/download/epub",
  "rel": "http://opds-spec.org/acquisition",
  "type": "application/vnd.readium.lcp.license.v1.0+json",
  "properties": {
    "availability": {
      "state": "available",
      "since": "2025-07-02T14:46:46.889Z",
      "until": "2025-08-01T14:46:46.889Z"
    },
    "indirectAcquisition": [{"type": "application/epub+zip"}],
    "lcp_hashed_passphrase": "+usAylGL6nyxGn7zH7YYO0ibG26tt5K+xkoDs/b/gKg="
  }
}
{
  "href": "https://example.com/download/pdf",
  "rel": "http://opds-spec.org/acquisition",
  "type": "application/vnd.readium.lcp.license.v1.0+json",
  "properties": {
    "availability": {
      "state": "available",
      "since": "2025-07-02T14:46:46.889Z",
      "until": "2025-08-01T14:46:46.889Z"
    },
    "indirectAcquisition": [{"type": "application/pdf"}],
    "lcp_hashed_passphrase": "+usAylGL6nyxGn7zH7YYO0ibG26tt5K+xkoDs/b/gKg="
  }
}
```

Borrow links usually require authentication from the client using [Authentication for OPDS 1.0](authentication-for-opds-1.0). 

In order to optimize this interaction, this document recommends using the `authenticate` hint:

```json
{
  "href": "https://example.com/borrow",
  "rel": "http://opds-spec.org/acquisition/borrow",
  "type": "application/opds-publication+json",
  "properties": {
    "authenticate": "https://example.com/authentication"
    "indirectAcquisition": [
      {
        "type": "application/vnd.readium.lcp.license.v1.0+json",
        "child": [{"type": "application/epub+zip"}, {"type": "application/pdf"}]
      }
    ]
  }
}
```

## 2. Bookshelf

Allowing users to read on any device is one of the core principles of OPDS.

For catalogs that allow users to borrow books, this means that all loans (and holds) must show up in an OPDS bookshelf where they can be freely downloaded again.

To maximize the discoverability of the user's bookshelf, catalogs must include a link to this bookshelf in their [Authentication Document](../authentication-for-opds-1.0.md#2-authentication-document):

```json
{
  "id": "https://example.com/authentication",
  "title": "Public Library",
  "description": "Enter a valid library card number and PIN code to authenticate on our service.",
  "links": [
    {
      "rel": "http://opds-spec.org/shelf", 
      "href": "https://example.com/user/loans", 
      "type": "application/opds+json"
    }
  ],
  "authentication": [
    {
      "type": "http://opds-spec.org/auth/basic",
      "labels": {
        "login": "Library card",
        "password": "PIN"
      }
    }
  ]
}
```

In addition, it's also recommended to include a link to this bookshelf in all OPDS feeds returned by a catalog:

```json
{
  "rel": "http://opds-spec.org/shelf", 
  "href": "https://example.com/user/loans", 
  "type": "application/opds+json",
  "properties": {
    "authenticate": "https://example.com/authentication"
  }
 }
```

If a bookshelf can grow to contain more than a few publications at a time, it's recommended to support both [facets](./opds-2.0.md#24-facets) and [search](./opds-2.0.md#3-search) (scoped to the bookshelf rather than the entire catalog).


## 3. Profile

Catalogs that offer the ability to borrow publications may also need to enforce limitations to the number of loans (and holds) allowed at any given time.

To convey this information to clients, catalogs should implement a [User Profile](./opds-user-profile-1.0.md) that can be discovered through the Authentication Document:

```json
{
  "id": "https://example.com/authentication",
  "title": "Public Library",
  "description": "Enter a valid library card number and PIN code to authenticate on our service.",
  "links": [
    {
      "rel": "http://opds-spec.org/shelf", 
      "href": "https://example.com/user/loans", 
      "type": "application/opds+json"
    },
    {
      "rel": "profile", 
      "href": "https://example.com/user/profile", 
      "type": "application/opds-profile+json"
    }
  ],
  "authentication": [
    {
      "type": "http://opds-spec.org/auth/basic",
      "labels": {
        "login": "Library card",
        "password": "PIN"
      }
    }
  ]
}
```

All fields are optional in this User Profile, but it's recommended to include at least a `name` and a link where the user can edit their profile:

```json
{
  "name": "John Smith",
  "links": [
    {
      "rel": "edit",
      "href": "https://example.com/user/edit",
      "type": "text/html"
    }
  ]
 }
```

If the catalog enforces a maximum number of loans at any given time, it must also include a `loans` object that indicates the total and remaining number of loans:

```
{
  "name": "John Smith",
  "links": [
    {
      "rel": "edit",
      "href": "https://example.com/user/edit",
      "type": "text/html"
    }
  ],
  "loans": {
    "total": 10,
    "available": 5
  }
}
```

## 4. Holds

TODO

## Appendix A - Catalog examples

* [🇧🇪 Lirtuel](https://www.lirtuel.be/v1/home.opds2)
