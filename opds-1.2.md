# OPDS Catalog 1.2

## Status of this Document

This document is a draft of the 1.2 version of the OPDS Catalog specification. 

## Abstract
The Open Publication Distribution System (OPDS) Catalog format is a syndication format for electronic 
publications based on Atom and HTTP. OPDS Catalogs enable the aggregation, distribution, discovery, 
and acquisition of electronic publications. 
OPDS Catalogs use existing or emergent open standards and conventions, with a priority on simplicity.

## 1. Introduction

The Open Publication Distribution System (OPDS) Catalog format is a syndication format for electronic publications 
based on Atom [RFC4287] and HTTP [RFC2616]. OPDS Catalogs enable available electronic publications to be:

* discovered, using optional search or a range of optional browsing techniques
* acquired, using direct downloads, lending, or vending

An OPDS Catalog is a set of one or more Atom Feeds, which are themselves listings of Atom Entries. 
The Atom Feeds that make up all OPDS Catalogs can be divided into two groups: 
Navigation Feeds, which create a hierarchy for browsing, and 
Acquisition Feeds, which list available electronic publications. 
Each Atom Entry in an Acquisition Feed includes basic metadata about the publication, the publication's format, 
and how the publication can be acquired. 
These included Atom Entries may be minimal subsets of the complete metadata, with a link to a more extensive, 
standalone representation URI (see the Section Listing Acquisition Feeds for more).

OPDS Catalogs may be aggregated and combined into larger OPDS Catalogs.


## 2. Media Type Considerations


### 2.1. The Atom Format Type Parameter
The Atom Publishing Protocol [RFC5023] defines the Atom Format Type Parameter.
Publishers of OPDS Catalogs SHOULD use the type parameter to help clients distinguish between relations to 
OPDS Catalog Entries and OPDS Catalog Feeds.

### 2.2. The OPDS Catalog Profile Parameter
Relations to OPDS Catalog Feed Document and OPDS Catalog Entry Document Resources SHOULD use a "profile" parameter 
following Section 4.3 of [RFC4288] with the value "opds-catalog". 
This profile parameter provides clients with an advisory hint that the Resource should be a component of an OPDS Catalog.

The complete media type for a relation to an OPDS Catalog Entry Document Resource SHOULD be:

`application/atom+xml;type=entry;profile=opds-catalog`

### 2.3. The OPDS Kind Parameter
In addition to the new "profile" parameter, this specification also introduces a new "kind" parameter following Section 4.3 
of [RFC4288] with the value "acquisition" or "navigation". 
This "kind" parameter provides clients with an advisory hint whether the Resource should be an Acquisition Feed or a 
Navigation Feed.

This value is intended to make it easier for OPDS clients to display basic contextual information about the feed without 
requiring that those clients dereference, parse, and analyze linked resources. That said, the client MUST NOT 
assume this parameter to provide completely accurate information about the nature of the feed. 
Feed publishers SHOULD make an effort to ensure that this value is accurate.

The complete media type for a relation to an Acquisition Feed SHOULD be:

`application/atom+xml;profile=opds-catalog;kind=acquisition`

The complete media type for a relation to a Navigation Feed SHOULD be:

`application/atom+xml;profile=opds-catalog;kind=navigation`
