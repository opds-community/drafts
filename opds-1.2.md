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
- Navigation Feeds, which create a hierarchy for browsing, and 
- Acquisition Feeds, which list available electronic publications. 

Each Atom Entry in an Acquisition Feed includes basic metadata about the publication, the publication's format, 
and how the publication can be acquired. 

These included Atom Entries may be minimal subsets of the complete metadata, with a link to a more extensive, 
standalone representation URI (see the Section Listing Acquisition Feeds for more).

OPDS Catalogs may be aggregated and combined into larger OPDS Catalogs.

## 2. OPDS Catalog Feed Documents

OPDS Catalog Feed Documents are Atom [RFC4287] Feeds. They serve two purposes:

- To create a browsable hierarchy of other OPDS Catalog Feed Documents and other Resources. These OPDS Catalog Feed Documents are referred to as Navigation Feeds.
- To collect a set of OPDS Catalog Entries. These OPDS Catalog Feed Documents are referred to as Acquisition Feeds.

While Navigation Feeds do provide a suggested hierarchy from the OPDS Catalog publisher, OPDS Catalog Feed Documents MAY contain other relations that allow for other methods of consumption and display. See the Section OPDS Catalog Relations for more detail.

Every OPDS Catalog Feed Document MUST either be an Acquisition Feed or a Navigation Feed. An Acquisition Feed can be identified by the presence of Acquisition Links in each Atom Entry.

### 2.1. Navigation Feeds

A Navigation Feed is an OPDS Catalog Feed Document whose Atom Entries serve to create a suggested hierarchy for presentation and browsing. A Navigation Feed MUST NOT contain OPDS Catalog Entries but instead contains Atom Entries that link to other Navigation or Acquisition Feeds or other Resources. Each Atom Entry's `atom:content` element SHOULD include a brief description of the linked Resource.

Links to Navigation Feeds MUST use the "type" attribute `application/atom+xml;profile=opds-catalog;kind=navigation`. 

OPDS Catalog providers SHOULD choose the best relation for each Navigation Feed based on the relations in the section OPDS Catalog Relations. The relation "subsection" SHOULD be used if no other relation is more appropriate.

**Example**

An OPDS Catalog Root that is the top of a set of Navigation Feeds references three Acquisition Feeds using `atom:link`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<feed xmlns="http://www.w3.org/2005/Atom">
  <id>urn:uuid:2853dacf-ed79-42f5-8e8a-a7bb3d1ae6a2</id>
  <link rel="self"  
        href="/opds-catalogs/root.xml"
        type="application/atom+xml;profile=opds-catalog;kind=navigation"/>
  <link rel="start"
        href="/opds-catalogs/root.xml"
        type="application/atom+xml;profile=opds-catalog;kind=navigation"/>
  <title>OPDS Catalog Root Example</title>
  <updated>2010-01-10T10:03:10Z</updated>
  <author>
    <name>Spec Writer</name>
    <uri>http://opds-spec.org</uri>
  </author>

  <entry>
    <title>Popular Publications</title>
    <link rel="http://opds-spec.org/sort/popular"
          href="/opds-catalogs/popular.xml"
          type="application/atom+xml;profile=opds-catalog;kind=acquisition"/>
    <updated>2010-01-10T10:01:01Z</updated>
    <id>urn:uuid:d49e8018-a0e0-499e-9423-7c175fa0c56e</id>
    <content type="text">Popular publications from this catalog based on downloads.</content>
  </entry>
  <entry>
    <title>New Publications</title>
    <link rel="http://opds-spec.org/sort/new"
          href="/opds-catalogs/new.xml"
          type="application/atom+xml;profile=opds-catalog;kind=acquisition"/>
    <updated>2010-01-10T10:02:00Z</updated>
    <id>urn:uuid:d49e8018-a0e0-499e-9423-7c175fa0c56c</id>
    <content type="text">Recent publications from this catalog.</content>
  </entry>
  <entry>
    <title>Unpopular Publications</title>
    <link rel="subsection"
          href="/opds-catalogs/unpopular.xml"
          type="application/atom+xml;profile=opds-catalog;kind=acquisition"/>
    <updated>2010-01-10T10:01:00Z</updated>
    <id>urn:uuid:d49e8018-a0e0-499e-9423-7c175fa0c56d</id>
    <content type="text">Publications that could use some love.</content>
  </entry>
</feed>
```

### 2.2. Acquisition Feeds

An Acquisition Feed is an OPDS Catalog Feed Document that collects OPDS Catalog Entries into a single, ordered set. The simplest complete OPDS Catalog would be a single Acquisition Feed listing all of the available OPDS Catalog Entries from that provider. In more complex OPDS Catalogs, Acquisition Feeds are used to present and organize sets of related OPDS Catalog Entries for browsing and discovery by clients and aggregators.

Links to Acquisition Feeds MUST use the "type" attribute `application/atom+xml;profile=opds-catalog;kind=acquisition`.

For further details on limiting the size of Acquisition Feeds through pagination, Partial Catalog Entries, and compression, see the Section Listing Acquisition Feeds.

**Example**

```xml
An Acquisition Feed listing OPDS Catalog Entries from the "Unpopular Publications" Entry provided in the Navigation Feed Example:
<?xml version="1.0" encoding="UTF-8"?>
<feed xmlns="http://www.w3.org/2005/Atom"
      xmlns:dc="http://purl.org/dc/terms/"
      xmlns:opds="http://opds-spec.org/2010/catalog">
  <id>urn:uuid:433a5d6a-0b8c-4933-af65-4ca4f02763eb</id>

  <link rel="related"
        href="/opds-catalogs/vampire.farming.xml"
        type="application/atom+xml;profile=opds-catalog;kind=acquisition"/>
  <link rel="self"    
        href="/opds-catalogs/unpopular.xml"
        type="application/atom+xml;profile=opds-catalog;kind=acquisition"/>
  <link rel="start"  
        href="/opds-catalogs/root.xml"
        type="application/atom+xml;profile=opds-catalog;kind=navigation"/>
  <link rel="up"      
        href="/opds-catalogs/root.xml"
        type="application/atom+xml;profile=opds-catalog;kind=navigation"/>

  <title>Unpopular Publications</title>
  <updated>2010-01-10T10:01:11Z</updated>
  <author>
    <name>Spec Writer</name>
    <uri>http://opds-spec.org</uri>
  </author>

  <entry>
    <title>Bob, Son of Bob</title>
    <id>urn:uuid:6409a00b-7bf2-405e-826c-3fdff0fd0734</id>
    <updated>2010-01-10T10:01:11Z</updated>
    <author>
      <name>Bob the Recursive</name>
      <uri>http://opds-spec.org/authors/1285</uri>
    </author>
    <dc:language>en</dc:language>
    <dc:issued>1917</dc:issued>
    <category scheme="http://www.bisg.org/standards/bisac_subject/index.html"
              term="FIC020000"
              label="FICTION / Men's Adventure"/>
    <summary>The story of the son of the Bob and the gallant part he played in
      the lives of a man and a woman.</summary>
    <link rel="http://opds-spec.org/image"    
          href="/covers/4561.lrg.png"
          type="image/png"/>
    <link rel="http://opds-spec.org/image/thumbnail"
          href="/covers/4561.thmb.gif"
          type="image/gif"/>

    <link rel="alternate"
          href="/opds-catalogs/entries/4571.complete.xml"
          type="application/atom+xml;type=entry;profile=opds-catalog"
          title="Complete Catalog Entry for Bob, Son of Bob"/>

    <link rel="http://opds-spec.org/acquisition"
          href="/content/free/4561.epub"
          type="application/epub+zip"/>
    <link rel="http://opds-spec.org/acquisition"
          href="/content/free/4561.mobi"
          type="application/x-mobipocket-ebook"/>
 </entry>

 <entry>
    <title>Modern Online Philately</title>
    <id>urn:uuid:7b595b0c-e15c-4755-bf9a-b7019f5c1dab</id>
    <author>
      <name>Stampy McGee</name>
      <uri>http://opds-spec.org/authors/21285</uri>
    </author>
    <author>
      <name>Alice McGee</name>
      <uri>http://opds-spec.org/authors/21284</uri>
    </author>
    <author>
      <name>Harold McGee</name>
      <uri>http://opds-spec.org/authors/21283</uri>
    </author>
    <updated>2010-01-10T10:01:10Z</updated>
    <rights>Copyright (c) 2009, Stampy McGee</rights>
    <dc:identifier>urn:isbn:978029536341X</dc:identifier>
    <dc:publisher>StampMeOnline, Inc.</dc:publisher>
    <dc:language>en</dc:language>
    <dc:issued>2009-10-01</dc:issued>
    <content type="text">The definitive reference for the web-curious
      philatelist.</content>
    <link rel="http://opds-spec.org/image"    
          href="/covers/11241.lrg.jpg"
          type="image/jpeg"/>

    <link rel="http://opds-spec.org/acquisition/buy"
          href="/content/buy/11241.epub"
          type="application/epub+zip">
      <opds:price currencycode="USD">18.99</opds:price>
      <opds:price currencycode="GBP">11.99</opds:price>
    </link>
 </entry>
</feed>
```

### 2.3. OPDS Catalog Root

The OPDS Catalog Root is the top-level OPDS Catalog Feed Document. It is either a single Acquisition Feed in the simple case or the start of a set of Navigation Feeds. Every OPDS Catalog MUST have one and only one OPDS Catalog Root.

External links to the OPDS Catalog Resource SHOULD use the IRI of the OPDS Catalog Root.

Each OPDS Catalog Feed Document SHOULD contain an `atom:link` element with a link relation of "start", which references the OPDS Catalog Root Resource.


### 2.4. Element Definitions

#### The `atom:feed` Element

The `atom:feed` element is the document (i.e., top-level) element of an OPDS Catalog Feed Document, acting as a container for metadata, hierarchy, and Publications associated with the OPDS Catalog. Its element children consist of metadata elements followed by zero or more `atom:entry` child elements.

The following child elements are refined by this specification:

- OPDS Catalog Feed Documents SHOULD contain one `atom:link` element with a "rel" attribute value of "self". This is the preferred URI for retrieving the `atom:feed` representing this OPDS Catalog Feed Document.

## 3. Search

An OPDS Catalog MAY provide a search facility through an [OpenSearch] description document. 

Links to [OpenSearch] description documents MUST use the "search" relation value and the `application/opensearchdescription+xml` media type as defined in the "Autodiscovery" section of the [OpenSearch] specification.

```xml
<link rel="search"
      href="search.xml"
      type="application/opensearchdescription+xml"/>
````

In an [OpenSearch] description document, the search interface MUST use the media type associated to OPDS Catalogs:

```xml
<Url type="application/atom+xml;profile=opds-catalog;kind=acquisition"
     template="http://example.com/search?q={searchTerms}" />
````

An OPDS Catalog MAY also provide more advanced possibilities for its search endpoint, using one or more fully qualified parameters from the Atom namespace such as:

- atom:author
- atom:contributor
- atom:title


OPDS Catalog Feed Documents MAY include elements from the [OpenSearch] namespace such as "opensearch:totalResults" or "opensearch:itemsPerPage" in [OpenSearch] responses.


**Open Search Description Document Example**

In order to provide a search endpoint that supports both basic (keyword based) and advanced search, an OPDS Catalog could provide the following template in its [OpenSearch] Description document:
```xml
<Url type="application/atom+xml;profile=opds-catalog"
     xmlns:atom="http://www.w3.org/2005/Atom"
     template="http://example.com/search?q={searchTerms}&amp;author={atom:author}&amp;contributor={atom:contributor}&amp;title={atom:title}" />
````

With such a template, an OPDS Client could for example support the following search queries:

- http://example.com/search?q=gardening
- http://example.com/search?q=gardening&author=smith
- http://example.com/search?author=drucker
- http://example.com/search?author=ferriss&title=four

## x. Media Type Considerations

### x.1. The Atom Format Type Parameter
The Atom Publishing Protocol [RFC5023] defines the Atom Format Type Parameter.
Publishers of OPDS Catalogs SHOULD use the type parameter to help clients distinguish between relations to 
OPDS Catalog Entries and OPDS Catalog Feeds.

### x.2. The OPDS Catalog Profile Parameter
Relations to OPDS Catalog Feed Document and OPDS Catalog Entry Document Resources MUST use a "profile" parameter 
following Section 4.3 of [RFC4288] with the value "opds-catalog". 
This profile parameter provides clients with an advisory hint that the Resource should be a component of an OPDS Catalog.

The complete media type for a relation to an OPDS Catalog Entry Document Resource MUST be:

`application/atom+xml;type=entry;profile=opds-catalog`

### x.3. The OPDS Kind Parameter
In addition to the new "profile" parameter, this specification also introduces a new "kind" parameter following Section 4.3 
of [RFC4288] with the value "acquisition" or "navigation". 
This "kind" parameter provides clients with an advisory hint whether the Resource should be an Acquisition Feed or a 
Navigation Feed.

This value is intended to make it easier for OPDS clients to display basic contextual information about the feed without 
requiring that those clients dereference, parse, and analyze linked resources. That said, the client MUST NOT 
assume this parameter to provide completely accurate information about the nature of the feed. 

The complete media type for a relation to an Acquisition Feed MUST be:

`application/atom+xml;profile=opds-catalog;kind=acquisition`

The complete media type for a relation to a Navigation Feed MUST be:

`application/atom+xml;profile=opds-catalog;kind=navigation`
