# OPDS Progression 1.0

OPDS Progression 1.0 defines a JSON based API to fetch and update the last known progression in an OPDS publication.

## Discovery

A progression service is tied to a specific publication and can be discovered in its links using either OPDS 1.x or 2.0.

It can be detected using: 

- `http://opds-spec.org/progression` as its `rel` value
- and `application/opds-progression+json` as its media type

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

## Progression Document

| Key | Definition | Format | Required |
| --- | ---------- | ------ | -------- |
| `modified` | Timestamp for the last-known progression. | ISO 8601 time and date | Yes |
| `device` | Identifies the device that provided the last-known progression. | [Device Object](#device-object) | Yes |
| `progression` | Total progression in the publication expressed as a percentage (%). | Float between 0 and 1 | Yes |
| `fragments` | Media-specific fragments. | Array of fragments using strings | No |

Fragments are extensible by nature but the following well-known fragments have been identified while writing up this document:

| Specification | Scope | Examples |
| ------------- | ----- | ------- |
| [HTML](https://html.spec.whatwg.org/) | HTML | `#id` |
| [Scroll to Text Fragment](https://wicg.github.io/scroll-to-text-fragment/) | Text |  `#:~:text=an%20example` |
| [Media Fragment URI 1.0](https://www.w3.org/TR/media-frags/) | Audio, Video and Images | `#t=67`, `#xywh=160,120,320,240`|
| [PDF](http://tools.ietf.org/rfc/rfc3778) | PDF | `#page=12` |

### Device Object

| Key | Definition | Format | Required |
| --- | ---------- | ------ | -------- |
| `id` | A unique identifier, not meant to be displayed to the user. | URI | Yes |
| `name` | A device name, meant to be displayed to the user. | String | Yes |


### Examples

*Example 3: Progression in an EPUB*

```json
{
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
  "modified": "2026-01-28T00:24:00Z",
  "device": {
    "id": "urn:uuid:019c0047-cc8d-7ec4-a3c3-938ccadc020a",
    "name": "Ebook Reader (Pixel 10 Pro)"
  },
  "progression": 0.0174920,
  "fragments": ["#t=40.274", "#par36"]
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
  "fragments": ["#page=17"]
}
```

*Example 6: Progression in an audiobook*

```json
{
  "modified": "2025-12-25T12:00:00Z",
  "device": {
    "id": "urn:uuid:019c0049-6e8c-745c-adb1-5b03f8ad50c4",
    "name": "Audiobook Player (Sonos Era 300)"
  },
  "progression": 0.72370325,
  "fragments": ["#t=849.250"]
}
```

## Interactions

| HTTP Verb | Expected behavior | 
| --------- | ----------------- | 
| `GET` | Client requests the last-known progression from the server. If this information is more up-to-date than what the client has, it requests to the user if they'd like to jump to the new progression. |
| `PUT` | Client sends the last-known progression to the server. Server determines whether the progression should be updated and returns a progression. |