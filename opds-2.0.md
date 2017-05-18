# OPDS 2.0

Open Publication Distribution System (OPDS) 2.0 is an extension of the [Readium Web Publication Manifest](https://github.com/readium/webpub-manifest), 
with a focus on aggregating publications together in order to facilitate their distribution.

## Status of this Document

This document is a draft of the 2.0 version of the OPDS Catalog specification.

## Collection Roles

| Role  | Semantics | Compact? | Required? | Reference |
| ----- | --------- | -------- | --------- | --------- |
| navigation  | An ordered list of links meant to browse a catalog in depth.  | Yes  | No  | [OPDS 2.0](opds-2.0.md) |
| publication  | Contains a list of publications.  | No  | No  | [OPDS 2.0](opds-2.0.md) |
| images  | A list of resources that can be displayed as the image representation of a publication.  | Yes  | No  | [OPDS 2.0](opds-2.0.md) |
| facets  | Links meant to obtain a sub-set of the current list of publications, or the same list in a different order.  | No  | No  | [OPDS 2.0](opds-2.0.md) |
| groups  | Structural element in a catalog meant to group publications together.  | No  | No  | [OPDS 2.0](opds-2.0.md) |

### The `navigation` role


```
{
  "metadata": {
    "title": "Example for navigation"
  },
  
  "links": [
    {"rel": "self", "href": "http://example.com/opds", "type": "application/opds+json"}
  ],
  
  "navigation": [
    {"href": "/new", "title": "New Publications", "type": "application/opds+json"},
    {"href": "/popular", "title": "Popular Publications", "type": "application/opds+json"}
  ]
}
```

### The `publications` role

```
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

```
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

```
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


```
{
  "rel": "search",
  "href": "search{?query,title,author}", 
  "type": "application/opds+json", 
  "templated": true
}
```
