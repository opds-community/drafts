# OPDS Catalog 1.2

The Open Publication Distribution System (OPDS) Catalog format is a syndication format for electronic 
publications based on Atom and HTTP. OPDS Catalogs enable the aggregation, distribution, discovery, 
and acquisition of electronic publications. 

OPDS Catalogs use existing or emergent open standards and conventions, with a priority on simplicity.

## Status of this Document

This document is a draft of the 1.2 version of the OPDS Catalog specification. 

## Table of Contents


- [1. Overview](#1-overview)
  * [1.1. Introduction](#11-introduction)
  * [1.2. Terminology](#12-terminology)
  * [1.3. Conformance Statements](#13-conformance-statements)
- [2. OPDS Catalog Feed Documents](#2-opds-catalog-feed-documents)
  * [2.1. Navigation Feeds](#21-navigation-feeds)
  * [2.2. Acquisition Feeds](#22-acquisition-feeds)
  * [2.3. OPDS Catalog Root](#23-opds-catalog-root)
  * [2.4. Element Definitions](#24-element-definitions)
- [3. Search](#3-search)
- [4. Facets](#4-facets)
- [5. OPDS Catalog Entry Documents](#5-opds-catalog-entry-documents)
  * [5.1. Metadata](#51-metadata)
      + [5.1.1. Relationship Between Atom and Dublin Core Metadata](#511-relationship-between-atom-and-dublin-core-metadata)
      + [5.1.2. Partial and Complete Catalog Entries](#512-partial-and-complete-catalog-entries)
      + [5.1.3. Summary and Content](#513-summary-and-content)
  * [5.2. Catalog Entry Relations](#52-catalog-entry-relations)
      + [5.2.1. Acquisition Relations](#521-acquisition-relations)
      + [5.2.2. Artwork Relations](#522-artwork-relations)
  * [5.3. Element Definitions](#53-element-definitions)
- [6. Additional Link Relations](#6-additional-link-relations)
  * [6.1. Relations for Previously Acquired Content](#61-relations-for-previously-acquired-content)
  * [6.2. Sorting Relations](#62-sorting-relations)
  * [6.3. Featured Relation](#63-featured-relation)
  * [6.4. Recommendations](#64-recommendations)
- [x. Media Type Considerations](#x-media-type-considerations)
  * [x.1. The Atom Format Type Parameter](#x1-the-atom-format-type-parameter)
  * [x.2. The OPDS Catalog Profile Parameter](#x2-the-opds-catalog-profile-parameter)
  * [x.3. The OPDS Kind Parameter](#x3-the-opds-kind-parameter)

## 1. Overview

### 1.1. Introduction

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

### 1.2. Terminology

<dl>
  <dt>Acquisition Feed</dt>
  <dd>An Atom Feed whose Atom Entries are exclusively OPDS Catalog Entries.</dd>

  <dt>Acquisition Link</dt>
  <dd>An atom:link element with a relation that begins with "<a href="http://opds-spec.org/acquisition">http://opds-spec.org/acquisition</a>" and refers to the Resource which holds the content of the described Publication or the Resource through which it may be acquired for any OPDS Catalog Entry. See the Sections Acquisition Relations and Acquiring Publications. They are serialized as OPDS Catalog Feed Documents.</dd>

  <dt>Complete Catalog Entry</dt>
  <dd>An OPDS Catalog Entry that includes all known metadata about the described Publication and is referenced by a Partial Catalog Entry.</dd>
      
  <dt>Facet</dt>
  <dd>A subset or an alternate order for an Acquisition Feed. In an OPDS Catalog, a Facet is represented as an atom:link in an Acquisition Feed.</dd>
        
  <dt>IRI</dt>
  <dd>An Internationalized Resource Identifier as defined in [RFC3987].</dd>

  <dt>Navigation Feed</dt>
  <dd>An Atom Feed whose Atom Entries are not OPDS Catalog Entries but instead links to other Navigation Feeds, Acquisition Feeds, or other Resources to establish a hierarchical, browsable presentation of the OPDS Catalog.</dd> 

  <dt>OPDS Catalog</dt>
  <dd>All of the Atom Feeds (Acquisition and Navigation) and Entries (Partial and Complete) following this specification published together to describe a consolidated group of available Publications.</dd> 

  <dt>OPDS Catalog Entry</dt>
  <dd>An Atom Entry that provides a representation of an available Publication and includes an Acquisition Link. They are serialized as OPDS Catalog Entry Documents.</dd> 

  <dt>OPDS Catalog Entry Document</dt>
  <dd>The formal name for the XML documents that are a refinement of an "atom:entry" from the Atom Syndication Format [RFC4287] that this specification defines.</dd>

  <dt>OPDS Catalog Feed Document</dt>
  <dd>The formal name for XML documents that are a refinement of an "atom:feed" from the Atom Syndication Format [RFC4287] that this specification defines.</dd>

  <dt>OPDS Catalog Root</dt>
  <dd>The Atom Feed that represents either the complete OPDS Catalog (a single Acquisition Feed) or the Atom Feed that starts a set of Navigation Feeds. External links to the OPDS Catalog SHOULD reference the OPDS Catalog Root Resource.</dd>

  <dt>Partial Catalog Entry</dt>
  <dd>An OPDS Catalog Entry that includes the minimal required metadata about the described Publication but no other metadata and links to the Complete Catalog Entry.</dd>

  <dt>Publication</dt>
  <dd>An electronic document, typically available as a single file. OPDS Catalogs are agnostic about the particular format of Publications.</dd>

  <dt>relation (or "relation of")</dt>
  <dd>Refers to the "rel" attribute value of an atom:link element.</dd>

  <dt>Representation</dt>
  <dd>An entity included with a request or response as defined in [RFC2616].</dd>

  <dt>Resource</dt>
  <dd>A network-accessible data object or service identified by an IRI, as defined in [RFC2616]. See [REC-webarch] for further discussion on Resources.</dd>

  <dt>URI</dt>
  <dd>A Uniform Resource Identifier as defined in [RFC3986]. In this specification, the phrase "the URI of a document" is shorthand for "a URI which, when dereferenced, is expected to produce that document as a representation".</dd>
</dl>

### 1.3. Conformance Statements

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC2119].


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

**Example**

An Acquisition Feed listing OPDS Catalog Entries from the "Unpopular Publications" Entry provided in the Navigation Feed Example:

```xml
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
```

In an [OpenSearch] description document, the search interface MUST use the media type associated to OPDS Catalogs:

```xml
<Url type="application/atom+xml;profile=opds-catalog;kind=acquisition"
     template="http://example.com/search?q={searchTerms}" />
```

An OPDS Catalog MAY also provide more advanced possibilities for its search endpoint, using one or more fully qualified parameters from the Atom namespace such as:

- atom:author
- atom:contributor
- atom:title


OPDS Catalog Feed Documents MAY include elements from the [OpenSearch] namespace such as `opensearch:totalResults` or `opensearch:itemsPerPage` in [OpenSearch] responses.


**Open Search Description Document Example**

In order to provide a search endpoint that supports both basic (keyword based) and advanced search, an OPDS Catalog could provide the following template in its [OpenSearch] Description document:

```xml
<Url type="application/atom+xml;profile=opds-catalog"
     xmlns:atom="http://www.w3.org/2005/Atom"
     template="http://example.com/search?q={searchTerms}&amp;author={atom:author}&amp;contributor={atom:contributor}&amp;title={atom:title}" />
```

With such a template, an OPDS Client could for example support the following search queries:

- http://example.com/search?q=gardening
- http://example.com/search?q=gardening&author=smith
- http://example.com/search?author=drucker
- http://example.com/search?author=ferriss&title=four

## 4. Facets

An Acquisition Feed MAY offer multiple links to reorder the Publications listed in the feed or limit them to a subset. This specification defines one new relation to identify such links as Facets:

- "http://opds-spec.org/facet": An Acquisition Feed with a subset or an alternate order of the Publications listed.

Links using this relation MUST only appear in Acquisition Feeds.

**Example**

```xml
<link rel="http://opds-spec.org/facet" 
      href="/sci-fi" 
      title="Science-Fiction" 
      opds:facetGroup="Categories" 
      opds:activeFacet="true" />
 
<link rel="http://opds-spec.org/facet" 
      href="/romance" 
      title="Romance"  
      opds:facetGroup="Categories" 
      thr:count="600" />
 
<link rel="http://opds-spec.org/facet" 
      href="/thrillers" 
      title="Thrillers"  
      opds:facetGroup="Categories" 
      thr:count="1200" />
 
<link rel="http://opds-spec.org/facet" 
      href="/sci-fi/english" 
      title="English" 
      opds:facetGroup="Language" 
      opds:activeFacet="true" />
 
<link rel="http://opds-spec.org/facet" 
      href="/sci-fi/french" 
      title="French"  
      opds:facetGroup="Language" 
      thr:count="40" />
```

#### The `opds:facetGroup` Attribute

Facets CAN be grouped together by the OPDS Catalog provider using an `opds:facetGroup` attribute. The value of this attribute is the name of the group.

A Facet MUST NOT appear in more than a single group.

#### The "opds:activeFacet" Attribute

A Facet is considered active, if the attribute associated to the Facet is already being used to filter Publications in the current Acquisition Feed.

The OPDS Catalog provider SHOULD indicate that a Facet is active using the `opds:activeFacet` attribute set to "true".

If the Facet is not active, the `opds:activeFacet` attribute SHOULD NOT appear in the link.

In a group of Facets, an OPDS Catalog provider MUST NOT mark more than one Facet as active.

#### The "thr:count" Attribute

The OPDS Catalog provider MAY provide an additional hint about the number of items expected in the Acquisition Feed, if an OPDS client follows a link.

The `thr:count` attribute, extracted from [RFC4685] MAY be added to a Facet in order to provide this information.


## 5. OPDS Catalog Entry Documents

OPDS Catalog Entry Documents are Atom [RFC4287] Entry documents that provide a complete representation of the metadata and data associated with an available Publication.

Each OPDS Catalog Entry Document MUST include at least one Acquisition Link.

### 5.1. Metadata

#### 5.1.1. Relationship Between Atom and Dublin Core Metadata

OPDS Catalog providers are encouraged to include metadata from the Dublin Core Metadata Initiative (DCMI) Metadata Terms [DCTERMS] whenever appropriate. However, these elements are not required. Some of the elements defined in [DCTERMS] overlap in meaning with similar elements defined by Atom in [RFC4287]. In general, OPDS Catalog Entries prefer the Atom elements over the [DCTERMS] elements and the following rules should be used by OPDS Catalog providers when choosing elements:

- OPDS Catalog Entries MUST be identified by an `atom:id`. One or more `dc:identifier` elements SHOULD be used to identify the represented Publication, if appropriate metadata is available, and MUST NOT identify the OPDS Catalog Entry.
- OPDS Catalog Entries MUST include an `atom:updated` element indicating when the OPDS Catalog Entry was last updated. A `dc:issued` element SHOULD be used to indicate the first publication date of the Publication and MUST NOT represent any date related to the OPDS Catalog Entry.
- OPDS Catalog Entries MUST include an `atom:title` representing the Publication's title and MUST NOT use `dc:title`.
- OPDS Catalog Entries SHOULD use `atom:author` to represent the Publication's creators and SHOULD NOT use `dc:creator`.
- OPDS Catalog Entries SHOULD use `atom:category` to represent the Publication's category, keywords, key phrases, or classification codes and SHOULD NOT use `dc:subject`.
- OPDS Catalog Entries SHOULD use `atom:rights` to represent rights held in and over the Publication and SHOULD NOT use `dc:rights`.
- OPDS Catalog Entries SHOULD use `atom:summary` and/or `atom:content` to describe the Publication SHOULD NOT use `dc:description` or `dc:abstract`.
- OPDS Catalog Entries MAY use `atom:contributor` to represent contributors to the Publication beside its creators.
- OPDS Catalog Entries MAY use `atom:published` to indicate when the OPDS Catalog Entry was first accessible.

All metadata elements required by Atom are required in OPDS Catalog Entries and Feeds.

Following Atom [RFC4287] Section 4.2.6, the content of an `atom:id` identifying an OPDS Catalog Entry MUST NOT change when the OPDS Catalog Entry is "relocated, migrated, syndicated, republished, exported, or imported" and "MUST be created in a way that assures uniqueness."

#### 5.1.2. Partial and Complete Catalog Entries

An OPDS Catalog Entry may be represented as either a Partial or Complete Catalog Entry. Partial Catalog Entries include only critical metadata elements to reduce the size of OPDS Catalog documents for bandwidth- and resource-limited clients and link to their alternate representation as a Complete Catalog Entries, which include all metadata elements.

A Partial Catalog Entry MUST include an "alternate" link relation referencing the Complete Catalog Entry Resource and that atom:link MUST use the "type" attribute `application/atom+xml;type=entry;profile=opds-catalog`.

Any OPDS Catalog Entry without a link to a Complete Catalog Entry MUST include all metadata elements.

Partial Catalog Entries SHOULD include the following metadata elements, if available:

- `atom:category`
- `atom:contributor`
- `atom:rights`
- `opds:price`

OPDS Catalog providers are strongly encouraged to limit metadata elements not required by Atom, OPDS, or listed above from Partial Catalog Entries to help facilitate the efficient transmission and consumption of OPDS Catalog documents. Implementers are encouraged to include metadata elements from other vocabularies in Complete Catalog Entries.

**Example**

An example of a Partial Catalog Entry (as would appear in an Acquisition Feed) for a Publication available in two formats:

```xml
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
  <summary type="text">The story of the son of the Bob and the gallant part he played in
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
```

The Complete Catalog Entry for the same Publication:

```xml
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
 
  <summary type="text">The story of the son of the Bob and the gallant part he played in
    the lives of a man and a woman.</summary>
  <content type="text">The story of the son of the Bob and the gallant part
    he played in the lives of a man and a woman. Bob begins his humble life
    under the wandering eye of his senile mother, but quickly learns how to
    escape into the wilder world. Follow Bob as he uncovers his father's past
    and uses those lessons to improve the lives of others.</content>
 
  <link rel="http://opds-spec.org/image"     
        href="/covers/4561.lrg.png"
        type="image/png"/> 
  <link rel="http://opds-spec.org/image/thumbnail" 
        href="/covers/4561.thmb.gif"
        type="image/gif"/>
 
  <link rel="self"
        href="/opds-catalogs/entries/4571.complete.xml"
        type="application/atom+xml;type=entry;profile=opds-catalog"/>
 
  <link rel="http://opds-spec.org/acquisition" 
        href="/content/free/4561.epub"
        type="application/epub+zip"/>
  <link rel="http://opds-spec.org/acquisition" 
        href="/content/free/4561.mobi"
        type="application/x-mobipocket-ebook"/>
</entry>
```

#### 5.1.3. Summary and Content

OPDS Catalog Entries use `atom:content` and `atom:summary` elements to describe a Publication. This is a distinction from Atom [RFC4287], which uses "atom:content" for the "content of the entry." Instead, OPDS Catalog Entries reference an electronic document with the Publication's content using Acquisition Links. OPDS Catalog Entries SHOULD include either `atom:summary` or `atom:content` elements or both to provide a description, summary, abstract, or excerpt of the Publication.

An `atom:summary` element in an OPDS Catalog Entry MUST use a "type" attribute of "text" and the content MUST NOT contain child elements. The content of an `atom:summary` element SHOULD NOT duplicate the content of `atom:title` or `atom:content`. Such text is intended to be presented to humans in a readable fashion. The restriction of the content of `atom:summary` and the "type" attribute is a restriction of Atom [RFC4287].

An `atom:content` element in an OPDS Catalog Entry either contains or links to a description, summary, abstract, or excerpt of the Publication. The behavior for "atomOutOfLineContent" and the "src" attribute are defined in Section 4.1.3 of [RFC4287].

If an OPDS Catalog Entry includes both `atom:content` and `atom:summary`, the `atom:content` SHOULD NOT be included in the Partial Catalog Entry. Both elements SHOULD be included in the Complete Catalog Entry.

```
atomSummary = 
  element atom:summary {
  atomCommonAttributes,
  attribute type { "text" }?,
    text
  }
```

### 5.2. Catalog Entry Relations

OPDS Catalog Entry Documents SHOULD include links to related Resources. This specification defines new relations for linking from OPDS Catalog Entry Documents. They are defined in the Sections Acquisition Relations and Artwork Relations.

OPDS Catalog providers are encouraged to use relations from [RFC5988] inside OPDS Catalog Entry Documents where appropriate.

#### 5.2.1. Acquisition Relations

Acquisition relations describe the extent of the content referred to by an Acquisition Link or the manner in which that Resource may be acquired. This specification defines six Acquisition Relations, which all begin with "http://opds-spec.org/acquisition":

- "http://opds-spec.org/acquisition": A generic relation that indicates that the complete representation of the content Resource may be retrieved.
- "http://opds-spec.org/acquisition/open-access": Indicates that the complete representation of the Resource can be retrieved without any requirement, which includes payment and registration.
- "http://opds-spec.org/acquisition/borrow": Indicates that the complete representation of the content Resource may be retrieved as part of a lending transaction.
- "http://opds-spec.org/acquisition/buy": Indicates that the complete representation of the content Resource may be retrieved as part of a purchase.
- "http://opds-spec.org/acquisition/sample": Indicates that a subset of the content Resource may be retrieved.
- "http://opds-spec.org/acquisition/subscribe": Indicates that the complete representation of the content Resource may be retrieved as part of a subscription.

#### 5.2.2. Artwork Relations

OPDS Catalog Entries MAY include `atom:link` elements to images that provide a visual representation of the Publication. For many Publications, these images will be the Publication's artwork. This specification defines two Artwork Relations, which begin with "http://opds-spec.org/image":

- "http://opds-spec.org/image": a graphical Resource associated to the OPDS Catalog Entry
- "http://opds-spec.org/image/thumbnail": a reduced-size version of a graphical Resource associated to the OPS Catalog Entry
Image resources related by http://opds-spec.org/image/thumbnail SHOULD be suitable for presentation at a small size.

The atom:links with these relations SHOULD include at least one link with a type attribute of "image/gif", "image/jpeg", or "image/png" and the image Resources MUST be in GIF, JPEG, or PNG format.

Additional atom:links MAY also include additional resources using a vector-based format.

While either image Resource is optional and may be included independently, OPDS Catalog providers are encouraged to provide both these relations and Resources whenever possible.

Some OPDS Catalog providers MAY choose to provide http://opds-spec.org/image/thumbnail image Resources using the "data" URL scheme from [RFC2397]. scheme if they support Artwork relations.

### 5.3. Element Definitions

#### The `atom:entry` Element

The `atom:entry` element is the document (i.e., top-level) element of an OPDS Catalog Entry Document, acting as a container for metadata and data associated with an available Publication.

The following child elements are refined by this specification:

- OPDS Catalog Entry Documents MUST contain at least one Acquisition Link, an atom:link element with a rel attribute that begins with "http://opds-spec.org/acquisition".


#### The `atom:link` Element

The `atom:link` element defines a reference from an Atom Entry or Atom Feed to a Resource.

```
atomLink =
  element atom:link {
    atomCommonAttributes ,
    attribute href { atomUri },
    attribute type { atomMediaType }? ,
    attribute hreflang { atomLanguageTag }? ,
    attribute title { text }? ,
    attribute length { text }? ,
    ((attribute rel { "http://opds-spec.org/facet" }, (attribute 
    opds:facetGroup { text }? & attribute opds:activeFacet { "true" }? ))
    |
    (attribute rel { "http://opds-spec.org/acquisition/buy" }, opdsPrice+ )
    |
    (attribute rel { OPDSUrisExceptBuy }, opdsPrice*)
    |
    (attribute rel { atomNCName | ( atomUriExceptOPDS ) } ))? ,
    (opdsIndirectAcquisition |
    anyOPDSForeignElement |
    text)*
  }
     
  # Here is where OPDS Catalogs use John Cowan's pragmatic evaluation of an
  # IRI. This modifies xsd:anyURI in XSD 1.0 to exclude ASCII characters not
  # valid in 1.1 or IRI's without being escaped. This matches the OPDS and Atom
  # specs, but not the non-normative atom.rnc.
  atomUri = xsd:anyURI - xsd:string {pattern = '.*[ <>{}|^`"\nrt].*'}
```
  
The following child elements are defined by this specification:

- `atom:link` elements with a rel attribute value set to "http://opds-spec.org/acquisition/buy" MUST contain at least one `opds:price` element.
- `atom:link` elements with a rel attribute set to "http://opds-spec.org/acquisition/borrow", "http://opds-spec.org/acquisition/subscribe", or "http://opds-spec.org/acquisition/sample" MAY contain one or more `opds:price` elements.

#### The `opds:price` Element

The `opds:price` element represents the acquisition price in a particular currency of an individual Publication in a particular format from a particular provider. This element can appear as a child of the `atom:link` element (in OPDS Catalog Entry Documents).

The content of `opds:price` is text describing a currency value. A currency sign MUST NOT be included.

```
opdsPrice = 
  element opds:price {
    atomCommonAttributes,
    attribute currencycode { opdsPriceCurrencyCode },
    xsd:decimal { minInclusive="0.0" } 
  }
```
      
In the `opds:price` element, the value of the "currencycode" attribute MUST be an active code from [ISO4217] representing a currency. It defines the currency used for the content of `the opds:price` element.

#### The `opds:indirectAcquisition` Element

In some cases, the OPDS Catalog provider MAY require the client to acquire an intermediate Resource before acquiring the final Publication. This can be the case for example with containers (archive formats, multimedia containers for various formats) or when a payment is required (need to go through a series of HTML pages to handle the transaction).

The `opds:indirectAcquisition` element represents these secondary media types [MIMEREG] that the client can expect to acquire if they follow the Acquisition Link.

These `opds:indirectAcquisition` elements CAN be arbitrarily nested to represent several levels of indirection.

```
opdsIndirectAcquisition = 
  element opds:indirectAcquisition {
    atomCommonAttributes,
    attribute type { atomMediaType },
    (  anyOPDSForeignElement | 
      opdsIndirectAcquisition) *
  }
```

## 6. Additional Link Relations

OPDS Catalog Feed Documents SHOULD include links to other available Acquisition and Navigation Feeds and other related Resources to encourage independent navigation.

These relations MAY be used by clients to provide additional or alternative navigation, both in a Navigation Feed or an Acquisition Feed.

The following relations are derived from [RFC5988], with some clarification:

- "start": The OPDS Catalog Root.
- "subsection": A Navigation Feed not better described by a more specific relation.

When creating an OPDS Catalog with Navigation and Acquisition Feeds, OPDS Catalog providers are encouraged to use the relations defined in this specification and [RFC5988]. If no appropriate relation is found, the Feeds SHOULD use a descriptive "atom:title" element and the "atom:link"s SHOULD use a descriptive "title" attribute.

### 6.1. Relations for Previously Acquired Content

An OPDS catalog MAY track content that was previously acquired by the user. This specification defines two new relations to identify Acquisition Feeds listing such resources:

- "http://opds-spec.org/shelf": A Resource that includes a user's existing set of Acquired Content, which MAY be represented as an OPDS Catalog.
- "http://opds-spec.org/subscriptions": A Resource that includes a user's set of subscriptions, which MAY be represented as an OPDS Catalog.
An OPDS client MAY use these links to automatically sync content to the user's local shelf or check for periodical content updates.

### 6.2. Sorting Relations

This specification defines two new relations, which begin with "http://opds-spec.org/sort", to describe how an Acquisition Feed is sorted. These relations SHOULD be used when creating Navigation Feeds and the OPDS Catalog Root, if applicable.

- "http://opds-spec.org/sort/new": An Acquisition Feed with newly released OPDS Catalog Entries. These Acquisition Feeds typically contain a subset of the OPDS Catalog Entries in an OPDS Catalog based on the publication date of the Publication.
- "http://opds-spec.org/sort/popular": An Acquisition Feed with popular OPDS Catalog Entries. These Acquisition Feeds typically contain a subset of the OPDS Catalog Entries in an OPDS Catalog based on a numerical ranking criteria.
Acquisition Feeds using the "http://opds-spec.org/sort/new" relation SHOULD be ordered with the most recent items first. Acquisition Feeds using the "http://opds-spec.org/sort/popular" relation SHOULD be ordered with the most popular items first.

### 6.3. Featured Relation

This specification also defines a relation to describe an Acquisition Feed of featured items. This relation SHOULD be used when creating Navigation Feeds and the OPDS Catalog Root, if applicable.

- "http://opds-spec.org/featured": An Acquisition Feed with featured OPDS Catalog Entries. These Acquisition Feeds typically contain a subset of the OPDS Catalog Entries in an OPDS Catalog that have been selected for promotion by the OPDS Catalog provider. No order is implied.

### 6.4. Recommendations

This specification also defines a relation to describe an Acquisition Feed of recommended items. Since recommendations can be customized for the user, such feed may require some kind of prior authentication. This relation SHOULD be used when creating Navigation Feeds and the OPDS Catalog Root, if applicable.

- "http://opds-spec.org/recommended": An Acquisition Feed with recommended OPDS Catalog Entries. These Acquisition Feeds typically contain a subset of the OPDS Catalog Entries in an OPDS Catalog that have been selected specifically for the user. Acquisition Feeds using the "http://opds-spec.org/recommended" relation SHOULD be ordered with the most recommended items first.

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
