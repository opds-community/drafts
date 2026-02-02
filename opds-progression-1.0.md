# OPDS Progression 1.0

OPDS Progression 1.0 defines a JSON based API to fetch and update the last known progression in an OPDS publication.

# Auto-discovery

A progression service is tied to a specific publication and can be discovered in links using either OPDS 1.x or 2.0.

It can be detected using: 

- `http://opds-spec.org/progression` for `rel` value
- and `application/opds-progression+json` for the media type

*Example 1: Link to a progression service in OPDS 2.0*

```json
{
  "href": "https://example.com/019c0435-5361-7e59-89b7-4ee01a6d87b8/progression",
  "type": "application/opds-progression+json",
  "rel": "http://opds-spec.org/progression"
}
```

When authentication is required, OPDS Catalogs should use an `authenticate` hint to avoid unnecessary round-trips.

*Example 2: Link that requires authentication*

```json
{
  "href": "https://example.com/019c0435-5361-7e59-89b7-4ee01a6d87b8/progression",
  "type": "application/opds-progression+json",
  "rel": "http://opds-spec.org/progression",
  "properties": {
    "authenticate": {
      "href": "https://example.com/authentication.json",
      "type": "application/opds-authentication+json"
    }
  }
}
```

# Syntax

## Progression Document

| Key | Definition | Format | Required |
| --- | ---------- | ------ | -------- |
| `title` | Contains text that can be relevant to identify or contextualize the progression. | String | No |
| `modified` | Timestamp for the last-known progression. | ISO 8601 time and date | Yes |
| `device` | Identifies the device that provided the last-known progression. | [Device Object](#device-object) | Yes |
| `progression` | Total progression in the publication expressed as a percentage (%). | Float between 0 and 1 | Yes |
| `references` | References inside the publication. | Array of URI references | No |

## References

> [!NOTE]
> WHATWG is in the process of integrating scroll to text fragments into the HTML specification. Once that's done, we'll remove this reference to the WICG draft.

References are meant to refine the progression expressed in `progression`.

Common use-cases for these references include:

- a media fragment for the entire publication (`#t=67`)
- a path into a packaged publication with an optional media fragment (`chapter1.html#par26`)
- or a full URL with an optional media fragment (`https://example.com/chapter1#par26`)

This document has identified the following specifications for media fragments:

| Specification | Scope | Examples |
| ------------- | ----- | ------- |
| [HTML](https://html.spec.whatwg.org/) | HTML | `#id` |
| [Scroll to Text Fragment](https://wicg.github.io/scroll-to-text-fragment/) | Text |  `#:~:text=an%20example` |
| [Media Fragment URI 1.0](https://www.w3.org/TR/media-frags/) | Audio, Video and Images | `#t=67`, `#xywh=160,120,320,240`|
| [PDF](http://tools.ietf.org/rfc/rfc3778) | PDF | `#page=12` |

This document does not provide processing rules for clients or servers.

In general, it is recommended to use more specific references over more generic one and to always fallback to `progression` when none of the URI references included in `references` can't be resolved.

## Device Object

| Key | Definition | Format | Required |
| --- | ---------- | ------ | -------- |
| `id` | A unique identifier, not meant to be displayed to the user. | URI | Yes |
| `name` | A device name, meant to be displayed to the user. | String | Yes |


## Examples

*Example 3: Progression in an EPUB*


```json
{
  "title": "Chapter 1 - A New Dawn",
  "modified": "2026-01-27T11:00:00Z",
  "device": {
    "id": "urn:uuid:019c0047-cc8d-7ec4-a3c3-938ccadc020a",
    "name": "Ebook Reader (Pixel 10 Pro)"
  },
  "progression": 0.0174920
}
```

*Example 4: Progression in an EPUB with Media Overlays*

```json
{
  "title": "Chapter 1 - A New Dawn",
  "modified": "2026-01-28T00:24:00Z",
  "device": {
    "id": "urn:uuid:019c0047-cc8d-7ec4-a3c3-938ccadc020a",
    "name": "Ebook Reader (Pixel 10 Pro)"
  },
  "progression": 0.0174920,
  "references": ["#t=40.274", "chapter1.html#par36"]
}
```

*Example 5: Progression in a PDF*

```json
{
  "modified": "2026-01-28T19:00:00Z",
  "device": {
    "id": "https://reader.example.com",
    "name": "Web Reader"
  },
  "progression": 0.048204,
  "references": ["#page=17"]
}
```

*Example 6: Progression in an audiobook*

```json
{
  "title": "Part 4",
  "modified": "2025-12-25T12:00:00Z",
  "device": {
    "id": "urn:uuid:019c0049-6e8c-745c-adb1-5b03f8ad50c4",
    "name": "Audiobook Player (Sonos Era 300)"
  },
  "progression": 0.72370325,
  "references": ["#t=849.250"]
}
```

# Interactions

| HTTP Verb | Expected behavior | 
| --------- | ----------------- | 
| `GET` | Client requests the last-known progression from the server. If this information is more up-to-date than what the client has, it requests to the user if they'd like to jump to the new progression. |
| `PUT` | Client sends the last-known progression to the server. Server determines whether the progression should be updated and returns a progression. |


# Appendix A - JSON Schema

A JSON Schema is available under version control at https://github.com/opds-community/drafts/blob/master/schema/progression.schema.json

For the purpose of validating an OPDS Authentication Document, use the following URL: https://drafts.opds.io/schema/progression.schema.json