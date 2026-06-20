# Homework Student Done Module (`/api/3/homeworks`)

## Overview

The homework student done module allows marking homework assignments as completed or not completed.

## Endpoint

### Mark Homework Done/Not Done
```
PUT /api/3/homeworks/{HOMEWORK_ID}/student-done/{status}
```

Marks a homework assignment as done or not done.

**Authentication**: Required

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `HOMEWORK_ID` | string | Homework identifier (URL-encoded if needed) |
| `status` | boolean | `true` for done, `false` for not done |

## URL Encoding

The homework ID may contain special characters that require URL encoding:
- `[` becomes `%5B`
- `]` becomes `%5D`
- Other special characters should be encoded

**Example**: `ZSQCVE[IMJ` → `ZSQCVE%5BIMJ`

## Usage Examples

### Mark as Done
```http
PUT /api/3/homeworks/ABC123/student-done/true HTTP/1.1
Authorization: Bearer <token>
```

### Mark as Not Done
```http
PUT /api/3/homeworks/ABC123/student-done/false HTTP/1.1
Authorization: Bearer <token>
```

### With URL Encoding
```http
PUT /api/3/homeworks/ZSQCVE%5BIMJ/student-done/true HTTP/1.1
Authorization: Bearer <token>
```

## Implementation Examples

### JavaScript
```javascript
// Properly encode the homework ID
const homeworkId = 'ZSQCVE[IMJ';
const encodedId = encodeURIComponent(homeworkId); // ZSQCVE%5BIMJ

fetch(`/api/3/homeworks/${encodedId}/student-done/true`, {
  method: 'PUT',
  headers: {
    'Authorization': `Bearer ${token}`
  }
});
```

### Python
```python
from urllib.parse import quote

homework_id = 'ZSQCVE[IMJ'
encoded_id = quote(homework_id)  # ZSQCVE%5BIMJ

requests.put(
    f'/api/3/homeworks/{encoded_id}/student-done/true',
    headers={'Authorization': f'Bearer {token}'}
)
```

## Use Cases

1. **Mark complete** - Student marks homework as done
2. **Unmark complete** - Student removes completion status
3. **Sync status** - Synchronize completion across devices

## Common Use Cases

1. **Student app** - Allow students to mark homework done
2. **Progress tracking** - Track homework completion
3. **Todo list** - Manage homework tasks

## Caching Strategy

- **Don't cache**: Real-time status change
- **Re-fetch homework**: After marking done/not done

## Notes

- Uses PUT method, not POST or GET
- Status is boolean (true/false)
- Homework IDs may contain special characters
- Always URL encode IDs with special characters
- Status is visible in homework responses (`Completed` field)
- Changes should reflect immediately in subsequent homework calls

## Related Modules

- **Homework** (`/api/3/homeworks`) - View and manage homework
- **Homework Count** (`/api/3/homeworks/count-actual`) - Count homework
