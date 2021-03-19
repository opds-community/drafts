# OPDS User Profile 1.0

## MIME Type Considerations

All OPDS User Profile documents <strong class="rfc">must</strong> be served using the `application/opds-profile+json` media type.

## Example

```json
{
  "name": "John Smith",
  "email": "john.smith@example.com",
  "links": [
    {
      "rel": "http://opds-spec.org/shelf",
      "href": "https://example.com/bookshelf.json",
      "type": "application/opds+json"
    },
    {
      "rel": "http://opds-spec.org/history",
      "href": "https://example.com/history.json",
      "type": "application/opds+json"
    }
  ],
  "loans": {
    "total": 10,
    "available": 5
  },
  "holds": {
    "total": 5,
    "available": 5
  }
}
```

## Appendix A. JSON Schema

A JSON Schema is available under version control at [https://github.com/opds-community/drafts/blob/master/schema/profile.schema.json](https://github.com/opds-community/drafts/blob/master/schema/authentication.schema.json)

For the purpose of validating an OPDS User Profile Document, use the following URL: [https://drafts.opds.io/schema/profile.schema.json](https://drafts.opds.io/schema/authentication.schema.json)