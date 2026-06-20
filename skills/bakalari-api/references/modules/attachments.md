# Attachments Module (`/api/3/komens/attachment`)

## Overview

The attachments module provides access to file attachments from messages and homework. Note that despite the URL path suggesting it's only for messages, this endpoint works for attachments from homework as well.

## Endpoint

### Download Attachment
```
GET /api/3/komens/attachment/{ATTACHMENT_ID}
```

Downloads a file attachment.

**Authentication**: Required
**Response**: File (binary)

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `ATTACHMENT_ID` | string | Attachment identifier (URL-encoded) |

**Important**: The attachment ID must be URL-encoded. For example, spaces become `%20`.

## Response

### Success (HTTP 200)

Returns the file as binary data with appropriate headers:

```http
HTTP/2 200
Content-Length: 1757
Content-Type: application/octet-stream
Content-Disposition: attachment; name="=?utf-8?B?...?="; filename*=utf-8''Never_gonna_give_you_up.txt
```

The file is returned directly in the response body.

### File Name from Headers

The `Content-Disposition` header contains the filename. It can be extracted in two ways:

**Method 1 - From `name` parameter:**
- ASCII filenames: `name="filename.txt"`
- Non-ASCII filenames: `name="=?utf-8?B?BASE64?="`

**Method 2 - From `filename*` parameter:**
- Always: `filename*=utf-8''%ENCODED_NAME`

## Usage Examples

### Download Attachment
```http
GET /api/3/komens/attachment/ABC123 HTTP/1.1
Authorization: Bearer <token>
```

### With URL Encoding
```http
GET /api/3/komens/attachment/ABC%20123 HTTP/1.1
Authorization: Bearer <token>
```

## Error Handling

### 401 Unauthorized
```json
{
  "Message": "Authorization has been denied for this request."
}
```
Token is expired or invalid.

### 404 Not Found
```json
{
  "Message": "An error has occurred."
}
```
Attachment ID is invalid or doesn't exist.

### 405 Method Not Allowed
```json
{
  "Message": "The requested resource does not support http method 'POST'."
}
```
Wrong HTTP method (must be GET).

## Implementation Examples

### Python - Extract Filename
```python
from cgi import parse_header
from base64 import b64decode
from urllib.parse import unquote

header = 'attachment; name="=?utf-8?B?TmV2ZXIgZ29ubmEgZ2l2ZSB5b3UgdXAudHh0?="; filename*=utf-8\'\'Never.txt'

# From filename* parameter
filename = unquote(parse_header(header)[1]["filename*"][7:])

# From name parameter (for non-ASCII)
if filename.startswith("=?utf-8?B?"):
    filename = b64decode(filename[10:-2]).decode()
```

### JavaScript - Download Attachment
```javascript
async function downloadAttachment(attachmentId, token) {
  const response = await fetch(`/api/3/komens/attachment/${attachmentId}`, {
    headers: {
      'Authorization': `Bearer ${token}`
    }
  });

  if (response.ok) {
    const blob = await response.blob();
    const url = window.URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = getFilenameFromHeaders(response.headers.get('Content-Disposition'));
    a.click();
  }
}
```

## Common Use Cases

1. **Homework attachments** - Download attached files for homework
2. **Message attachments** - Download files from received messages
3. **Document access** - Access school documents
4. **Resource materials** - Download learning materials

## Caching Strategy

- **Don't cache**: File content may change
- **Cache metadata**: Attachment info can be cached briefly

## Notes

- Works for both message and homework attachments
- Returns binary file data
- Content-Type is typically `application/octet-stream`
- Filename must be extracted from Content-Disposition header
- Special characters in IDs require URL encoding
- Large files may take time to download

## Related Modules

- **Homework** (`/api/3/homeworks`) - Homework with attachments
- **Komens** (`/api/3/komens/messages/*`) - Messages with attachments
