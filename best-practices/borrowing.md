# Best Practices: Borrowing Publications with OPDS 2.0

With wide adoption from public libraries in multiple countries, borrowing has become one of the most common use-cases for OPDS 2.0.

This document covers best practices that catalogs and clients should be aware of when implementing this use case.

## 1. Borrow Links

OPDS 2.0 requires publications to include at least one [Acquisition Link](../opds-2.0.md#53-acquisition-links). 

For publications that can be borrowed, this means using `http://opds-spec.org/acquisition/borrow` as the `rel` value of a Link Object.

While the specification is neutral about what acquisition links should return, this document recommends returning an OPDS publication using `application/opds-publication+json` in `type`.

Returning an OPDS publication provides several benefits over returning the publication itself:

- it allows catalogs to provide multiple formats and/or DRM for the publication
- if the publication is protected by Readium LCP, it also allows [the auto-discovery of the user's passphrase](https://readium.org/lcp-specs/notes/lcp-key-retrieval.html#including-a-hashed-passphrase-in-an-opds-publication)
- and can include additional information, such as the expiration date for a loan

In order to properly set expectations for clients, borrow links must also include which publication formats will be available using [`indirectAcquisition`](https://drafts.opds.io/opds-2.0#53-acquisition-links).

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
},
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

## 2. Authentication

Borrow links usually require authentication from the client using [Authentication for OPDS 1.0](../authentication-for-opds-1.0.md). 

In order to optimize this interaction, catalogs should use the `authenticate` property and identify the [Authentication Document](../authentication-for-opds-1.0.md#2-authentication-document) that will be returned by the server.

```json
{
  "href": "https://example.com/borrow",
  "rel": "http://opds-spec.org/acquisition/borrow",
  "type": "application/opds-publication+json",
  "properties": {
    "authenticate": "https://example.com/authentication",
    "indirectAcquisition": [
      {
        "type": "application/vnd.readium.lcp.license.v1.0+json",
        "child": [{"type": "application/epub+zip"}, {"type": "application/pdf"}]
      }
    ]
  }
}
```

Using this hint, catalogs can also introduce optional authentication throughout their catalog.

For example, a server could serve a list of custom recommendations for a logged in user, while everyone else would see a default list of publications.

## 3. Bookshelf

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

If a bookshelf can grow to contain more than a few publications at a time, it's recommended to support both [facets](../opds-2.0.md#24-facets) and [search](../opds-2.0.md#3-search) (scoped to the bookshelf rather than the entire catalog).


## 4. Profile

Catalogs that offer the ability to borrow publications may also need to enforce limitations to the number of loans (and holds) allowed at any given time.

To convey this information to clients, catalogs should implement a [User Profile](../opds-user-profile-1.0.md) that must be discoverable through the [Authentication Document](../authentication-for-opds-1.0.md#2-authentication-document) with a link where `rel` is set to `profile` and `type` set to `application/opds-profile+json`.

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

If the catalog enforces a maximum number of loans and/or holds, it should also include this information in the User Profile.

```json
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
  },
  "holds": {
    "total": 5,
    "available": 2
  }
}
```

## 5. Holds

In some cases, publications have limitations that can force a catalog to implement a hold queue.

When a publications is unavailable, catalogs must use the `availability` object in the Borrow Link to indicate this information where the `state` is set to `unavailable`.

Catalogs should also include additional context to the user, such as:

* an estimated availability data using `until`
* and the length of the hold queue using the `holds` object and the `total` property

```json
{
  "href": "https://example.com/borrow",
  "rel": "http://opds-spec.org/acquisition/borrow",
  "type": "application/opds-publication+json",
  "properties": {
    "availability": {
      "state": "unavailable",
      "until": "2025-12-01",
      "holds": {
        "total": 12
      }
    },
    "authenticate": "https://example.com/authentication",
    "indirectAcquisition": [
      {
        "type": "application/vnd.readium.lcp.license.v1.0+json",
        "child": [{"type": "application/epub+zip"}]
      }
    ]
  }
}
```

Placing a hold successfully must return an updated OPDS Publication where the Borrow Link has been updated with a new availability `state` set to `reserved`.

At that time, the hold should also be automatically added to the user's bookshelf, to allow them to track it.

Catalogs should also include the position of the patron in the hold queue using `position`.

If the catalog allows the patron to cancel a hold, it should include the `cancellable` property as well.

```json
{
  "href": "https://example.com/borrow",
  "rel": "http://opds-spec.org/acquisition/borrow",
  "type": "application/opds-publication+json",
  "properties": {
    "availability": {
      "state": "reserved",
      "until": "2025-12-01",
      "holds": {
        "total": 12
        "position": 12
      }
    },
    "cancellable": true,
    "authenticate": "https://example.com/authentication",
    "indirectAcquisition": [
      {
        "type": "application/vnd.readium.lcp.license.v1.0+json",
        "child": [{"type": "application/epub+zip"}]
      }
    ]
  }
}
```

Sending a `DELETE` request to the URL will cancel the hold and return an updated OPDS Publication where the `state` is no longer set to `reserved`.

When the user reaches the head of the hold queue and the publication becomes available to them, the catalog must replace the Borrow Link with a Download Link.

It is recommended to also include the date where the loan started using `since` and the date where the loan ends using `until`.


```json
{
  "href": "https://example.com/download/epub",
  "rel": "http://opds-spec.org/acquisition",
  "type": "application/vnd.readium.lcp.license.v1.0+json",
  "properties": {
    "availability": {
      "state": "available",
      "since": "2025-12-02T14:46:46.889Z",
      "until": "2025-12-23T14:46:46.889Z"
    },
    "indirectAcquisition": [{"type": "application/epub+zip"}],
    "lcp_hashed_passphrase": "+usAylGL6nyxGn7zH7YYO0ibG26tt5K+xkoDs/b/gKg="
  }
}
```

Some catalogs may require the user to confirm their loan when they reach the head of the hold queue. 

To do so, these catalogs may use an intermediary state using `ready` where the loan must be confirmed by the user. 

In this case, they must also include the date and time where the loan can no longer be confirmed and will be passed to the next user in the hold queue using `until`.

In this state, the user may still cancel their hold if they're no longer interested.

```json
{
  "href": "https://example.com/borrow",
  "rel": "http://opds-spec.org/acquisition/borrow",
  "type": "application/opds-publication+json",
  "properties": {
    "availability": {
      "state": "ready",
      "until": "2025-12-04T14:46:46.889Z"
    },
    "cancellable": true,
    "authenticate": "https://example.com/authentication",
    "indirectAcquisition": [
      {
        "type": "application/vnd.readium.lcp.license.v1.0+json",
        "child": [{"type": "application/epub+zip"}]
      }
    ]
  }
}
```

Whenever a hold becomes available for a user (whether it needs to be confirmed or automatically turned into a loan), catalogs should contact the user to warn them.

Clients may also fetch a user's bookshelf on a regular basis to warn the user of these changes to a publication's availability.


## Appendix A - Catalog Examples

* 🇧🇪 [Lirtuel](https://www.lirtuel.be/v1/home.opds2)

## Appendix B - Error Handling

TODO
