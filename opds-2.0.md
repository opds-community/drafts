# OPDS Catalog 2.0

Open Publication Distribution System (OPDS) 2.0 is an extension of the [Readium Web Publication Manifest](https://github.com/readium/webpub-manifest), 
with a focus on aggregating publications together in order to facilitate their distribution.

## Status of this Document

This document is a draft of the 2.0 version of the OPDS Catalog specification.

## Collection Roles

OPDS 2.0 introduces five new collection roles to the Readium Web Publication Manifest model.

While none of them are a prerequisite, an OPDS 2.0 feed must contain at least:

- one `navigation` collection
- and/or one `publications` collection
- and/or one `groups` collection

While previous versions of the OPDS specification were limited to the single collection model of Atom, OPDS 2.0 directly embraces the ability for an OPDS feed to contain as many collections as required.

| Role  | Semantics | Compact? | Required? | Reference |
| ----- | --------- | -------- | --------- | --------- |
| navigation  | An ordered list of links meant to browse a catalog in depth.  | Yes  | No  | [OPDS 2.0](opds-2.0.md) |
| publications  | Contains a list of publications.  | No  | No  | [OPDS 2.0](opds-2.0.md) |
| images  | A list of resources that can be displayed as the image representation of a publication.  | Yes  | No  | [OPDS 2.0](opds-2.0.md) |
| facets  | Links meant to obtain a sub-set of the current list of publications, or the same list in a different order.  | No  | No  | [OPDS 2.0](opds-2.0.md) |
| groups  | Structural element in a catalog meant to group publications together.  | No  | No  | [OPDS 2.0](opds-2.0.md) |

### The `navigation` role

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

### The `publications` role

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

### The `facets` role

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

In `metadata` a feed can contain:
- `numberOfItems` to indicate the total number of items available
- `itemsPerPage` to indicate the number of items on a given page
- `currentPage` to indicate the current page

In `links` the following relations can be used:
- `next` to indicate the next page
- `previous` to indicate the previous page
- `first` to indicate the first page
- `last` to indicate the last page

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
