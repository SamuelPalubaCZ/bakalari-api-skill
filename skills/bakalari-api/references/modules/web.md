# Web Module (`/api/3/web`)

## Overview

The web module provides endpoints for browser-based authentication and web module configuration. This allows logging into the Bakalari web interface using API tokens.

## Endpoints

### Web Modules
```
GET /api/3/webmodule
```

Returns available web modules and their URLs.

**Authentication**: Required
**Content-Type**: `application/x-www-form-urlencoded`

**Response**:
```json
{
  "WebModules": [
    {
      "IconId": "dokumenty",
      "SubMenu": null,
      "Url": "next/dokumentyPrehled.aspx",
      "Name": "Dokumenty"
    },
    {
      "IconId": "vyukoveZdroje",
      "SubMenu": null,
      "Url": "next/TeachingResources.aspx",
      "Name": "Výukové zdroje"
    }
  ],
  "Dashboard": {
    "IconId": null,
    "SubMenu": null,
    "Url": "next/dash.aspx",
    "Name": null
  }
}
```

### Get Login Token
```
GET /api/3/logintoken
```

Returns a one-time token for web authentication.

**Authentication**: Required
**Content-Type**: `application/x-www-form-urlencoded`

**Response**:
```
"XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
```

Returns a one-time `LOGIN_TOKEN` for browser authentication.

### Login via Token
```
GET /api/3/login/LOGIN_TOKEN?returnUrl=next/dash.aspx
```

Authenticates to the web interface using a login token.

**No Authentication** Required (token is in URL)

**Query Parameters**:
- `returnUrl`: URL to redirect after successful login

**Success Response** (302 Found):
```
Location: /next/dash.aspx
```

**Failure Response** (302 Found):
```
Location: /login?ReturnUrl=next/dash.aspx
```

## Response Fields

### Web Module Object
| Field | Type | Description |
|-------|------|-------------|
| `IconId` | string | Icon identifier |
| `Name` | string | Module name |
| `Url` | string | Relative URL to module |
| `SubMenu` | object | Submenu items (if any) |

### Dashboard Object
| Field | Type | Description |
|-------|------|-------------|
| `IconId` | string | Icon identifier (usually null) |
| `Url` | string | Dashboard URL |
| `Name` | string | Name (usually null) |

## Usage Examples

### Get Web Modules
```http
GET /api/3/webmodule HTTP/1.1
Authorization: Bearer <token>
```

### Get Login Token
```http
GET /api/3/logintoken HTTP/1.1
Authorization: Bearer <token>
```

### Login to Web
```http
GET /api/3/login/ABCDEFG123456789?returnUrl=next/dash.aspx HTTP/1.1
```

## Authentication Flow for Web

1. **Authenticate via API**: Get access_token using `/api/login`
2. **Get login token**: Call `/api/3/logintoken` with access_token
3. **Login to web**: Use returned LOGIN_TOKEN in `/api/3/login/{LOGIN_TOKEN}`
4. **Access web**: Redirected to returnUrl with web session

## Use Cases

1. **Browser integration** - Log users into web interface programmatically
2. **Deep linking** - Direct users to specific web pages
3. **Web modules access** - Discover available web features
4. **Embedded views** - Embed web interface in apps

## Common Use Cases

1. **SSO integration** - Single sign-on to web interface
2. **Document access** - Link to web documents module
3. **Teaching resources** - Access web teaching resources
4. **Dashboard access** - Direct to dashboard

## Error Handling

### 401 Unauthorized
```json
{
  "Message": "Authorization has been denied for this request."
}
```
Token is expired or invalid.

### 405 Method Not Allowed
```json
{
  "Message": "The requested resource does not support http method 'POST'."
}
```
Wrong HTTP method (must be GET).

## Implementation Notes

1. **One-time token**: LOGIN_TOKEN is single-use
2. **Web session**: Creates web browser session
3. **Redirect based**: Uses HTTP redirects for login
4. **Return URL**: Specify where to redirect after login

## Caching Strategy

- **Web modules**: Cache for 1 hour
- **Login token**: Don't cache (one-time use)

## Notes

- Login token is one-time use only
- Creates web browser session, not API session
- Web modules may vary by school configuration
- ReturnUrl must be relative path
- Useful for browser-based integration
- Token format varies (random string)

## Related Modules

- **Login** (`/api/login`) - Initial authentication
- **Authentication** - Token management
- **API Info** (`/api/3`) - API version information
