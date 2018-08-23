[![OPDS Logo](https://opds.io/img/logo.png)](https://opds.io)

<style>
.rfc {
    color: #d55;
    font-variant: small-caps;
    font-style: normal;
}
</style>

# OPDS Catalog 1.2

The Open Publication Distribution System (OPDS) Catalog format is a syndication format for electronic 
publications based on Atom and HTTP. OPDS Catalogs enable the aggregation, distribution, discovery, 
and acquisition of electronic publications. 

OPDS Catalogs use existing or emergent open standards and conventions, with a priority on simplicity.

**This version:**

* [https://drafts.opds.io/opds-1.2](https://drafts.opds.io/opds-1.2)

**Previous version:**

* [https://specs.opds.io/opds-1.1.html](https://specs.opds.io/opds-1.1.html)

**Editors:**

* Hadrien Gardeur ([Feedbooks](http://www.feedbooks.com))
* Leonard Richardson ([NYPL](https://www.nypl.org/))

**Participate:**

* [GitHub opds-community/drafts](https://github.com/opds-community/drafts)
* [File an issue](https://github.com/opds-community/drafts/issues)

## Status of this Document

This document is a draft of the 1.2 version of the OPDS Catalog specification. 

## Table of Contents

- [1. Overview](#1-overview)
  * [1.1. Introduction](#11-introduction)
  * [1.2. Terminology](#12-terminology)
  * [1.3. Conformance Statements](#13-conformance-statements)
  * [1.4. RELAX NG Schema](#14-relax-ng-schema)
- [2. OPDS Catalog Feed Documents](#2-opds-catalog-feed-documents)
  * [2.1. OPDS Catalog Root](#21-opds-catalog-root)
  * [2.2. Navigation Feeds](#22-navigation-feeds)
  * [2.3. Acquisition Feeds](#23-acquisition-feeds)
  * [2.4. Listing Acquisition Feeds](#24-listing-acquisition-feeds)
  * [2.5. Complete Acquisition Feeds](#25-complete-acquisition-feeds)
  * [2.6. Element Definitions](#26-element-definitions)
      + [The `atom:feed` Element](#the--atom-feed--element)
- [3. Search](#3-search)
- [4. Facets](#4-facets)
    + [The `opds:facetGroup` Attribute](#the--opds-facetgroup--attribute)
    + [The `opds:activeFacet` Attribute](#the--opds-activefacet--attribute)
    + [The `thr:count` Attribute](#the--thr-count--attribute)
- [5. OPDS Catalog Entry Documents](#5-opds-catalog-entry-documents)
  * [5.1. Metadata](#51-metadata)
      + [5.1.1. Relationship Between Atom and Dublin Core Metadata](#511-relationship-between-atom-and-dublin-core-metadata)
      + [5.1.2. Partial and Complete Catalog Entries](#512-partial-and-complete-catalog-entries)
      + [5.1.3. Summary and Content](#513-summary-and-content)
  * [5.2. Catalog Entry Relations](#52-catalog-entry-relations)
      + [5.2.1. Acquisition Relations](#521-acquisition-relations)
      + [5.2.2. Artwork Relations](#522-artwork-relations)
  * [5.3. Acquiring Publications](#53-acquiring-publications)
  * [5.4. Element Definitions](#54-element-definitions)
      + [The `atom:entry` Element](#the--atom-entry--element)
      + [The `atom:link` Element](#the--atom-link--element)
      + [The `opds:price` Element](#the--opds-price--element)
      + [The `opds:indirectAcquisition` Element](#the--opds-indirectacquisition--element)
- [6. Additional Link Relations](#6-additional-link-relations)
  * [6.1. Relations for Previously Acquired Content](#61-relations-for-previously-acquired-content)
  * [6.2. Sorting Relations](#62-sorting-relations)
  * [6.3. Featured Relation](#63-featured-relation)
  * [6.4. Recommendations](#64-recommendations)
- [7. Other Considerations](#7-other-considerations)
  * [7. Discovering OPDS Catalogs](#7-discovering-opds-catalogs)
  * [7. Aggregating OPDS Catalogs](#7-aggregating-opds-catalogs)
  * [7.1 Media Type Considerations](#71-media-type-considerations)
      + [7.1.1. The Atom Format Type Parameter](#711-the-atom-format-type-parameter)
      + [7.1.2. The OPDS Catalog Profile Parameter](#712-the-opds-catalog-profile-parameter)
      + [7.1.3. The OPDS Kind Parameter](#713-the-opds-kind-parameter)
  * [7.2. Security Considerations](#72-security-considerations)
      + [7.2.1. Securing a Catalog](#721-securing-a-catalog)
      + [7.2.2 Linked Resources](#722-linked-resources)
      + [7.2.3 URIs and IRIs](#723-uris-and-iris)
      + [7.2.4 Code Injection and Cross Site Scripting](#724-code-injection-and-cross-site-scripting)
  * [7.3. Bandwidth and Processing Considerations](#73-bandwidth-and-processing-considerations)
- [8. References](#8-references)
  * [8.1. Normative References](#81-normative-references)
  * [8.2. Informative References](#82-informative-references)
- [Appendix A. Contributors](#appendix-a-contributors)
- [Appendix B. RELAX NG Compact Schema](#appendix-b-relax-ng-compact-schema)


## 1. Overview

### 1.1. Introduction

The Open Publication Distribution System (OPDS) Catalog format is a syndication format for electronic publications 
based on Atom [[RFC4287](https://tools.ietf.org/html/rfc4287)] and HTTP [[RFC2616](https://tools.ietf.org/html/rfc2616)]. OPDS Catalogs enable available electronic publications to be:

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
  <dd>An atom:link element with a relation that begins with <code>http://opds-spec.org/acquisition</code> and refers to the Resource which holds the content of the described Publication or the Resource through which it may be acquired for any OPDS Catalog Entry. Check the following sections for more detail: <a href="#521-acquisition-relations">Acquisition Relations</a> and <a href="#53-acquiring-publications">Acquiring Publications</a>.</dd>

  <dt>Complete Catalog Entry</dt>
  <dd>An OPDS Catalog Entry that includes all known metadata about the described Publication and is referenced by a Partial Catalog Entry.</dd>
      
  <dt>Facet</dt>
  <dd>A subset or an alternate order for an Acquisition Feed. In an OPDS Catalog, a Facet is represented as an atom:link in an Acquisition Feed.</dd>
        
  <dt>IRI</dt>
  <dd>An Internationalized Resource Identifier as defined in [<a href="https://tools.ietf.org/html/rfc3987">RFC3987</a>].</dd>

  <dt>Navigation Feed</dt>
  <dd>An Atom Feed whose Atom Entries are not OPDS Catalog Entries but instead links to other Navigation Feeds, Acquisition Feeds, or other Resources to establish a hierarchical, browsable presentation of the OPDS Catalog.</dd> 

  <dt>OPDS Catalog</dt>
  <dd>All of the Atom Feeds (Acquisition and Navigation) and Entries (Partial and Complete) following this specification published together to describe a consolidated group of available Publications.</dd> 

  <dt>OPDS Catalog Entry</dt>
  <dd>An Atom Entry that provides a representation of an available Publication and includes an Acquisition Link. They are serialized as OPDS Catalog Entry Documents.</dd> 

  <dt>OPDS Catalog Entry Document</dt>
  <dd>The formal name for the XML documents that are a refinement of an "atom:entry" from the Atom Syndication Format [<a href="https://tools.ietf.org/html/rfc4287">RFC4287</a>] that this specification defines.</dd>

  <dt>OPDS Catalog Feed Document</dt>
  <dd>The formal name for XML documents that are a refinement of an <code>atom:feed</code> from the Atom Syndication Format [<a href="https://tools.ietf.org/html/rfc4287">RFC4287</a>] that this specification defines.</dd>

  <dt>OPDS Catalog Root</dt>
  <dd>The Atom Feed that represents either the complete OPDS Catalog (a single Acquisition Feed) or the Atom Feed that starts a set of Navigation Feeds.</dd>

  <dt>Partial Catalog Entry</dt>
  <dd>An OPDS Catalog Entry that includes the minimal required metadata about the described Publication but no other metadata and links to the Complete Catalog Entry.</dd>

  <dt>Publication</dt>
  <dd>An electronic document, typically available as a single file. OPDS Catalogs are agnostic about the particular format of Publications.</dd>

  <dt>relation (or "relation of")</dt>
  <dd>Refers to the <code>rel</code> attribute value of an atom:link element.</dd>

  <dt>Representation</dt>
  <dd>An entity included with a request or response as defined in [<a href="https://tools.ietf.org/html/rfc2616">RFC2616</a>].</dd>

  <dt>Resource</dt>
  <dd>A network-accessible data object or service identified by an IRI, as defined in [<a href="https://tools.ietf.org/html/rfc2616">RFC2616</a>]. See [<a href="http://www.w3.org/TR/2004/REC-webarch-20041215">REC-webarch</a>] for further discussion on Resources.</dd>

  <dt>URI</dt>
  <dd>A Uniform Resource Identifier as defined in [<a href="https://tools.ietf.org/html/rfc3986">RFC3986</a>]. In this specification, the phrase "the URI of a document" is shorthand for "a URI which, when dereferenced, is expected to produce that document as a representation".</dd>
</dl>

### 1.3. Conformance Statements

The key words <em class="rfc">must</em>, <em class="rfc">must not</em>, <em class="rfc">required</em>, <em class="rfc">shall</em>, <em class="rfc">shall not</em>, <em class="rfc">should</em>, <em class="rfc">should not</em>, <em class="rfc">recommended</em>, <em class="rfc">may</em>, and <em class="rfc">optional</em> in this document are to be interpreted as described in [[RFC2119](https://tools.ietf.org/html/rfc2119)].

### 1.4. RELAX NG Schema

Some sections of this specification are illustrated with fragments of a non-normative RELAX NG Compact schema [[RNC](http://www.oasis-open.org/committees/relax-ng/compact-20021121.html)]. However, the text of this specification provides the definition of conformance. Complete schemas appear in [Appendix B](#appendix-b-relax-ng-compact-schema).


## 2. OPDS Catalog Feed Documents

OPDS Catalog Feed Documents are Atom [[RFC4287](https://tools.ietf.org/html/rfc4287)] Feeds. They serve two purposes:

- To create a browsable hierarchy of other OPDS Catalog Feed Documents and other Resources. These OPDS Catalog Feed Documents are referred to as Navigation Feeds.
- To collect a set of OPDS Catalog Entries. These OPDS Catalog Feed Documents are referred to as Acquisition Feeds.

While Navigation Feeds do provide a suggested hierarchy from the OPDS Catalog publisher, OPDS Catalog Feed Documents <em class="rfc">may</em> contain [other relations](#6-additional-link-relations) that allow for other methods of consumption and display. 

Every OPDS Catalog Feed Document <em class="rfc">must</em> either be an Acquisition Feed or a Navigation Feed. An Acquisition Feed can be identified by the presence of Acquisition Links in each Atom Entry.

### 2.1. OPDS Catalog Root

The OPDS Catalog Root is the top-level OPDS Catalog Feed Document. It is either a single Acquisition Feed in the simple case or the start of a set of Navigation Feeds. Every OPDS Catalog <em class="rfc">must</em> have one and only one OPDS Catalog Root.

External links to the OPDS Catalog Resource <em class="rfc">should</em> use the IRI of the OPDS Catalog Root.

Each OPDS Catalog Feed Document <em class="rfc">should</em> contain an `atom:link` element with a link relation of `start`, which references the OPDS Catalog Root Resource.

### 2.2. Navigation Feeds

A Navigation Feed is an OPDS Catalog Feed Document whose Atom Entries serve to create a suggested hierarchy for presentation and browsing. A Navigation Feed <em class="rfc">must not</em> contain OPDS Catalog Entries but instead contains Atom Entries that link to other Navigation or Acquisition Feeds or other Resources. 

Links to Navigation Feeds <em class="rfc">must</em> use the `type` attribute `application/atom+xml;profile=opds-catalog;kind=navigation`. 

OPDS Catalog providers <em class="rfc">should</em> choose the best relation for each Navigation Feed based on the relations in the section OPDS Catalog Relations. The relation `subsection` <em class="rfc">should</em> be used if no other relation is more appropriate.

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

### 2.3. Acquisition Feeds

An Acquisition Feed is an OPDS Catalog Feed Document that collects OPDS Catalog Entries into a single, ordered set. The simplest complete OPDS Catalog would be a single Acquisition Feed listing all of the available OPDS Catalog Entries from that provider. In more complex OPDS Catalogs, Acquisition Feeds are used to present and organize sets of related OPDS Catalog Entries for browsing and discovery by clients and aggregators.

Links to Acquisition Feeds <em class="rfc">must</em> use the `type` attribute `application/atom+xml;profile=opds-catalog;kind=acquisition`.

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

### 2.4. Listing Acquisition Feeds

OPDS Catalog Feed Documents, especially Acquisition Feeds, may contain large numbers of Atom Entries.

A client such as a web spider or web browser might be overwhelmed if the response to a GET contained every Atom Entry in an Acquisition Feed — in turn the server might also waste bandwidth and processing time on generating a response that cannot be handled. For this reason, servers MAY respond to Acquisition Feed GET requests with a paginated response: an OPDS Catalog Feed Document containing a partial list of the Acquisition Feed's member Atom Entries and a link to the next partial Acquisition Feed, if it exists, as defined in Section 3 of [[RFC5005](https://tools.ietf.org/html/rfc5005)].

OPDS Catalog providers <em class="rfc">should</em> use Partial Catalog Entries in all Acquisition Feeds except Complete Acquisition Feeds, which are intended for crawling and are referenced using the `http://opds-spec.org/crawlable` relation.

Clients <em class="rfc">must not</em> assume that an OPDS Catalog Entry returned in the Acquisition Feed is a full representation of an OPDS Catalog Entry Resource, as described in the Section Partial and Complete Entries.

### 2.5. Complete Acquisition Feeds

An OPDS Catalog provider <em class="rfc">may</em> provide a single, consolidated Acquisition Feed that includes the complete representation of every unique OPDS Catalog Entry Document in an OPDS Catalog in an atom:feed to facilitate crawling and aggregation. Complete Acquisition Feeds <em class="rfc">should not</em> be paginated unless they are extremely large.

This representation is called a Complete Acquisition Feed and each OPDS Catalog Entry <em class="rfc">must</em> be ordered by atom:updated, with the most recently updated Atom Entries coming first in the document order.

If available, each OPDS Catalog Feed Document in the OPDS Catalog <em class="rfc">should</em> contain an atom:link element with a relation of `http://opds-spec.org/crawlable` that references the Complete Acquisition Feed Resource.

A Complete Acquisition Feed <em class="rfc">must</em> include a `fh:complete` element from [[RFC5005](https://tools.ietf.org/html/rfc5005)] unless pagination is required. See Section 2 of [[RFC5005](https://tools.ietf.org/html/rfc5005)] for the specification of the `fh:complete` element.

OPDS Catalog providers <em class="rfc">should</em> use a compressed Content-Encoding when transmitting Complete Acquisition Feeds over HTTP. See Section 14.11 of [[RFC2616](https://tools.ietf.org/html/rfc2616)] for more on compression.

OPDS Catalog providers <em class="rfc">must</em> include Complete Catalog Entries when serializing a Complete Acquisition Feed.

### 2.6. Element Definitions

#### The `atom:feed` Element

The `atom:feed` element is the document (i.e., top-level) element of an OPDS Catalog Feed Document, acting as a container for metadata, hierarchy, and Publications associated with the OPDS Catalog. Its element children consist of metadata elements followed by zero or more `atom:entry` child elements.

The following child elements are refined by this specification:

- OPDS Catalog Feed Documents <em class="rfc">should</em> contain one `atom:link` element with a `rel` attribute value of `self`. This is the preferred URI for retrieving the `atom:feed` representing this OPDS Catalog Feed Document.

## 3. Search

An OPDS Catalog <em class="rfc">may</em> provide a search facility through an [[OpenSearch](http://www.opensearch.org/Specifications/OpenSearch/1.1)] description document. 

Links to [[OpenSearch](http://www.opensearch.org/Specifications/OpenSearch/1.1)] description documents <em class="rfc">must</em> use the `search` relation value and the `application/opensearchdescription+xml` media type as defined in the "Autodiscovery" section of the [[OpenSearch](http://www.opensearch.org/Specifications/OpenSearch/1.1)] specification.

```xml
<link rel="search"
      href="search.xml"
      type="application/opensearchdescription+xml"/>
```

In an [[OpenSearch](http://www.opensearch.org/Specifications/OpenSearch/1.1)] description document, the search interface <em class="rfc">must</em> use the media type associated to OPDS Catalogs:

```xml
<Url type="application/atom+xml;profile=opds-catalog;kind=acquisition"
     template="http://example.com/search?q={searchTerms}" />
```

An OPDS Catalog <em class="rfc">may</em> also provide more advanced possibilities for its search endpoint, using one or more fully qualified parameters from the Atom namespace such as:

- atom:author
- atom:contributor
- atom:title


OPDS Catalog Feed Documents <em class="rfc">may</em> include elements from the [[OpenSearch](http://www.opensearch.org/Specifications/OpenSearch/1.1)] namespace such as `opensearch:totalResults` or `opensearch:itemsPerPage` in [[OpenSearch](http://www.opensearch.org/Specifications/OpenSearch/1.1)] responses.


**Open Search Description Document Example**

In order to provide a search endpoint that supports both basic (keyword based) and advanced search, an OPDS Catalog could provide the following template in its [[OpenSearch](http://www.opensearch.org/Specifications/OpenSearch/1.1)] Description document:

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

An Acquisition Feed <em class="rfc">may</em> offer multiple links to reorder the Publications listed in the feed or limit them to a subset. This specification defines one new relation to identify such links as Facets:

- `http://opds-spec.org/facet`: An Acquisition Feed with a subset or an alternate order of the Publications listed.

Links using this relation <em class="rfc">must</em> only appear in Acquisition Feeds.

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

Facets <em class="rfc">may</em> be grouped together by the OPDS Catalog provider using an `opds:facetGroup` attribute. The value of this attribute is the name of the group.

A Facet <em class="rfc">must not</em> appear in more than a single group.

#### The `opds:activeFacet` Attribute

A Facet is considered active, if the attribute associated to the Facet is already being used to filter Publications in the current Acquisition Feed.

The OPDS Catalog provider <em class="rfc">should</em> indicate that a Facet is active using the `opds:activeFacet` attribute set to `true`.

If the Facet is not active, the `opds:activeFacet` attribute <em class="rfc">should not</em> appear in the link.

In a group of Facets, an OPDS Catalog provider <em class="rfc">must not</em> mark more than one Facet as active.

#### The `thr:count` Attribute

The OPDS Catalog provider <em class="rfc">may</em> provide an additional hint about the number of items expected in the Acquisition Feed, if an OPDS client follows a link.

The `thr:count` attribute, extracted from [[RFC4685](https://tools.ietf.org/html/rfc4685)] <em class="rfc">may</em> be added to a Facet in order to provide this information.


## 5. OPDS Catalog Entry Documents

OPDS Catalog Entry Documents are Atom [[RFC4287](https://tools.ietf.org/html/rfc4287)] Entry documents that provide a complete representation of the metadata and data associated with an available Publication.

Each OPDS Catalog Entry Document <em class="rfc">must</em> include at least one Acquisition Link.

### 5.1. Metadata

#### 5.1.1. Relationship Between Atom and Dublin Core Metadata

OPDS Catalog providers are encouraged to include metadata from the Dublin Core Metadata Initiative (DCMI) Metadata Terms [[DCTERMS](http://dublincore.org/documents/dcmi-terms/)] whenever appropriate. However, these elements are not required. Some of the elements defined in [[DCTERMS](http://dublincore.org/documents/dcmi-terms/)] overlap in meaning with similar elements defined by Atom in [[RFC4287](https://tools.ietf.org/html/rfc4287)]. In general, OPDS Catalog Entries prefer the Atom elements over the [[DCTERMS](http://dublincore.org/documents/dcmi-terms/)] elements and the following rules should be used by OPDS Catalog providers when choosing elements:

- OPDS Catalog Entries <em class="rfc">must</em> be identified by an `atom:id`. One or more `dc:identifier` elements <em class="rfc">should</em> be used to identify the represented Publication, if appropriate metadata is available, and MUST NOT identify the OPDS Catalog Entry.
- OPDS Catalog Entries <em class="rfc">must</em> include an `atom:updated` element indicating when the OPDS Catalog Entry was last updated. A `dc:issued` element <em class="rfc">should</em> be used to indicate the first publication date of the Publication and <em class="rfc">must not</em> represent any date related to the OPDS Catalog Entry.
- OPDS Catalog Entries <em class="rfc">must</em> include an `atom:title` representing the Publication's title and <em class="rfc">must not</em> use `dc:title`.
- OPDS Catalog Entries <em class="rfc">should</em> use `atom:author` to represent the Publication's creators and <em class="rfc">should not</em> use `dc:creator`.
- OPDS Catalog Entries <em class="rfc">should</em> use `atom:category` to represent the Publication's category, keywords, key phrases, or classification codes and <em class="rfc">should not</em> use `dc:subject`.
- OPDS Catalog Entries <em class="rfc">should</em> use `atom:rights` to represent rights held in and over the Publication and <em class="rfc">should not</em> use `dc:rights`.
- OPDS Catalog Entries <em class="rfc">should</em> use `atom:summary` and/or `atom:content` to describe the Publication <em class="rfc">should not</em> use `dc:description` or `dc:abstract`.
- OPDS Catalog Entries <em class="rfc">may</em> use `atom:contributor` to represent contributors to the Publication beside its creators.
- OPDS Catalog Entries <em class="rfc">may</em> use `atom:published` to indicate when the OPDS Catalog Entry was first accessible.

All metadata elements required by Atom are required in OPDS Catalog Entries and Feeds.

Following Atom [[RFC4287](https://tools.ietf.org/html/rfc4287)] Section 4.2.6, the content of an `atom:id` identifying an OPDS Catalog Entry <em class="rfc">must not</em> change when the OPDS Catalog Entry is "relocated, migrated, syndicated, republished, exported, or imported" and "<em class="rfc">must</em> be created in a way that assures uniqueness."

#### 5.1.2. Partial and Complete Catalog Entries

An OPDS Catalog Entry may be represented as either a Partial or Complete Catalog Entry. Partial Catalog Entries include only critical metadata elements to reduce the size of OPDS Catalog documents for bandwidth- and resource-limited clients and link to their alternate representation as a Complete Catalog Entries, which include all metadata elements.

A Partial Catalog Entry <em class="rfc">must</em> include an `alternate` link relation referencing the Complete Catalog Entry Resource and that `atom:link` <em class="rfc">must</em> use the `type` attribute `application/atom+xml;type=entry;profile=opds-catalog`.

Any OPDS Catalog Entry without a link to a Complete Catalog Entry <em class="rfc">must</em> include all metadata elements.

Partial Catalog Entries <em class="rfc">should</em> include the following metadata elements, if available:

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

OPDS Catalog Entries use `atom:content` and `atom:summary` elements to describe a Publication. This is a distinction from Atom [[RFC4287](https://tools.ietf.org/html/rfc4287)], which uses `atom:content` for the "content of the entry." Instead, OPDS Catalog Entries reference an electronic document with the Publication's content using Acquisition Links. OPDS Catalog Entries <em class="rfc">should</em> include either `atom:summary` or `atom:content` elements or both to provide a description, summary, abstract, or excerpt of the Publication.

An `atom:summary` element in an OPDS Catalog Entry <em class="rfc">must</em> use a `type` attribute of `text` and the content <em class="rfc">must not</em> contain child elements. The content of an `atom:summary` element <em class="rfc">should not</em> duplicate the content of `atom:title` or `atom:content`. Such text is intended to be presented to humans in a readable fashion. The restriction of the content of `atom:summary` and the `type` attribute is a restriction of Atom [[RFC4287](https://tools.ietf.org/html/rfc4287)].

An `atom:content` element in an OPDS Catalog Entry either contains or links to a description, summary, abstract, or excerpt of the Publication. The behavior for `atomOutOfLineContent` and the `src` attribute are defined in Section 4.1.3 of [[RFC4287](https://tools.ietf.org/html/rfc4287)].

If an OPDS Catalog Entry includes both `atom:content` and `atom:summary`, the `atom:content` <em class="rfc">should not</em> be included in the Partial Catalog Entry. Both elements <em class="rfc">should</em> be included in the Complete Catalog Entry.

```
atomSummary = 
  element atom:summary {
  atomCommonAttributes,
  attribute type { "text" }?,
    text
  }
```

### 5.2. Catalog Entry Relations

OPDS Catalog Entry Documents <em class="rfc">should</em> include links to related Resources. This specification defines new relations for linking from OPDS Catalog Entry Documents. They are defined in the Sections Acquisition Relations and Artwork Relations.

OPDS Catalog providers are encouraged to use relations from [[RFC5988](https://tools.ietf.org/html/rfc5988)] inside OPDS Catalog Entry Documents where appropriate.

#### 5.2.1. Acquisition Relations

Acquisition relations describe the extent of the content referred to by an Acquisition Link or the manner in which that Resource may be acquired. This specification defines six Acquisition Relations, which all begin with "http://opds-spec.org/acquisition":

- `http://opds-spec.org/acquisition`: A generic relation that indicates that the complete representation of the content Resource may be retrieved.
- `http://opds-spec.org/acquisition/open-access`: Indicates that the complete representation of the Resource can be retrieved without any requirement, which includes payment and registration.
- `http://opds-spec.org/acquisition/borrow`: Indicates that the complete representation of the content Resource may be retrieved as part of a lending transaction.
- `http://opds-spec.org/acquisition/buy`: Indicates that the complete representation of the content Resource may be retrieved as part of a purchase.
- `http://opds-spec.org/acquisition/sample` or `preview`: Indicates that a subset of the content Resource may be retrieved.
- `http://opds-spec.org/acquisition/subscribe`: Indicates that the complete representation of the content Resource may be retrieved as part of a subscription.

#### 5.2.2. Artwork Relations

OPDS Catalog Entries <em class="rfc">may</em> include `atom:link` elements to images that provide a visual representation of the Publication. For many Publications, these images will be the Publication's artwork. This specification defines two Artwork Relations, which begin with `http://opds-spec.org/image`:

- `http://opds-spec.org/image`: a graphical Resource associated to the OPDS Catalog Entry
- `http://opds-spec.org/image/thumbnail`: a reduced-size version of a graphical Resource associated to the OPS Catalog Entry

Image resources related by `http://opds-spec.org/image/thumbnail` <em class="rfc">should</em> be suitable for presentation at a small size.

The `atom:link` elements with these relations <em class="rfc">should</em> include at least one link with a type attribute of `image/gif`, `image/jpeg`, or `image/png` and the image Resources <em class="rfc">must</em> be in GIF, JPEG, or PNG format.

Additional `atom:link` elements <em class="rfc">may</em> also include resources using a vector-based format.

While either image Resource is optional and may be included independently, OPDS Catalog providers are encouraged to provide both these relations and Resources whenever possible.

Some OPDS Catalog providers MAY choose to provide `http://opds-spec.org/image` thumbnail image Resources using the `data` URL scheme from [[RFC2397](https://tools.ietf.org/html/rfc2397)].

### 5.3. Acquiring Publications

The goal of OPDS Catalogs is to make Publications both discoverable and straightforward to acquire on a range of devices and platforms. To support that goal, this specification strives to provide a framework for describing how a Publication may be acquired while not attempting to constrain this very complex topic. Commonly-used acquisition scenarios may be specified in an update to this specification.

All Acquisition Links <em class="rfc">must</em> include a `type` attribute that advises clients on the media type of the representation that is expected to be returned when the value of the href attribute is dereferenced. As with Atom, the value of the type attribute <em class="rfc">must</em> conform to the syntax of a MIME media type [[MIMEREG](https://tools.ietf.org/html/rfc4288)].

Publications in a format using Digital Rights Management <em class="rfc">should</em> use a different value for the type attribute of the Acquisition Link than the same format without Digital Rights Management.

OPDS Catalog clients may only support a subset of all possible Publication media types. These clients will need to filter both the type attribute of Acquisition Links.

OPDS Catalogs may only provide certain Publications through an Indirect Acquisition, either through a container or a different Acquisition workflow. In such cases, it is up to the clients to filter these publications based on both the `opds:indirectAcquisition` and `atom:link` type attributes.

**Examples**

The simplest case is a Publication available in one format:

```
<link rel="http://opds-spec.org/acquisition" 
      type="video/mp4v-es" 
      href="/content/free/4561.mp4"/>
```

If the Publication was available in multiple formats as unique Resources, they would simply be listed:

```
<link rel="http://opds-spec.org/acquisition/borrow" 
      href="/content/borrow/4561.mobi"
      type="application/x-mobipocket-ebook" />
 
<link rel="http://opds-spec.org/acquisition/borrow" 
      href="/content/borrow/4561.epub"
      type="application/epub+zip" />
```      

If the Publication requires payment, at least one `opds:price` element is required:

```
<link rel="http://opds-spec.org/acquisition/buy"
      href="/product/song1.mp3"
      type="audio/mpeg">
  <opds:price currencycode="USD">1.99</opds:price> 
</link>
```

If the same Publication requires a payment through an HTML page, then an `opds:indirectAcquisition` element is required to describe the content type of the final Publication Representation:

```
<link rel="http://opds-spec.org/acquisition/buy"
      href="/product/1"
      type="text/html">
  <opds:price currencycode="USD">1.99</opds:price>
  <opds:indirectAcquisition type="audio/mpeg" />
</link>
```

Multiple `opds:indirectAcquisition` elements can also be used as child elements of an Acquisition Link or another `opds:indirectAcquisition` when this is necessary (a bundle would be a good example):

```
<link type="text/html" 
      rel="http://opds-spec.org/acquisition/buy" 
      href="/item/1111/buy/">
  <opds:price currencycode="EUR">10.99</opds:price>
  <opds:indirectAcquisition type="application/zip">
    <opds:indirectAcquisition type="application/epub+zip" />
    <opds:indirectAcquisition type="application/pdf" />
    <opds:indirectAcquisition type="application/x-mobipocket-ebook" />
  </opds:indirectAcquisition>
</link>
```

### 5.4. Element Definitions

#### The `atom:entry` Element

The `atom:entry` element is the document (i.e., top-level) element of an OPDS Catalog Entry Document, acting as a container for metadata and data associated with an available Publication.

The following child elements are refined by this specification:

- OPDS Catalog Entry Documents <em class="rfc">must</em> contain at least one Acquisition Link, an atom:link element with a rel attribute that begins with `http://opds-spec.org/acquisition`.


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

- `atom:link` elements with a rel attribute value set to `http://opds-spec.org/acquisition/buy` <em class="rfc">must</em> contain at least one `opds:price` element.
- `atom:link` elements with a rel attribute set to `http://opds-spec.org/acquisition/borrow`, `http://opds-spec.org/acquisition/subscribe`, or `http://opds-spec.org/acquisition/sample` <em class="rfc">may</em> contain one or more `opds:price` elements.

#### The `opds:price` Element

The `opds:price` element represents the acquisition price in a particular currency of an individual Publication in a particular format from a particular provider. This element can appear as a child of the `atom:link` element (in OPDS Catalog Entry Documents).

The content of `opds:price` is text describing a currency value. A currency sign <em class="rfc">must not</em> be included.

```
opdsPrice = 
  element opds:price {
    atomCommonAttributes,
    attribute currencycode { opdsPriceCurrencyCode },
    xsd:decimal { minInclusive="0.0" } 
  }
```
      
In the `opds:price` element, the value of the `currencycode` attribute <em class="rfc">must</em> be an active code from [[ISO4217](http://www.iso.org/iso/en/prods-services/popstds/currencycodeslist.html)] representing a currency. It defines the currency used for the content of the `opds:price` element.

#### The `opds:indirectAcquisition` Element

In some cases, the OPDS Catalog provider <em class="rfc">may</em> require the client to acquire an intermediate Resource before acquiring the final Publication. This can be the case for example with containers (archive formats, multimedia containers for various formats) or when a payment is required (need to go through a series of HTML pages to handle the transaction).

The `opds:indirectAcquisition` element represents these secondary media types [[MIMEREG](https://tools.ietf.org/html/rfc4288)] that the client can expect to acquire if they follow the Acquisition Link.

These `opds:indirectAcquisition` elements <em class="rfc">may</em> be arbitrarily nested to represent several levels of indirection.

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

OPDS Catalog Feed Documents <em class="rfc">should</em> include links to other available Acquisition and Navigation Feeds and other related Resources to encourage independent navigation.

These relations <em class="rfc">may</em> be used by clients to provide additional or alternative navigation, both in a Navigation Feed or an Acquisition Feed.

The following relations are derived from [[RFC5988](https://tools.ietf.org/html/rfc5988)], with some clarification:

- `start`: The OPDS Catalog Root.
- `subsection`: An OPDS Feed not better described by a more specific relation.

When creating an OPDS Catalog with Navigation and Acquisition Feeds, OPDS Catalog providers are encouraged to use the relations defined in this specification and [[RFC5988](https://tools.ietf.org/html/rfc5988)]. If no appropriate relation is found, the Feeds <em class="rfc">should</em> use a descriptive `atom:title` element and the `atom:link` elements <em class="rfc">should</em> use a descriptive `title` attribute.

### 6.1. Relations for Previously Acquired Content

An OPDS catalog <em class="rfc">may</em> track content that was previously acquired by the user. This specification defines two new relations to identify Acquisition Feeds listing such resources:

- `http://opds-spec.org/shelf`: A Resource that includes a user's existing set of Acquired Content, which <em class="rfc">may</em> be represented as an OPDS Catalog.
- `http://opds-spec.org/subscriptions`: A Resource that includes a user's set of subscriptions, which <em class="rfc">may</em> be represented as an OPDS Catalog.

An OPDS client <em class="rfc">may</em> use these links to automatically sync content to the user's local shelf or check for periodical content updates.

### 6.2. Sorting Relations

This specification defines two new relations, which begin with `http://opds-spec.org/sort`, to describe how an Acquisition Feed is sorted. These relations SHOULD be used when creating Navigation Feeds and the OPDS Catalog Root, if applicable.

- `http://opds-spec.org/sort/new`: An Acquisition Feed with newly released OPDS Catalog Entries. These Acquisition Feeds typically contain a subset of the OPDS Catalog Entries in an OPDS Catalog based on the publication date of the Publication.
- `http://opds-spec.org/sort/popular`: An Acquisition Feed with popular OPDS Catalog Entries. These Acquisition Feeds typically contain a subset of the OPDS Catalog Entries in an OPDS Catalog based on a numerical ranking criteria.

Acquisition Feeds using the `http://opds-spec.org/sort/new` relation <em class="rfc">should</em> be ordered with the most recent items first. 

Acquisition Feeds using the `http://opds-spec.org/sort/popular` relation <em class="rfc">should</em> be ordered with the most popular items first.

### 6.3. Featured Relation

This specification also defines a relation to describe an Acquisition Feed of featured items. This relation <em class="rfc">should</em> be used when creating Navigation Feeds and the OPDS Catalog Root, if applicable.

- `http://opds-spec.org/featured`: An Acquisition Feed with featured OPDS Catalog Entries. These Acquisition Feeds typically contain a subset of the OPDS Catalog Entries in an OPDS Catalog that have been selected for promotion by the OPDS Catalog provider. No order is implied.

### 6.4. Recommendations

This specification also defines a relation to describe an Acquisition Feed of recommended items. Since recommendations can be customized for the user, such feed may require some kind of prior authentication. This relation <em class="rfc">should</em> be used when creating Navigation Feeds and the OPDS Catalog Root, if applicable.

- `http://opds-spec.org/recommended`: An Acquisition Feed with recommended OPDS Catalog Entries. These Acquisition Feeds typically contain a subset of the OPDS Catalog Entries in an OPDS Catalog that have been selected specifically for the user. 

Acquisition Feeds using the `http://opds-spec.org/recommended` relation <em class="rfc">should</em> be ordered with the most recommended items first.

## 7. Other Considerations

### 7. Discovering OPDS Catalogs

OPDS Catalogs may be referenced in HTML/XHTML pages, HTTP headers, or using other techniques. These links may reference both OPDS Catalog Entries or Feeds. 

Links to OPDS Catalog Entry Document Resources <em class="rfc">must</em> use a type attribute of `application/atom+xml;type=entry;profile=opds-catalog`. 

Links to OPDS Catalog Feed Document Resources <em class="rfc">must</em> use a type attribute of `application/atom+xml;profile=opds-catalog`.

The most common mechanism for encouraging the auto-discovery of OPDS Catalogs is to link from an HTML document to the OPDS Catalog Root Resource, using the auto-discovery pattern popularized by the syndicated feed community [[AUTODISCOVERY](http://www.rssboard.org/rss-autodiscovery)].

Multiple links to OPDS Catalog Resources <em class="rfc">may</em> be expressed in a single HTML document.

An example of two links inside an HTML page about the same Publication:

```xml
<link rel="related"   
      href="/opds-catalogs/root" 
      type="application/atom+xml;profile=opds-catalog"  
      title="Example OPDS Catalog" /> 
 
<link rel="alternate" 
      href="/entry/1"
      type="application/atom+xml;type=entry;profile=opds-catalog" 
      title="Example OPDS Entry" />
```

Auto-discovery links <em class="rfc">may</em> also be expressed using HTTP headers as defined in [[RFC5988](https://tools.ietf.org/html/rfc5988)].

### 7. Aggregating OPDS Catalogs

OPDS Catalogs <em class="rfc">may</em> be aggregated using the same techniques as Atom Feeds. 

Aggregators <em class="rfc">should</em> use the atom:source element from Section 4.2.11 of [[RFC4287](https://tools.ietf.org/html/rfc4287)] to include information about the original OPDS Catalog.

### 7.1 Media Type Considerations

#### 7.1.1. The Atom Format Type Parameter

The Atom Publishing Protocol [RFC5023] defines the Atom Format Type Parameter.
Publishers of OPDS Catalogs <em class="rfc">should</em> use the `type` parameter to help clients distinguish between relations to 
OPDS Catalog Entries and OPDS Catalog Feeds.

#### 7.1.2. The OPDS Catalog Profile Parameter

Relations to OPDS Catalog Feed Document and OPDS Catalog Entry Document Resources <em class="rfc">must</em> use a `profile` parameter 
following Section 4.3 of [[RFC4288](https://tools.ietf.org/html/rfc4288)] with the value `opds-catalog`. 
This profile parameter provides clients with an advisory hint that the Resource should be a component of an OPDS Catalog.

The complete media type for a relation to an OPDS Catalog Entry Document Resource <em class="rfc">must</em> be:

`application/atom+xml;type=entry;profile=opds-catalog`

#### 7.1.3. The OPDS Kind Parameter

In addition to the new "profile" parameter, this specification also introduces a new `kind` parameter following Section 4.3 
of [[RFC4288](https://tools.ietf.org/html/rfc4288)] with the value `acquisition` or `navigation`. 
This `kind` parameter provides clients with an advisory hint whether the Resource should be an Acquisition Feed or a 
Navigation Feed.

This value is intended to make it easier for OPDS clients to display basic contextual information about the feed without  requiring that those clients dereference, parse, and analyze linked resources. That said, the client <em class="rfc">must not</em> assume this parameter to provide completely accurate information about the nature of the feed. 

The complete media type for a relation to an Acquisition Feed <em class="rfc">must</em> be:

`application/atom+xml;profile=opds-catalog;kind=acquisition`

The complete media type for a relation to a Navigation Feed <em class="rfc">must</em> be:

`application/atom+xml;profile=opds-catalog;kind=navigation`

### 7.2. Security Considerations

OPDS Catalogs are Atom documents delivered over HTTP and thus subject to the security considerations found in Section 15 of [[RFC2616](https://tools.ietf.org/html/rfc2616)] and Section 5 of [[RFC4287](https://tools.ietf.org/html/rfc4287)].

#### 7.2.1. Securing a Catalog

OPDS Catalogs are delivered over HTTP. Authentication requirements for HTTP are covered in Section 11 of [[RFC2616](https://tools.ietf.org/html/rfc2616)].

The type of authentication required for any OPDS Catalog is a decision to be made by the OPDS Catalog provider. OPDS Catalog clients are likely to face authentication schemes that vary across OPDS Catalogs. At a minimum, client and server implementations <em class="rfc">must</em> be capable of being configured to use HTTP Basic Authentication [[RFC2617](https://tools.ietf.org/html/rfc2617)] in conjunction with a connection made with TLS 1.3 [[RFC8446](https://tools.ietf.org/html/rfc8446)] or a subsequent standards-track version of TLS. It is <em class="rfc">recommended</em> that OPDS Catalog clients be implemented in such a way that new authentication schemes can be deployed.

Because this protocol uses HTTP response status codes as the primary means of reporting the result of a request, OPDS Catalog providers are advised to respond to unauthorized or unauthenticated requests using an appropriate 4xx HTTP response code (e.g., 401 "Unauthorized" or 403 "Forbidden") in accordance with [[RFC2617](https://tools.ietf.org/html/rfc2617)].

#### 7.2.2 Linked Resources

OPDS Catalogs can contain XML External Entities as defined in Section 4.2.2 of [[REC-xml](http://www.w3.org/TR/2006/REC-xml-20060816)]. OPDS Catalog implementations are not required to load external entities. External entities are subject to the same security concerns as any network operation and can alter the semantics of an OPDS Catalog Feed Document or OPDS Catalog Entry Document. The same issues exist for Resources linked to by elements such as `atom:link` and `atom:content`.

#### 7.2.3 URIs and IRIs

OPDS Catalog implementations handle URIs and IRIs. See Section 7 of [[RFC3986](https://tools.ietf.org/html/rfc3986)] and Section 8 of [[RFC3987](https://tools.ietf.org/html/rfc3987)] for security considerations related to their handling and use.

#### 7.2.4 Code Injection and Cross Site Scripting

OPDS Catalogs can contain a broad range of content types including code that might be executable in some contexts. Malicious publishers could attempt to attack servers or other clients by injecting code into OPDS Catalog Feed Documents or OPDS Catalog Entry Documents or Media Resources.

Server implementations are strongly encouraged to verify that external content is safe prior to aggregating, processing, or publishing it. In the case of HTML, experience indicates that verification based on a white list of acceptable content is more effective than a black list of forbidden content.

Additional information about XHTML and HTML content safety can be found in Section 8.1 of [[RFC4287](https://tools.ietf.org/html/rfc4287)].

### 7.3. Bandwidth and Processing Considerations

Many OPDS Catalog clients operate in mobile environments, which may impose strict limitations on bandwidth and processing resources. OPDS Catalog publishers are strongly encouraged to publish their OPDS Catalogs using compression and caching techniques and the partial feeds described in the Section Listing Acquisition Feeds. Implementers are encouraged to investigate and use alternative mechanisms regarded as equivalently good or better at the time of deployment. See [[CACHING](http://www.mnot.net/cache_docs/)] for more on caching techniques.

## 8. References

### 8.1. Normative References

- [[DCTERMS](http://dublincore.org/documents/dcmi-terms/)] DCMI Usage Board, "DCMI Metadata Terms", January 2008.
- [[ISO4217](http://www.iso.org/iso/en/prods-services/popstds/currencycodeslist.html)] "ISO 4217 currency and funds name and code elements", International Standard ISO 4217.
- [[MIMEREG](https://tools.ietf.org/html/rfc4288)] Freed, N. and J. Klensin, "Media Type Specifications and Registration Procedures", BCP 13, RFC 4288, December 2005.
- [[OpenSearch](http://www.opensearch.org/Specifications/OpenSearch/1.1)] Clinton D., "Open Search 1.1 Draft 4".
- [[REC-xml](http://www.w3.org/TR/2006/REC-xml-20060816)] Yergeau, F., Paoli, J., Bray, T., Sperberg-McQueen?, C., and E. Maler, "Extensible Markup Language (XML) 1.0 (Fourth Edition)", World Wide Web Consortium Recommendation REC-xml-20060816, August 2006.
- [[RFC2119](https://tools.ietf.org/html/rfc2119)] Bradner, S., "Key words for use in RFCs to Indicate Requirement Levels", BCP 14, RFC 2119, March 1997.
- [[RFC2397](https://tools.ietf.org/html/rfc2397)] Masinter, L., "The 'data' URL scheme", RFC 2397, August 1998.
- [[RFC2616](https://tools.ietf.org/html/rfc2616)] Fielding, R., Gettys, J., Mogul, J., Frystyk, H., Masinter, L., Leach, P., and T. Berners-Lee, "Hypertext Transfer Protocol — HTTP/1.1", RFC 2616, June 1999.
- [[RFC2617](https://tools.ietf.org/html/rfc2617)] Franks, J., Hallam-Baker, P., Hostetler, J., Lawrence, S., Leach, P., Luotonen, A., and L. Stewart, "HTTP Authentication: Basic and Digest Access Authentication", RFC 2617, June 1999.
- [[RFC3986](https://tools.ietf.org/html/rfc3986)] Berners-Lee, T., Fielding, R., and L. Masinter, "Uniform Resource Identifier (URI): Generic Syntax", STD 66, RFC 3986, January 2005.
- [[RFC3987](https://tools.ietf.org/html/rfc3987)] Duerst, M. and M. Suignard, "Internationalized Resource Identifiers (IRIs)", RFC 3987, January 2005.
- [[RFC4287](https://tools.ietf.org/html/rfc4287)] Nottingham, M. and R. Sayre, "The Atom Syndication Format", RFC 4287, December 2005.
- [[RFC4288](https://tools.ietf.org/html/rfc4288)] Freed, N. and J. Klensin, "Media Type Specifications and Registration Procedures", RFC 4288, December 2005.
- [[RFC4685](https://tools.ietf.org/html/rfc4685)] Snell, J., "Atom Threading Extensions", RFC 4685, September 2006.
- [[RFC5005](https://tools.ietf.org/html/rfc5005)] Nottingham, M., "Feed Paging and Archiving", RFC 5005, September 2007.
- [[RFC5988](https://tools.ietf.org/html/rfc5988)] Nottingham, M., "Web Linking", RFC5988, October 2010.
- [[RFC8446](https://tools.ietf.org/html/rfc2246)] Rescorla, E., "The Transport Layer Security (TLS) Protocol Version 1.3", RFC 8446, August 2018.

### 8.2. Informative References

- [[AUTODISCOVERY](http://www.rssboard.org/rss-autodiscovery)] Cadenhead, R., Holderness, J., Morin, R., "RSS Autodiscovery", November 2006.
- [[CACHING](http://www.mnot.net/cache_docs/)] Nottingham, M., "Caching Tutorial", 1998.
- [[REC-webarch](http://www.w3.org/TR/2004/REC-webarch-20041215)] Walsh, N. and I. Jacobs, "Architecture of the World Wide Web, Volume One", W3C REC REC-webarch-20041215, December 2004.
- [[RNC](http://www.oasis-open.org/committees/relax-ng/compact-20021121.html)] Clark, J., "RELAX NG Compact Syntax", December 2001.

## Appendix A. Contributors

The content and concepts within this specification are a product of the OPDS Community.

## Appendix B. RELAX NG Compact Schema

This appendix is informative.

The RELAX NG schema explicitly excludes elements in the OPDS Catalog namespace that
are not defined in this revision of the specification. Requirements for Atom Protocol
processors encountering such markup are given in Sections 6.2 and 6.3 of
[[RFC4287](https://tools.ietf.org/html/rfc4287)].

The Schema for OPDS Catalog Feed & Entry Documents:

```
# -*- rnc -*- 
# RELAX NG Compact Syntax Grammar for OPDS Catalog Feed & Entry Documents
# Version 2010-08-18
namespace atom = "http://www.w3.org/2005/Atom"
namespace opds = "http://opds-spec.org/2010/catalog"
namespace local = ""
   
# The OPDS Catalog spec extends Atom (RFC4287), and the additions require some
# patterns not used in the Atom schema. The first is atomUriExceptOPDS, which
# is used to describe an atomLink whose rel value is an atomNCName (no-colon
# name) or any URI other than these from OPDS Catalogs. In these cases, no
# opds:price element should appear.
atomUriExceptOPDS = string - ( string "http://opds-spec.org/acquisition/buy"
                             | string "http://opds-spec.org/acquisition/borrow"
                             | string "http://opds-spec.org/acquisition/subscribe"
                             | string "http://opds-spec.org/acquisition/sample" )
   
# Next is OPDSUrisExceptBuy, which is used to describe an atomLink whose
# rel value is from OPDS Catalogs but is not ".../acquisition/buy". In such
# cases, an opds:price element is optional.
OPDSUrisExceptBuy = string "http://opds-spec.org/acquisition/borrow"
                  | string "http://opds-spec.org/acquisition/subscribe"
                  | string "http://opds-spec.org/acquisition/sample"
 
# To simplify OPDS Catalog validation, we do not use Schematron to assert that
# any atom:link with a rel value of ".../acquisition/buy" must be accompanied
# by one or more opds:price elements.
# Instead we rely on Relax NG to describe one of three situations:
# - the rel value is ".../acquisition/buy" and at least one opds:price element
#   is required
# - the rel value is ".../acquisition/borrow" or ".../acquisition/subscribe" or
#   ".../acquisition/sample", in case opds:price elements may be
#   included; or
# - the value of the rel attribute is any other URI or an Atom-defined no-colon
#   name, and no opds:price element is permitted
 
# Note that this OPDS Catalog schema includes atom.rnc, so that schema must be
# present for validation.
# 
# Note also that atom.rnc defines atomUri as text and not as xsd:anyURI, and so
# wherever the Atom spec requires an IRI, the schema will not check the value
# against any URI pattern or logic. The OPDS Catalog schema overrides atom.rnc
# to provide a relatively accurate test. With the approval of XSD 1.1, the
# schema definition should change to xsd:anyURI to match what the spec text
# says.
include "atom.rnc" {
 
undefinedAttribute =
  attribute * - (xml:base | xml:lang | local:*| opds:* ) { text }
 
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
    
# Here we override Atom to account for HTML abuse in the summary element,
# restricting it in OPDS Catalog to text:
  atomSummary = 
    element atom:summary {
      atomCommonAttributes,
      attribute type { "text" }?,
      text
    }
  } 
   
  anyOPDSForeignElement =
    element * - ( atom:* | opds:* ) {
      ( attribute * { text }
      | text
      | anyElement )*
    } 
   
# An opds:indirectAcquisition should use strictly MIME media type for
#its type attribute
  opdsIndirectAcquisition = 
    element opds:indirectAcquisition {
      atomCommonAttributes,
      attribute type { atomMediaType },
      (  anyOPDSForeignElement | 
        opdsIndirectAcquisition) *
    }
      
# An opds:price element should not contain a currency symbol; it is
# restricted to non-negative decimal numbers.
  opdsPrice = 
    element opds:price {
      atomCommonAttributes,
      attribute currencycode { opdsPriceCurrencyCode },
      xsd:decimal { minInclusive="0.0" } 
    }
   
   
# Instead of allowing every possible 3-letter combination as a currency
# code, here the permissible codes (as identified in ISO4217 as of
# 2018-08-16) are enumerated. Code for metals and 3-digit equivalents are
# excluded from this list.
  opdsPriceCurrencyCode =  (   
    "AED" | "AFN" | "ALL" | "AMD" | "ANG" | "AOA" | "ARS" | "AUD" | "AWG" | "AZN" | "BAM" | "BBD" | "BDT" | 
    "BGN" | "BHD" | "BIF" | "BMD" | "BND" | "BOB" | "BOV" | "BRL" | "BSD" | "BTN" | "BWP" | "BYN" | "BZD" | 
    "CAD" | "CDF" | "CHE" | "CHF" | "CHW" | "CLF" | "CLP" | "CNY" | "COP" | "COU" | "CRC" | "CUC" | "CUP" | 
    "CVE" | "CZK" | "DJF" | "DKK" | "DOP" | "DZD" | "EGP" | "ERN" | "ETB" | "EUR" | "FJD" | "FKP" | "GBP" | 
    "GEL" | "GHS" | "GIP" | "GMD" | "GNF" | "GTQ" | "GYD" | "HKD" | "HNL" | "HRK" | "HTG" | "HUF" | "IDR" | 
    "ILS" | "INR" | "IQD" | "IRR" | "ISK" | "JMD" | "JOD" | "JPY" | "KES" | "KGS" | "KHR" | "KMF" | "KPW" | 
    "KRW" | "KWD" | "KYD" | "KZT" | "LAK" | "LBP" | "LKR" | "LRD" | "LSL" | "LYD" | "MAD" | "MDL" | "MGA" | 
    "MKD" | "MMK" | "MNT" | "MOP" | "MRU" | "MUR" | "MVR" | "MWK" | "MXN" | "MXV" | "MYR" | "MZN" | "NAD" | 
    "NGN" | "NIO" | "NOK" | "NPR" | "NZD" | "OMR" | "PAB" | "PEN" | "PGK" | "PHP" | "PKR" | "PLN" | "PYG" | 
    "QAR" | "RON" | "RSD" | "RUB" | "RWF" | "SAR" | "SBD" | "SCR" | "SDG" | "SEK" | "SGD" | "SHP" | "SLL" | 
    "SOS" | "SRD" | "SSP" | "STN" | "SVC" | "SYP" | "SZL" | "THB" | "TJS" | "TMT" | "TND" | "TOP" | "TRY" | 
    "TTD" | "TWD" | "TZS" | "UAH" | "UGX" | "USD" | "USN" | "UYI" | "UYU" | "UZS" | "VEF" | "VES" | "VND" | 
    "VUV" | "WST" | "XAF" | "XAG" | "XAU" | "XBA" | "XBB" | "XBC" | "XBD" | "XCD" | "XDR" | "XOF" | "XPD" | 
    "XPF" | "XPT" | "XSU" | "XTS" | "XUA" | "XXX" | "YER" | "ZAR" | "ZMW" | "ZWL"
  )
```

This schema is also maintained in version control: [https://github.com/opds-community/specs/blob/master/schema/1.2/opds.rnc](https://github.com/opds-community/specs/blob/master/schema/1.2/opds.rnc)
