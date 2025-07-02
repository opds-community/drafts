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
    "indirectAcquisition": [
      {
        "type": "application/epub+zip"
      }
    ],
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
    "indirectAcquisition": [
      {
        "type": "application/pdf"
      }
    ],
    "lcp_hashed_passphrase": "+usAylGL6nyxGn7zH7YYO0ibG26tt5K+xkoDs/b/gKg="
  }
}
```

Borrow links also usually require authentication from the client using [Authentication for OPDS 1.0](authentication-for-opds-1.0). 

In order to optimize this interaction, this document recommends using an `authenticate` property to advise the client as well:

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

## 2. Bookshelf and Profile

TODO

## 3. Holds

TODO
