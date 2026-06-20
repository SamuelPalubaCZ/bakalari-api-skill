# Bakalari API v3 - Authentication

## Overview

The Bakalari API uses OAuth2-style token authentication. All API requests (except login) require a valid access token.

## Authentication Flow

```
┌─────────┐                    ┌──────────────┐                    ┌─────────────────┐
│ Client  │                    │ Bakalari API │                    │   Token Store   │
└────┬────┘                    └──────┬───────┘                    └────────┬────────┘
     │                                │                                     │
     │ POST /api/login               │                                     │
     │ (username + password)         │                                     │
     ├──────────────────────────────>│                                     │
     │                                │                                     │
     │ access_token + refresh_token  │                                     │
     │<──────────────────────────────┤                                     │
     │                                │                                     │
     │ Store tokens securely         │                                     │
     ├─────────────────────────────────────────────────────────────────────>│
     │                                │                                     │
     │ GET /api/3/* + Bearer token   │                                     │
     ├──────────────────────────────>│                                     │
     │                                │                                     │
     │ API Response                   │                                     │
     │<──────────────────────────────┤                                     │
     │                                │                                     │
     │ Token expires (~1 hour)        │                                     │
     │<══════════════════════════════│                                     │
     │                                │                                     │
     │ POST /api/login               │                                     │
     │ (refresh_token)               │                                     │
     ├──────────────────────────────>│                                     │
     │                                │                                     │
     │ New access_token + refresh_token                                      │
     │<──────────────────────────────┤                                     │
     │                                │                                     │
     │ Update stored tokens          │                                     │
     ├─────────────────────────────────────────────────────────────────────>│
     │                                │                                     │
```

## Login Endpoint

### Initial Login

**Endpoint**: `POST /api/login`

**Content-Type**: `application/x-www-form-urlencoded`

**Request Body**:
```
client_id=ANDR&grant_type=password&username=<USERNAME>&password=<PASSWORD>
```

**Parameters**:

| Parameter | Value | Description |
|-----------|-------|-------------|
| client_id | `ANDR` | Android client identifier (always this value) |
| grant_type | `password` | OAuth2 grant type for initial login |
| username | string | User's login username |
| password | string | User's password |

**Response** (HTTP 200 OK):

```json
{
  "access_token": "eyJhbGciOiJIUzI1NiIs...",
  "refresh_token": "def50200f3b7f8e9d1c2a...",
  "id_token": "eyJhbGciOiJIUzI1NiIs...",
  "token_type": "Bearer",
  "expires_in": 3599,
  "bak:UserId": "12345",
  "bak:ApiVersion": "3.13"
}
```

**Response Fields**:

| Field | Type | Description | Lifetime |
|-------|------|-------------|----------|
| access_token | string | Bearer token for API requests | ~1 hour |
| refresh_token | string | Token for obtaining new access token | ~1 month |
| id_token | string | JWT with user identity information | N/A |
| token_type | string | Always "Bearer" | N/A |
| expires_in | number | Seconds until access_token expires | ~3599 |
| bak:UserId | string | Unique user identifier | N/A |
| bak:ApiVersion | string | API version number | N/A |

## Token Refresh

### Refresh Access Token

**Endpoint**: `POST /api/login`

**Content-Type**: `application/x-www-form-urlencoded`

**Request Body**:
```
client_id=ANDR&grant_type=refresh_token&refresh_token=<REFRESH_TOKEN>
```

**Parameters**:

| Parameter | Value | Description |
|-----------|-------|-------------|
| client_id | `ANDR` | Android client identifier |
| grant_type | `refresh_token` | OAuth2 grant type for refresh |
| refresh_token | string | Refresh token from initial login |

**Response**: Same format as initial login

**Important Notes**:
- Each refresh returns a NEW refresh token
- Old refresh_token becomes invalid after successful refresh
- Always store the latest refresh_token
- Refresh token lifetime is ~1 month

## Using Access Token

### Authorization Header

All authenticated requests require the Bearer token:

```http
Authorization: Bearer <access_token>
```

**Example Request**:

```http
GET /api/3/marks HTTP/1.1
Host: https://bakalari.gymnazium.cz:444
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

## Error Handling

### Invalid Credentials

**Status**: 400 Bad Request

**Response**:
```json
{
  "error": "invalid_grant",
  "error_description": "Invalid username or password"
}
```

**Action**: Verify credentials and retry

### Expired Token

**Status**: 401 Unauthorized

**Response**:
```json
{
  "error": "invalid_token",
  "error_description": "The access token has expired"
}
```

**Action**: Use refresh_token to obtain new access_token

### Invalid Refresh Token

**Status**: 400 Bad Request

**Response**:
```json
{
  "error": "invalid_grant",
  "error_description": "Invalid refresh token"
}
```

**Action**: User must re-authenticate with username/password

## Token Lifecycle

```
Login
  │
  ├─> access_token (expires ~1 hour)
  │     │
  │     └─> Use for API requests
  │           │
  │           └─> Expires (401 error)
  │                 │
  │                 └─> Refresh with refresh_token
  │
  └─> refresh_token (expires ~1 month)
        │
        ├─> Use to get new access_token
        │     │
        │     └─> Returns NEW refresh_token
        │           │
        │           └─> Old refresh_token invalidated
        │
        └─> Expires → User must login again
```

## Security Best Practices

### Token Storage

1. **Encrypt at rest** - Store tokens encrypted
2. **Use secure storage** - Use platform secure storage (Keychain, Keystore)
3. **Never log tokens** - Tokens are sensitive credentials
4. **Clear on logout** - Delete all stored tokens on logout

### Token Usage

1. **HTTPS only** - Never send tokens over HTTP
2. **Short exposure** - Keep tokens in memory only when needed
3. **Validate server certificate** - Prevent MITM attacks
4. **Check expiration** - Refresh tokens before expiry

### Refresh Strategy

1. **Proactive refresh** - Refresh tokens 5 minutes before expiry
2. **Handle 401 gracefully** - Refresh and retry failed request
3. **Queue requests** - During refresh, queue pending requests
4. **Abort on failure** - If refresh fails, require user login

## Implementation Notes

### Client ID

The `client_id=ANDR` is the Android app identifier. Other valid values:
- `ANDR` - Android (most commonly used)
- `IOS` - iOS (may work, less tested)

### Token Formats

- `access_token`: ~2556 characters, random-looking string
- `refresh_token`: ~3459 characters, starts with `def50200`
- `id_token`: ~872 characters, JWT format (base64 with dots)

### Expiration Handling

**Recommended approach**:
```python
# Get current timestamp when receiving token
token_expires_at = time.time() + response['expires_in']

# Check before using token
if time.time() >= token_expires_at - 300:  # 5 min buffer
    refresh_access_token()
```

### Full Authentication Example

1. **Initial Login**:
   ```
   POST /api/login
   → Store access_token, refresh_token, expires_at
   ```

2. **Make API Request**:
   ```
   GET /api/3/marks
   Header: Authorization: Bearer <access_token>
   ```

3. **On 401 Error**:
   ```
   POST /api/login with refresh_token
   → Update access_token, refresh_token, expires_at
   → Retry original request
   ```

4. **On Refresh Failure**:
   ```
   → Clear all tokens
   → Prompt user to login again
   ```
