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
      "rel": "edit",
      "href": "https://example.com/user/edit",
      "type": "text/html"
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

A JSON Schema is available under version control at <https://github.com/opds-community/drafts/blob/master/schema/profile.schema.json>

For the purpose of validating an OPDS User Profile Document, use the following URL: <https://drafts.opds.io/schema/profile.schema.json>