# OPDS User Profile 1.0

## Media Type Considerations

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
