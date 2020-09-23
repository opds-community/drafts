# OPDS Catalog 2.0

Open Publication Distribution System (OPDS) 2.0 is based on the [Readium Web Publication Manifest](https://github.com/readium/webpub-manifest) model and JSON-LD, 
with a focus on aggregating publications together in order to facilitate their distribution.

**This version:**

* [https://drafts.opds.io/opds-2.0](https://drafts.opds.io/opds-2.0)

**Previous version:**

* [https://specs.opds.io/opds-1.2](https://specs.opds.io/opds-1.2)

**Editors:**

* Hadrien Gardeur ([De Marque](http://www.demarque.com))

**Participate:**

* [GitHub opds-community/drafts](https://github.com/opds-community/drafts)
* [File an issue](https://github.com/opds-community/drafts/issues)


## Status of this Document

This document is a draft of the 2.0 version of the OPDS Catalog specification.

## Table of Contents

* [Overview](#overview)
* [1. Collections](#1-collections)
  * [1.1. Navigation](#11-navigation)
  * [1.2. Publications](#12-publications)
  * [1.3. Images](#13-images)
  * [1.4. Facets](#14-facets)
  * [1.5. Groups](#15-groups)
* [2. Search](#2-search)
* [3. Pagination](#3-pagination)
* [4. Publications](#4-publications)
  * [4.1. OPDS Publication](#41-opds-publication)
  * [4.2. Metadata](#42-metadata)
  * [4.3. Acquisition Links](#43-acquisition-links)
* [Appendix A. JSON Schema](#appendix-a-json-schema)

## Overview

OPDS 2.0 is based on the same abstract model as the [Readium Web Publication Manifest](https://github.com/readium/webpub-manifest).

Compared to previous versions of the OPDS specification:

- the model allows multiple collections to be contained in a single feed
- the Link Object model is more powerful than the `link` element in Atom, it supports URI templates and multiple relations
- metadata are expressed in JSON but an RDF graph can be extracted using a JSON-LD context
- the core metadata vocabulary is tied to schema.org instead of Dublin Core

In the Readium Web Publication Manifest model, everything has to be a collection:

- a collection is identified by its role
- a collection contains both `metadata` and `links`, along with sub-collections
- specific collection roles that only contain `links` are called compact collections and use a compact syntax (array of Link Objects)

An OPDS 2.0 Catalog Feed is a collection too, with the following requirements:

- it <em class="rfc">must</em> contain at least one collection identified by the following roles: `navigation`, `publications` or `groups`
- it <em class="rfc">must</em> contain a `title` in its `metadata`
- it <em class="rfc">must</em> contain a reference to itself using a `self` link in `links`

OPDS 2.0 feeds are identified using the following media type: `application/opds+json`

## 1. Collections

OPDS 2.0 introduces five new collection roles to the Readium Web Publication Manifest model.


| Role  | Semantics | Compact? | Required? | Reference |
| ----- | --------- | -------- | --------- | --------- |
| [navigation](#11-navigation)  | An ordered list of links meant to browse a catalog in depth.  | Yes  | No  | [OPDS 2.0](opds-2.0.md) |
| [publications](#12-publications)  | Contains a list of publications.  | No  | No  | [OPDS 2.0](opds-2.0.md) |
| [images](#13-images)  | Visual representations for a publication.  | Yes  | No  | [OPDS 2.0](opds-2.0.md) |
| [facets](#14-facets)   | Links meant to obtain a sub-set of the current list of publications, or the same list in a different order.  | No  | No  | [OPDS 2.0](opds-2.0.md) |
| [groups](#15-groups)   | Structural element in a catalog meant to contain `navigation` or `publications` collections.  | No  | No  | [OPDS 2.0](opds-2.0.md) |

### 1.1. Navigation

A `navigation` collection is meant to provide links that an end user can follow in order to browse a catalog. 

Each Link Object <em class="rfc">must</em> contain a `title`.

While a `navigation` link <em class="rfc">may</em> point to any number of format, it is <em class="rfc">recommended</em> to either link to other OPDS 2.0 feeds (`application/opds+json`) or to HTML (`text/html`).

Catalog providers <em class="rfc">should</em> also attempt to provide a meaningful link relation for each Link Object in a `navigation` collection.

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
      "rel": "current"
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

### 1.2. Publications

A `publications` collection is meant to list publications in an OPDS feed.

Publications <em class="rfc">must</em> either be:

- a [Readium Web Publication](https://github.com/readium/webpub-manifest) with no restrictions in terms of access (no payment, no credentials required, no limitations whatsoever)
- or an [OPDS Publication](#41-opds-publication)   

Each publication listed in an OPDS feed <em class="rfc">must</em> contain an `images` collection.

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
        {"rel": "self", "href": "http://example.org/publication.json", "type": "application/opds-publication+json"},
        {"rel": "http://opds-spec.org/acquisition/open-access", "href": "http://example.org/file.epub", "type": "application/epub+zip"}
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

### 1.3. Images

While previous versions of OPDS relied on link relations to identify visual representations, OPDS 2.0 introduces a dedicated collection role for that purpose.

This new collection role is mostly meant to support responsive images across all types of devices.

Link Objects in `images` <em class="rfc">may</em> include any number of image format, resolution or aspect ratio.

At least one image resource <em class="rfc">must</em> use one of the following formats: `image/jpeg`, `image/png` or `image/gif`.

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

### 1.4. Facets

Facets are meant to allow a user to explore a large collection of publications by either changing the order or obtaining a sub-set for the current feed.

The `facets` role is meant to indicate that a collection contains a facet group. Each facet group:

* <em class="rfc">must</em> contain a `title` in its `metadata`
* <em class="rfc">should</em> contain at least two or more Link Objects in `links`
* <em class="rfc">may</em> contain `numberOfItems` in the `properties` of a Link Object to hint at the number of publications available for a given facet
* <em class="rfc">may</em> use `self` in the `rel` of a Link Object to indicate which facet is currently active


**Example**

```json
{
  "facets": [
    {
      "metadata": {
        "title": "Language"
      },
      "links": [
        {
          "href": "/fr", 
          "type": "application/opds+json", 
          "title": "French", 
          "properties": { "numberOfItems": 10 }
        },
        {
          "href": "/en", 
          "type": "application/opds+json", 
          "title": "English", 
          "properties": { "numberOfItems": 40 }
        },
        {
          "href": "/de", 
          "type": "application/opds+json", 
          "title": "German", 
          "properties": { "numberOfItems": 6 }
        }
      ]
    }
  ]
}
```

### 1.5. Groups

When an OPDS catalog feed needs to contain more than one `navigation` or `publications` collection, it needs to rely on the `groups` role.

Groups are meant to contain:

- either a single `navigation` collection
- or a single `publications` collection

Each group <em class="rfc">must</em> provide a `title` in its metadata.

In addition, each group <em class="rfc">may</em> also provide:

- a `self` link where the client can access the full collection
- `numberOfItems` in its metadata to indicate the total number of publications available for that collection

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
            {"rel": "self", "href": "http://example.org/publication.json", "type": "application/opds-publication+json"},
            {"rel": "http://opds-spec.org/acquisition/open-access", "href": "http://example.org/file.epub", "type": "application/epub+zip"}
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

## 2. Search

Each catalog <em class="rfc">may</em> provide the ability to search.

A catalog can indicate that search is available by providing a Link Object in `links` where the relation is set to "search":

```json
{
  "rel": "search",
  "href": "search{?query}", 
  "type": "application/opds+json", 
  "templated": true
}
```

Search is always expressed using a URI template with at least one query parameter: "query". This query parameter enables basic keyword search.

In addition to basic keyword search, an OPDS catalog <em class="rfc">may</em> allow advanced search on any metadata element defined in the [Default Context Document](https://github.com/readium/webpub-manifest/tree/master/contexts/default).

For example the following Link Object allows keyword search as well as restricting search on the "title" or "author" of a publication:

```json
{
  "rel": "search",
  "href": "search{?query,title,author}", 
  "type": "application/opds+json", 
  "templated": true
}
```

## 3. Pagination

An OPDS Catalog can contain a very large number of publications. While such catalogs are usually broken down using navigation, facets and groups, a single sub-collection could still contain thousands of publications.

To handle that issue, OPDS 2.0 supports pagination based on `metadata` and `links`.

In `metadata` a feed <em class="rfc">may</em> contain the following elements:

| Key  | Definition | Format |
| ---- | ---------- | ------ | 
| `numberOfItems`  | Indicates the total number of items in a collection.  | Integer |
| `itemsPerPage`  | Indicates the number of items displayed per page for the current collection.  | Integer |
| `currentPage`  | Indicates the current page number.  | Integer |


In `links` the following relations <em class="rfc">may</em> be used:

| Relation  | Definition | Reference |
| ------------- | ---------- | --------- | 
| `next`  | Refers to the next resource in a ordered series of resources.  | [HTML4](https://www.w3.org/TR/html4/types.html#type-links) |
| `previous`  | Refers to the previous resource in an ordered series of resources. Synonym for "prev".  | [HTML4](https://www.w3.org/TR/html4/types.html#type-links) |
| `first`  | An IRI that refers to the furthest preceding resource in a series of resources.  | [RFC5988](https://tools.ietf.org/html/rfc5988) |
| `last`  | An IRI that refers to the furthest following resource in a series of resources. | [RFC5988](https://tools.ietf.org/html/rfc5988) |


**Example: Basic pagination**

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

## 4. Publications

### 4.1. OPDS Publication

An OPDS Publication is essentially a Readium Web Publication without the requirement to include a `readingOrder` collection:

* it <em class="rfc">must</em> be identified by the following media type: `application/opds-publication+json`
* it <em class="rfc">must</em> contain at least one [Acquisition Link](#43-acquisition-link)
* it <em class="rfc">should</em> contain a `self` link

### 4.2. Metadata

Metadata expression in OPDS 2.0 is based on the [Readium Web Publication Manifest](https://github.com/readium/webpub-manifest) model and [its default context](https://github.com/readium/webpub-manifest/tree/master/contexts/default).

OPDS 2.0 introduces a slight extension to this model by allowing <em class="rfc">optional</em> links at a metadata level in the following elements: 

* all contributor elements: `author`, `translator`, `editor`, `artist`, `illustrator`, `letterer`, `penciler`, `colorist`, `inker` and `narrator`
* `publisher`
* `series`
* `collection`

The `links` element is used for that purpose with an array of Link Objects.

**Example: Publication metadata enhanced with links**

```json
"metadata": {
  "@type": "http://schema.org/EBook",
  "identifier": "urn:isbn:9780000000002",
  "title": "A Journey into the Center of the Earth",
  "author": {
    "name": "Jules Verne",
    "identifier": "http://isni.org/isni/0000000121400562",
    "sortAs": "Verne, Jules",
    "links": [
      {"href": "/author/0000000121400562", "type": "application/opds+json"}
    ]
  },
  "translator": "Frederick Amadeus Malleson",
  "language": "en",
  "publisher": "SciFi Publishing Inc.",
  "modified": "2016-02-22T11:31:38Z",
  "description": "The story involves German professor Otto Lidenbrock who believes there are volcanic tubes going toward the centre of the Earth. He, his nephew Axel, and their guide Hans descend into the Icelandic volcano Snæfellsjökull, encountering many adventures, including prehistoric animals and natural hazards, before eventually coming to the surface again in southern Italy, at the Stromboli volcano.",
  "belongsTo": {
    "series": {
      "name": "The Extraordinary Voyages",
      "position": 3,
      "links": [
        {"href": "/series/167", "type": "application/opds+json"}
      ]
    },
    "collection": "SciFi Classics"
  }
}
```

### 4.3. Acquisition Links

In OPDS 2.0, the concept of an Acquision Link is not as central as in OPDS 1.x since publications can also be accessed through a manifest.

That said, for publications that are strictly accessible through a download or require specific interactions, the concept remains.

OPDS 2.0 allows the following relations to indicate that a publication can be acquired:

| Relation  | Definition | Reference |
| ------------- | ---------- | --------- | 
| `http://opds-spec.org/acquisition`  | Fallback acquisition relation when no other relation is a good fit to express the nature of the transaction.  | [OPDS 1.2](https://github.com/opds-community/opds-revision/blob/master/opds-1.2.md) |
| `http://opds-spec.org/acquisition/open-access`  | Indicates that a publication is freely accessible without any requirement, including authentication.  | [OPDS 1.2](https://github.com/opds-community/opds-revision/blob/master/opds-1.2.md) |
| `http://opds-spec.org/acquisition/borrow`  | Indicates that a publication can be borrowed for a limited period of time.  | [OPDS 1.2](https://github.com/opds-community/opds-revision/blob/master/opds-1.2.md) |
| `http://opds-spec.org/acquisition/buy`  | Indicates that a publication can be purchased for a given price. | [OPDS 1.2](https://github.com/opds-community/opds-revision/blob/master/opds-1.2.md) |
| `http://opds-spec.org/acquisition/sample`  | Indicates that a sub-set of the full publication is freely accessible at a given URI, without any prior requirement. | [OPDS 1.2](https://github.com/opds-community/opds-revision/blob/master/opds-1.2.md) |
| `preview`  | Indicates that a sub-set of the full publication is freely accessible at a given URI, without any prior requirement. | [RFC6903](https://tools.ietf.org/html/rfc6903#section-3) |
| `http://opds-spec.org/acquisition/subscribe`  | Indicates that a publication be subscribed to, usually as part of a purchase and for a limited period of time. | [OPDS 1.2](https://github.com/opds-community/opds-revision/blob/master/opds-1.2.md) |

An OPDS 2.0 catalog <em class="rfc">should not</em> use `http://opds-spec.org/acquisition` when another link relation is suitable to express the interaction.

In addition to link relations, OPDS 2.0 also defines a number of properties to express relevant information for these interactions:

| Key  | Definition | Format |
| ---- | ---------- | ------ | 
| `price`  | Provides the acquisition price in a given currency.  | Price Object |
| `indirectAcquisition`  | Provides the expected download format for a publication, after an acquisition through an intermediate resource.  | Array of Acquisition Objects |

The Price Object can contain the following keys:

| Key  | Definition | Format |
| ---- | ---------- | ------ | 
| `currency`  | Provides the currency for a specific price.  | ISO 4217 currency code |
| `value`  | Provides the decimal value for a specific price.  | Float |

The Acquisition Object can contain the following keys:

| Key  | Definition | Format |
| ---- | ---------- | ------ | 
| `type`  | Indicates the media type in which the publication can be indirectly acquired.  | MIME Media Type|
| `child` | Contains an additional level of indirection if necessary.  | Array of Acquisition Objects |

**Example 1: Simple paid acquisition**

```json
{
  "href": "/buy",
  "rel": "http://opds-spec.org/acquisition/buy",
  "type": "application/epub+zip",
  "properties": {
    "price": {
      "currency": "USD",
      "value": 4.99
    }
  }
}
```

**Example 2: Indirect Acquisition through an HTML document**

```json
{
  "href": "/buy",
  "rel": "http://opds-spec.org/acquisition/buy",
  "type": "text/html",
  "properties": {
    "price": {
      "currency": "USD",
      "value": 4.99
    },
    "indirectAcquisition": [{"type": "application/epub+zip"}]
  }
}
```

## Appendix A. JSON Schema

A JSON Schema for OPDS 2.0 is available under version control at [https://github.com/opds-community/drafts/tree/master/schema](https://github.com/opds-community/drafts/tree/master/schema)

For the purpose of validating an OPDS 2.0 catalog, use the following JSON Schema resources:

* OPDS 2.0 Feed: [https://drafts.opds.io/schema/feed.schema.json](https://drafts.opds.io/schema/feed.schema.json)
* OPDS 2.0 Publication: [https://drafts.opds.io/schema/publication.schema.json](https://drafts.opds.io/schema/publication.schema.json)
