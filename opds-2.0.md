# OPDS 2.0

Open Publication Distribution System (OPDS) 2.0 is an extension of the [Readium Web Publication Manifest](https://github.com/readium/webpub-manifest), 
with a focus on aggregating publications together in order to facilitate their distribution.

## Status of this Document

This document is a draft of the 2.0 version of the OPDS Catalog specification.

## Collection Roles

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
      ]
    }
  ]
}
```
