# OPDS Catalog 2.0

Open Publication Distribution System (OPDS) 2.0 is based on [Readium Web Publication Manifest](https://github.com/readium/webpub-manifest) model, 
with a focus on aggregating publications together in order to facilitate their distribution.

## Status of this Document

This document is a draft of the 2.0 version of the OPDS Catalog specification.

## Abstract Model

OPDS 2.0 is based on the same abstract model as the [Readium Web Publication Manifest](https://github.com/readium/webpub-manifest).

Compared to previous versions of the OPDS specification:

- the model allows multiple collections to be contained in a single feed
- the Link Object model is more powerful than the `link` element in Atom, it supports URI templates and multiple relationships
- metadata are expressed in JSON but an RDF graph can be extracted using a JSON-LD context
- the core metadata vocabulary is tied to schema.org instead of Dublin Core

In the Readium Web Publication Manifest model, everything has to be a collection:

- a collection is identified by its role
- a collection contains both `metadata` and `links`
- specific collection roles that only contain `links` are called compact collections and use a compact syntax (array of Link Objects)

An OPDS 2.0 Catalog Feed is a collection too, with the following requirement:

- it must contain at least one collection identified by the following roles: `navigation`, `publications` or `groups`
- it must contain a `title` in its `metadata`
- it must contain a reference to itself using a `self` link in `links`

OPDS 2.0 feeds are identified using the following media type: `application/opds+json`

## Collection Roles

OPDS 2.0 introduces five new collection roles to the Readium Web Publication Manifest model.


| Role  | Semantics | Compact? | Required? | Reference |
| ----- | --------- | -------- | --------- | --------- |
| [navigation](#navigation)  | An ordered list of links meant to browse a catalog in depth.  | Yes  | No  | [OPDS 2.0](opds-2.0.md) |
| [publications](#publications)  | Contains a list of publications.  | No  | No  | [OPDS 2.0](opds-2.0.md) |
| [images](#the-images-role)  | Visual representations for a publication.  | Yes  | No  | [OPDS 2.0](opds-2.0.md) |
| [facets](#facets)   | Links meant to obtain a sub-set of the current list of publications, or the same list in a different order.  | No  | No  | [OPDS 2.0](opds-2.0.md) |
| [groups](#groups)   | Structural element in a catalog meant to contain `navigation` or `publications` collections.  | No  | No  | [OPDS 2.0](opds-2.0.md) |

## Navigation

A `navigation` collection is meant to provide links that an end user can follow in order to browse a catalog. 

Each Link Object must contain a `title`.

While a `navigation` link can point to any number of format, it is recommended to either link to other OPDS 2.0 feeds (`application/opds+json`) or to HTML (`text/html`).

Catalog providers should also attempt to provide a meaningful link relation for each Link Object in a `navigation` collection.

**Example**

```json
{
  "metadata": {
    "title": "Example for navigation"
  },
  
  "links": [
    {"rel": "self", "href": "http://example.com/opds", "type": "application/opds+json"}
  ],
  
  "navigation": [
    {
      "href": "/new", 
      "title": "New Publications", 
      "type": "application/opds+json", 
      "rel": "http://opds-spec.org/sort/new"
    },
    {
      "href": "/popular", 
      "title": "Popular Publications", 
      "type": "application/opds+json", 
      "rel": "http://opds-spec.org/sort/popular"
    }
  ]
}
```

## Publications

A `publications` collection is meant to list publications in an OPDS feed.

Publications can either be:

- Web Publications with no restrictions in terms of access (no payment, no credentials required, no limitations whatsoever)
- any other publication format, as long as the publication has at least one acquisition link

All publications listed in such a collection must provide a `self` link that will be used by OPDS clients to identify, reference and access publications.


**Example**

```json
{
  "metadata": {
    "title": "Example listing publications"
  },
  
  "links": [
    {"rel": "self", "href": "http://example.com/new", "type": "application/opds+json"}
  ],
  
  "publications": [
    {
      "metadata": {
        "@type": "http://schema.org/Book",
        "title": "Moby-Dick",
        "author": "Herman Melville",
        "identifier": "urn:isbn:978031600000X",
        "language": "en",
        "modified": "2015-09-29T17:00:00Z"
      },
      "links": [
        {"rel": "self", "href": "http://example.org/manifest.json", "type": "application/webpub+json"}
      ],
      "images": [
        {"href": "http://example.org/cover.jpg", "type": "image/jpeg", "height": 1400, "width": 800},
        {"href": "http://example.org/cover-small.jpg", "type": "image/jpeg", "height": 700, "width": 400},
        {"href": "http://example.org/cover.svg", "type": "image/svg+xml"}
      ]
    }
  ]
}
```

### The `images` role

While previous versions of OPDS relied on link relations to identify visual representations, OPDS 2.0 introduces a dedicated collection role for that purpose.

This new collection role is mostly meant to support responsive images across all types of devices.

Link Objects in `images` can include any number of image format, resolution or aspect ratio.

The only requirement is to include at least one image resource in the following formats: `image/jpeg`, `image/png` or `image/gif`.

Each publication listed in an OPDS feed must contain an `images` collection.

**Example**

```json
"images": [
  {
    "href": "http://example.org/cover.jpg", 
    "type": "image/jpeg", 
    "height": 1400, 
    "width": 800
  },
  {
    "href": "http://example.org/cover-small.jpg", 
    "type": "image/jpeg", 
    "height": 700, 
    "width": 400
  },
  {
    "href": "http://example.org/cover.svg", 
    "type": "image/svg+xml"
  }
]
```

## Facets

Facets are meant to allow a user to explore a large collection of publications by either changing the order or obtaining a sub-set.

```json
{
  "metadata": {
    "title": "Example for facets"
  },
  
  "links": [
    {"rel": "self", "href": "http://example.com/faceted", "type": "application/opds+json"}
  ],
  
  "facets": [
    {
      "metadata": {
        "title": "Language", 
        "numberOfItems": 56
      },
      "links": [
        {"href": "/fr", "type": "application/opds+json", "title": "French", "properties": {"numberOfItems": 10}},
        {"href": "/en", "type": "application/opds+json", "title": "English", "properties": {"numberOfItems": 40}},
        {"href": "/de", "type": "application/opds+json", "title": "German", "properties": {"numberOfItems": 6}}
      ]
    }
  ]
}
```

## Groups

When an OPDS catalog feed needs to contain more than one `navigation` or `publications` collection, it needs to rely on the `groups` role.

Groups are meant to contain:

- either a single `navigation` collection
- or a single `publications` collection

Each group must provide a `title` in its metadata.

**Example 1: Repeating navigation using groups**

```json
{
  "metadata": {
    "title": "Example for groups"
  },
  
  "links": [
    {"rel": "self", "href": "http://example.com/grouped", "type": "application/opds+json"}
  ],
  
  "groups": [
    {
      "metadata": {"title": "Main Menu"},
      "navigation": [
        {"href": "/new", "title": "New Publications", "type": "application/opds+json", "rel": "http://opds-spec.org/sort/new"},
        {"href": "/popular", "title": "Popular Publications", "type": "application/opds+json", "rel": "http://opds-spec.org/sort/popular"}
      ]
    },
    {
      "metadata": {"title": "Formats"},
      "navigation": [
        {"href": "/ebooks", "title": "EBooks", "type": "application/opds+json", "rel": "subsection"},
        {"href": "/audiobooks", "title": "Audiobooks", "type": "application/opds+json", "rel": "subsection"}
      ]
    }
  ]
}
```

In addition, groups may also provide:

- a `self` link where the client can access the full collection
- `numberOfItems` in its metadata to indicate the total number of publications available for that collection

A group may for example contain a `publications` collection with 5 publications embedded, but indicate in `numberOfItems` that there are 50 publications in total and provide a link to that full collection.

**Example 2: Mixing navigation & publications**

```json
{
  "metadata": {
    "title": "Example for groups"
  },
  
  "links": [
    {"rel": "self", "href": "http://example.com/grouped", "type": "application/opds+json"}
  ],
  
  "groups": [
    {
      "metadata": {"title": "Main Menu"},
      "navigation": [
        {"href": "/new", "title": "New Publications", "type": "application/opds+json", "rel": "http://opds-spec.org/sort/new"},
        {"href": "/popular", "title": "Popular Publications", "type": "application/opds+json", "rel": "http://opds-spec.org/sort/popular"}
      ]
    },
    {
      "metadata": {
        "title": "Featured Books",
        "numberOfItems": 20
      },
      "links": [
        {"rel": "self", "href": "/featured", "type": "application/opds+json"}
      ],
      "publications": [
        {
          "metadata": {
            "@type": "http://schema.org/Book",
            "title": "Moby-Dick",
            "author": "Herman Melville",
            "identifier": "urn:isbn:978031600000X",
            "language": "en",
            "modified": "2015-09-29T17:00:00Z"
          },
          "links": [
            {"rel": "self", "href": "http://example.org/manifest.json", "type": "application/webpub+json"}
          ],
          "images": [
            {"href": "http://example.org/cover.jpg", "type": "image/jpeg", "height": 1400, "width": 800},
            {"href": "http://example.org/cover-small.jpg", "type": "image/jpeg", "height": 700, "width": 400},
            {"href": "http://example.org/cover.svg", "type": "image/svg+xml"}
          ]
        }
      ]
    }
  ]
}
```


## Search

A catalog can indicate that search is available by providing a Link Object in `links` where the relationship is set to "search":

```json
{
  "rel": "search",
  "href": "search{?query}", 
  "type": "application/opds+json", 
  "templated": true
}
```

Search is always expressed using a URI template with at least one query parameter: "query". This query parameter enables basic keyword search.

In addition to basic keyword search, an OPDS catalog can allow advanced search on any metadata element defined in the [Default Context Document](https://github.com/readium/webpub-manifest/tree/master/contexts/default).

For example the following Link Object allows keyword search as well as restricting search on the "title" or "author" of a publication:


```json
{
  "rel": "search",
  "href": "search{?query,title,author}", 
  "type": "application/opds+json", 
  "templated": true
}
```

## Pagination

An OPDS Catalog can contain a very large number of publications. While such catalogs are usually broken down using navigation, facets and groups, a single sub-collection could still contain thousands of publications.

To handle that issue, OPDS 2.0 supports pagination based on `metadata` and `links`.

In `metadata` a feed can contain the following elements:

| Key  | Value | Format |
| ----- | ----- | ------ | 
| numberOfItems  | Indicates the total number of items in a collection.  | Integer |
| itemsPerPage  | Indicates the number of items displayed per page for the current collection.  | Integer |
| currentPage  | Indicates the current page number.  | Integer |


In `links` the following relations can be used:

| Relationship  | Definition | Reference |
| ------------- | ---------- | --------- | 
| next  | Refers to the next resource in a ordered series of resources.  | [HTML4](https://www.w3.org/TR/html4/types.html#type-links) |
| previous  | Refers to the previous resource in an ordered series of resources. Synonym for "prev".  | [HTML4](https://www.w3.org/TR/html4/types.html#type-links) |
| first  | An IRI that refers to the furthest preceding resource in a series of resources.  | [RFC5988](https://tools.ietf.org/html/rfc5988) |
| last  | An IRI that refers to the furthest following resource in a series of resources. | [RFC5988](https://tools.ietf.org/html/rfc5988) |


**Example**

```json
{
  "metadata": {
    "title": "Paginated feed",
    "numberOfItems": 5678,
    "itemsPerPage": 50,
    "currentPage": 2
  },
  "links": [
    {"rel": "self", "href": "/?page=2", "type": "application/opds+json"},
    {"rel": ["first", "previous"], "href": "/?page=1", "type": "application/opds+json"},
    {"rel": "next", "href": "/?page=3", "type": "application/opds+json"},
    {"rel": "last", "href": "/?page=114", "type": "application/opds+json"}
  ]
}
```
