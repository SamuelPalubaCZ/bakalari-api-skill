# API Info Module (`/api`)

## Overview

The API info module provides information about available API versions and their metadata. This is useful for determining compatibility and feature availability.

## Endpoints

### All API Versions
```
GET /api
```

Returns a list of all available API versions.

**Authentication**: Not required

### Current API Version (v3)
```
GET /api/3
```

Returns information about API version 3.

**Authentication**: Not required

## Response Structures

### All API Versions Response

```http
GET /api HTTP/1.1
```

```json
[
  {
    "ApiVersion": "3.12.0",
    "ApplicationVersion": "1.32.625.2",
    "BaseUrl": "api/3"
  }
]
```

### Current Version Response

```http
GET /api/3 HTTP/1.1
```

```json
{
  "ApiVersion": "3.12.0",
  "ApplicationVersion": "1.32.625.2",
  "BaseUrl": "api/3"
}
```

## Response Fields

| Field | Type | Description |
|-------|------|-------------|
| `ApiVersion` | string | API version number (e.g., "3.12.0") |
| `ApplicationVersion` | string | Bakaláři application version |
| `BaseUrl` | string | Base URL for this API version |

## Usage Examples

### Check Available Versions
```http
GET /api HTTP/1.1
```

### Get Version 3 Info
```http
GET /api/3 HTTP/1.1
```

## Use Cases

1. **Version detection** - Determine available API versions
2. **Compatibility check** - Verify required version is available
3. **Feature detection** - Some features depend on API version
4. **Debugging** - Identify server version information

## API Versioning

### Version 3
- **Current stable version**
- **Base URL**: `/api/3/`
- **Status**: Actively maintained
- **Features**: All documented endpoints

### Version History

| Version | Status | Notes |
|---------|--------|-------|
| v1 | Deprecated | Legacy version |
| v2 | Deprecated | Legacy version |
| v3 | Current | Active development |

## Error Handling

### 405 Method Not Allowed (POST)

```json
{
  "Message": "The requested resource does not support http method 'POST'."
}
```

These endpoints only support GET requests.

## Implementation Notes

1. **No authentication** - These endpoints are public
2. **Cache aggressively** - Version info rarely changes
3. **Use before login** - Can check version before authentication
4. **Version validation** - Verify minimum required version

## Caching Strategy

- **Version info**: Cache for 24 hours
- **Rarely changes**: Version information is stable
- **Safe to cache**: No authentication required

## Version Number Format

API versions follow semantic versioning:
- **Major**: 3 (API generation)
- **Minor**: 12 (feature additions)
- **Patch**: 0 (bug fixes)

Example: `3.12.0`
- Major version 3 = third API generation
- Minor version 12 = feature set 12
- Patch version 0 = no patch

## Related Information

- **User module** (`/api/3/user`) - Also returns ApiVersion field
- **Authentication** - Required for most other endpoints
- **Compatibility** - Check version for feature availability

## Technical Details

### Application Version

The `ApplicationVersion` field indicates the Bakaláři server software version:
- Used for debugging compatibility issues
- May indicate feature availability
- Helpful for support scenarios

### Base URL

The `BaseUrl` field indicates:
- Relative path for API requests
- Usually `api/3` for version 3
- Used with school base URL

Example:
```
School base: https://bakalari.gymnazium.cz:444
API base: api/3
Full URL: https://bakalari.gymnazium.cz:444/api/3/marks
```

## Notes

- These endpoints don't require authentication
- Use for version detection before API calls
- API v3 is the current stable version
- Versions are backwards compatible within major version
- Check ApiVersion for new features availability
- ApplicationVersion can help troubleshoot server issues
