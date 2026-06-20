# Bakalari API v3 - Error Handling

Comprehensive guide to handling errors and implementing robust API clients.

## HTTP Status Codes

| Code | Status | Meaning | Typical Action |
|------|--------|---------|----------------|
| 200 | OK | Success | Process response |
| 400 | Bad Request | Invalid parameters | Check request format |
| 401 | Unauthorized | Invalid/expired token | Refresh authentication |
| 403 | Forbidden | Insufficient permissions | Check user permissions |
| 404 | Not Found | Invalid endpoint | Verify URL |
| 405 | Method Not Allowed | Wrong HTTP method | Use correct method |
| 500 | Server Error | Server problem | Retry with backoff |
| 502 | Bad Gateway | Gateway issue | Retry with backoff |
| 503 | Service Unavailable | Maintenance | Retry later |

---

## Error Response Format

### Standard Error Response

```json
{
  "error": "error_code",
  "error_description": "Human readable error message"
}
```

**Fields**:
- `error`: Machine-readable error code
- `error_description`: Human-readable explanation

---

## Common Error Scenarios

### 401 Unauthorized - Token Expired

**Response**:
```json
{
  "error": "invalid_token",
  "error_description": "The access token has expired"
}
```

**Cause**: Access token expired (~1 hour lifetime)

**Solution**:
1. Use refresh_token to obtain new access_token
2. Retry original request with new token

**Implementation**:
```python
def make_request(url, token):
    response = requests.get(url, headers={'Authorization': f'Bearer {token}'})
    if response.status_code == 401:
        new_token = refresh_access_token()
        response = requests.get(url, headers={'Authorization': f'Bearer {new_token}'})
    return response
```

---

### 400 Bad Request - Invalid Credentials

**Response**:
```json
{
  "error": "invalid_grant",
  "error_description": "Invalid username or password"
}
```

**Cause**: Wrong username or password

**Solution**:
1. Prompt user to verify credentials
2. Re-attempt login with correct credentials

---

### 400 Bad Request - Invalid Refresh Token

**Response**:
```json
{
  "error": "invalid_grant",
  "error_description": "Invalid refresh token"
}
```

**Cause**: Refresh token expired or revoked

**Solution**:
1. Clear all stored tokens
2. Require user to login again with username/password

---

### 405 Method Not Allowed

**Response**:
```json
{
  "error": "method_not_allowed",
  "error_description": "Method not allowed for this endpoint"
}
```

**Cause**: Using wrong HTTP method

**Solution**:
- Ensure GET requests for data retrieval
- Use POST only for `/api/login`

---

### 500/502/503 - Server Errors

**Response**: May be HTML or empty

**Cause**: Server overload, maintenance, or internal error

**Solution**: Implement exponential backoff retry

---

## Retry Strategy

### Exponential Backoff

```python
import time

def make_request_with_retry(url, max_retries=5):
    for attempt in range(max_retries):
        response = requests.get(url)
        if response.status_code in [200, 201]:
            return response
        elif response.status_code in [500, 502, 503]:
            if attempt < max_retries - 1:
                wait_time = 2 ** attempt  # 1, 2, 4, 8, 16 seconds
                time.sleep(wait_time)
                continue
        raise Exception(f"Request failed: {response.status_code}")
```

**Wait Times**:
- Attempt 1: 1 second
- Attempt 2: 2 seconds
- Attempt 3: 4 seconds
- Attempt 4: 8 seconds
- Attempt 5: 16 seconds

---

## Token Management Errors

### Refresh Before Expiry

**Best Practice**: Refresh tokens 5 minutes before expiration

```python
def get_valid_token():
    if token_expires_at - time.time() < 300:  # 5 minutes
        return refresh_access_token()
    return current_access_token
```

### Handle Refresh Failure

When refresh_token also fails:

```python
def refresh_access_token():
    response = requests.post('/api/login', data={
        'client_id': 'ANDR',
        'grant_type': 'refresh_token',
        'refresh_token': refresh_token
    })

    if response.status_code == 400:
        # Refresh token invalid, user must login again
        clear_all_tokens()
        raise AuthenticationRequiredException()

    return response.json()['access_token']
```

---

## Network Error Handling

### Connection Timeout

```python
try:
    response = requests.get(url, timeout=10)
except requests.Timeout:
    # Handle timeout
    return None
except requests.ConnectionError:
    # Handle connection error
    return None
```

### Recommended Timeouts

- Connection timeout: 10 seconds
- Read timeout: 30 seconds

```python
response = requests.get(url, timeout=(10, 30))
```

---

## Graceful Degradation

### Offline Support

When API is unavailable:

1. **Cache last known good state**
2. **Serve cached data**
3. **Queue failed requests**
4. **Sync when connection restored**

```python
def get_marks():
    try:
        return api_get('/api/3/marks')
    except ConnectionError:
        # Return cached data
        return get_cached_marks()
```

### Queue Failed Requests

```python
request_queue = []

def queue_request(url, params):
    request_queue.append({'url': url, 'params': params})

def process_queue():
    while request_queue:
        request = request_queue.pop(0)
        try:
            make_request(request['url'], request['params'])
        except ConnectionError:
            request_queue.append(request)
            break
```

---

## Error Recovery Flow

```
┌─────────────────┐
│  Make Request   │
└────────┬────────┘
         │
         v
┌─────────────────┐    Success
│  200 OK         │────────────> Return Response
└─────────────────┘
         │
         v
┌─────────────────┐
│  401 Unauthorized│
└────────┬────────┘
         │
         v
┌─────────────────┐
│ Refresh Token   │
└────────┬────────┘
         │
    ┌────┴────┐
    │         │
    v         v
┌──────┐  ┌──────────┐
│Success│  │  Failed  │
└───┬──┘  └─────┬────┘
    │           │
    v           v
┌─────────┐  ┌──────────────────┐
│ Retry   │  │ Require Login    │
│ Request │  │ Again            │
└─────────┘  └──────────────────┘
```

---

## User-Friendly Error Messages

Translate technical errors to user-friendly messages:

| Error | User Message |
|-------|--------------|
| 401 expired | "Session expired, refreshing..." |
| 401 invalid | "Please login again" |
| 403 forbidden | "You don't have permission for this" |
| 404 not found | "Resource not found" |
| 500 server error | "Server error, please try again" |
| Network error | "Connection error, check internet" |
| Timeout | "Request timed out, please try again" |

---

## Logging Error Information

For debugging, log error details:

```python
def log_error(response):
    print(f"API Error: {response.status_code}")
    print(f"URL: {response.url}")
    print(f"Response: {response.text}")
```

**Important**: Never log sensitive data (tokens, passwords)

---

## Best Practices

1. **Always handle 401** - Implement token refresh
2. **Use timeouts** - Prevent indefinite hanging
3. **Implement backoff** - Don't hammer the server
4. **Cache responses** - Reduce load and improve UX
5. **Queue requests** - Handle offline scenarios
6. **Clear tokens** - On refresh failure, require re-login
7. **Provide feedback** - Show user what's happening
8. **Log appropriately** - Debug info without sensitive data

---

## Testing Error Scenarios

Test your error handling:

1. **Invalid credentials** - Should show login error
2. **Expired token** - Should auto-refresh
3. **Invalid refresh token** - Should require re-login
4. **Network timeout** - Should show connection error
5. **Server error (500)** - Should retry with backoff
6. **Invalid endpoint** - Should handle gracefully

---

## Error Codes Reference

| OAuth2 Code | Meaning |
|-------------|---------|
| `invalid_request` | Missing required parameter |
| `invalid_client` | Invalid client_id |
| `invalid_grant` | Invalid credentials or refresh token |
| `unauthorized_client` | Client not authorized |
| `unsupported_grant_type` | Invalid grant_type |
| `invalid_token` | Invalid or expired access token |

---

## Summary

**Key Points**:
- 401 means token expired - refresh and retry
- 400 on refresh means re-login required
- 5xx errors - retry with exponential backoff
- Always implement timeouts
- Cache responses for better UX
- Queue requests for offline support
- Provide user-friendly error messages
