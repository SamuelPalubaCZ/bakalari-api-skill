---
name: bakalari-api
description: This skill should be used when the user asks to "integrate with Bakalari API", "build a Bakalari app", "connect to Bakaláři school system", "fetch grades from Bakalari", "work with bakalari-api", mentions "Bakalari API v3", or discusses the Czech school information system integration.
version: 1.0.0
license: MIT
---

# Bakalari API v3

The Bakalari API v3 is the official API used by the Bakaláři OnLine mobile application to communicate with school servers. This skill provides comprehensive documentation for integrating with the Czech school information system.

## Overview

**Bakaláři** is a widely used school information system in the Czech Republic. The API v3 enables third-party applications to access student data including grades, timetable, homework, events, messages, and more.

**Important**: This is an unofficial community-maintained documentation. The API is owned by the BAKALÁŘI software company.

### Key Characteristics

- **Base Protocol**: HTTPS
- **API Version**: v3 (endpoints prefixed with `/api/3/`)
- **Authentication**: OAuth2 Bearer tokens
- **Content Type**: `application/x-www-form-urlencoded` for requests, `application/json` for responses
- **Token Lifetime**: Access tokens ~1 hour, refresh tokens ~1 month
- **School-Specific URLs**: Each school has its own base URL

## Architecture

### URL Structure

```
<SCHOOL_BASE_URL>/api/3/<MODULE>/<ACTION>
```

**Example**: `https://bakalari.gymnazium.cz:444/api/3/marks`

**Components**:
- `SCHOOL_BASE_URL`: School-specific HTTPS endpoint (often includes port like `:444`)
- `/api/3/`: API version prefix
- `MODULE`: Feature area (marks, timetable, homeworks, etc.)
- `ACTION`: Specific operation (optional for some endpoints)

### Common Base URL Pattern

School URLs typically follow one of these patterns:
- `https://<school-domain>.cz:<port>`
- `https://<subdomain>.<school-domain>.cz:<port>`

The port is commonly `444` but varies by school deployment.

## Authentication Flow

The API uses OAuth2-style token authentication. All authenticated requests require a valid access token.

### Step 1: Initial Login

**Endpoint**: `POST /api/login`

**Request Body** (URL-encoded):
```
client_id=ANDR&grant_type=password&username=<USERNAME>&password=<PASSWORD>
```

**Parameters**:
- `client_id`: Always `ANDR` (Android client identifier)
- `grant_type`: `password` for initial login
- `username`: User's login username
- `password`: User's password

**Response** (JSON):
```json
{
  "access_token": "<2556-character token>",
  "refresh_token": "<3459-character token>",
  "id_token": "<872-character JWT token>",
  "token_type": "Bearer",
  "expires_in": 3599,
  "bak:UserId": "<user-identifier>",
  "bak:ApiVersion": "3.13"
}
```

**Token Fields**:
- `access_token`: Used for all API requests (1 hour lifetime)
- `refresh_token`: Used to obtain new access tokens (~1 month lifetime)
- `id_token`: JWT containing user identity information
- `expires_in`: Seconds until access token expires
- `bak:UserId`: Unique user identifier
- `bak:ApiVersion`: API version supported by server

### Step 2: Using Access Token

For all authenticated API requests, include the Authorization header:

```
Authorization: Bearer <ACCESS_TOKEN>
```

**Example Request**:
```http
GET /api/3/marks HTTP/1.1
Host: bakalari.gymnazium.cz:444
Authorization: Bearer eyJhbGc...
```

### Step 3: Token Refresh

When access token expires (typically 1 hour), use the refresh token to obtain a new one.

**Endpoint**: `POST /api/login`

**Request Body** (URL-encoded):
```
client_id=ANDR&grant_type=refresh_token&refresh_token=<REFRESH_TOKEN>
```

**Parameters**:
- `client_id`: Always `ANDR`
- `grant_type`: `refresh_token`
- `refresh_token`: The refresh token from initial login

**Response**: Same format as initial login, with new tokens.

**Important**: The old refresh token becomes invalid after a successful refresh. Always use the newest refresh token.

## Request Format

### HTTP Methods

- **GET**: Retrieve data (most common)
- **POST**: Submit data or trigger actions
- **PUT**: Update resources (rarely used)

### Headers

**Required for authenticated requests**:
```
Authorization: Bearer <ACCESS_TOKEN>
```

**Optional**:
```
Content-Type: application/x-www-form-urlencoded
Accept: application/json
```

### Query Parameters

Most GET endpoints support query parameters for filtering and pagination:

```
/api/3/homeworks?from=2025-01-01&to=2025-01-31
```

Common date format: ISO 8601 (`2025-01-15T00:00:00+01:00`)

### POST Request Body

POST requests use URL-encoded format:

```
param1=value1&param2=value2
```

Set header: `Content-Type: application/x-www-form-urlencoded`

## Response Format

### Success Response

All successful responses return JSON:

```json
{
  "Field1": "Value1",
  "Field2": ["Array", "Values"],
  "Field3": {
    "Nested": "Object"
  }
}
```

### Common Object Types

**Subject**:
```json
{
  "Id": "string",
  "Abbrev": "string",
  "Name": "string"
}
```

**Teacher**:
```json
{
  "Id": "string",
  "Abbrev": "string",
  "Name": "string",
  "Type": "string"
}
```

**Class**:
```json
{
  "Id": "string",
  "Abbrev": "string",
  "Name": "string"
}
```

### Error Response

Errors return appropriate HTTP status codes:

```json
{
  "error": "error_code",
  "error_description": "Human readable error message"
}
```

## Error Handling

### HTTP Status Codes

| Code | Meaning | Action |
|------|---------|--------|
| 200 OK | Success | Process response normally |
| 400 Bad Request | Invalid parameters | Check request format |
| 401 Unauthorized | Invalid/expired token | Refresh token and retry |
| 404 Not Found | Invalid endpoint | Verify URL |
| 405 Method Not Allowed | Wrong HTTP method | Use correct method |
| 500 Server Error | Server problem | Retry later |

### Common Error Scenarios

**Token Expired (401)**:
```
Error: 401 Unauthorized
Action: Use refresh_token to get new access_token
```

**Invalid Credentials (400)**:
```
Error: 400 Bad Request - "invalid_grant"
Action: Verify username and password are correct
```

**Wrong HTTP Method (405)**:
```
Error: 405 Method Not Allowed
Action: Ensure you're using GET for data retrieval
```

### Retry Logic

Implement exponential backoff for server errors:
- Wait 1 second, then retry
- If still failing, wait 2 seconds
- Continue doubling wait time up to 30 seconds
- After 5 failed retries, abort

## Available Modules

The API is organized into modules. Each module provides specific functionality:

| Module | Purpose | Endpoint Pattern |
|--------|---------|------------------|
| **User** | User profile and permissions | `/api/3/user` |
| **Marks** | Grades and evaluations | `/api/3/marks` |
| **Final Marks** | Report cards and final grades | `/api/3/marks/final` |
| **Marks Measures** | Disciplinary measures and commendations | `/api/3/marks/measures` |
| **Timetable** | Schedule and lessons | `/api/3/timetable/*` |
| **Homeworks** | Assignments and tasks | `/api/3/homeworks` |
| **Events** | School events calendar | `/api/3/events/*` |
| **Komens** | Messaging system | `/api/3/komens/*` |
| **Absence** | Attendance records | `/api/3/absence/*` |
| **Subjects** | Subject catalog | `/api/3/subjects` |
| **Substitutions** | Schedule changes | `/api/3/substitutions` |
| **Payments** | Class fund management | `/api/3/payments/*` |
| **GDPR** | Data protection information | `/api/3/gdpr/*` |
| **Campaign** | School announcements and ads | External service |

### Module Availability

Not all modules are enabled for every school or user. Check the user endpoint to see which modules are available:

```json
{
  "Modules": [
    {"Name": "marks", "Enabled": true},
    {"Name": "timetable", "Enabled": true},
    {"Name": "homeworks", "Enabled": false}
  ]
}
```

## Quick Endpoint Reference

### Authentication
- `POST /api/login` - Authenticate and get tokens

### Core Data
- `GET /api/3/user` - User profile and module list
- `GET /api/3/subjects` - All subjects with teacher assignments

### Grades
- `GET /api/3/marks` - All grades and evaluations
- `POST /api/3/marks/what-if` - Grade prediction calculator
- `GET /api/3/marks/final` - Historical report cards
- `GET /api/3/marks/measures` - Disciplinary measures and commendations
- `POST /api/3/marks/SetClassificationConfirmation` - Confirm/sign grades

### Timetable
- `GET /api/3/timetable/actual` - Current week timetable
- `GET /api/3/timetable/permanent` - Full year timetable
- `GET /api/3/timetable/actual/<date>` - Specific week

### Homework
- `GET /api/3/homeworks` - Homework with date filtering
- Parameters: `from`, `to` (ISO dates)

### Events
- `GET /api/3/events` - All events
- `GET /api/3/events/my` - User's personal events
- `GET /api/3/events/public` - Public school events

### Messages (Komens)
- `GET /api/3/komens/messages/received` - Received messages
- `GET /api/3/komens/messages/sent` - Sent messages
- `GET /api/3/komens/messages/noticeboard` - School noticeboard

### Attendance
- `GET /api/3/absence/student` - Student absence records

### Changes
- `GET /api/3/substitutions` - Teacher and room substitutions

### Additional
- `GET /api/3/user/student-at-school` - Student presence status
- `GET /api/3/payments/classfund/summary` - Class fund balance
- `GET /api/3/payments/classfund` - Payment history
- `GET /api/3/gdpr/commissioners` - GDPR contacts
- `GET /api` - Available API versions

## Best Practices

### Security

1. **Never store passwords** - Only store tokens securely
2. **Use secure storage** - Encrypt tokens when storing locally
3. **HTTPS only** - Never use HTTP (API requires HTTPS)
4. **Token refresh** - Refresh before expiry to maintain session

### Rate Limiting

- Implement delays between requests (recommended: 100-500ms)
- Cache responses where appropriate
- Respect server rate limits (typically 60-120 requests/minute)

### Caching Strategy

Cache data with appropriate TTL:
- **User profile**: 24 hours
- **Subjects**: 7 days
- **Marks**: 5-15 minutes
- **Timetable**: 1 hour
- **Homework**: 30 minutes
- **Events**: 1 hour

### Offline Support

The API supports offline operation through token-based caching:
1. Store last known good state locally
2. Use cached data when offline
3. Sync when connection restored

### Error Recovery

Always implement graceful degradation:
1. Check network connectivity before requests
2. Handle token expiration gracefully
3. Provide cached data when API unavailable
4. Queue failed requests for retry

### Date Handling

All dates use ISO 8601 format with timezone:
```
2025-06-20T00:00:00+02:00
```

When making requests, ensure dates are properly URL-encoded:
```
from=2025-06-20T00%3A00%3A00%2B02%3A00
```

## Module-Specific Details

For detailed information about each module's endpoints, request structures, and response formats, refer to the reference documentation:

- `references/authentication.md` - Complete authentication guide
- `references/endpoints.md` - Full endpoint reference
- `references/modules/` - Per-module detailed documentation
- `references/data-models.md` - Response structure definitions
- `references/error-handling.md` - Error codes and handling patterns

## User Types

The API supports three user types with different permissions:

1. **Student** - Can access own grades, timetable, homework
2. **Parent** - Can access children's data (requires additional parameters)
3. **Teacher** - Can enter grades, manage classes

User type is returned in `/api/3/user` response:
```json
{
  "UserType": "student",
  "ClassId": "string"
}
```

## What to Remember

1. **Always use HTTPS** - API requires secure connections
2. **Token management** - Refresh before expiration (1 hour lifetime)
3. **School-specific URLs** - Base URL varies by school
4. **Bearer authentication** - All requests need `Authorization: Bearer` header
5. **JSON responses** - All responses are JSON format
6. **Check module availability** - Not all features enabled for all users
7. **Handle 401 gracefully** - Refresh token and retry

## Common Use Cases

When users request help with Bakalari integration, focus on:

1. **Authentication** - First step is always obtaining tokens
2. **Module selection** - Identify which endpoints needed
3. **Request structure** - Correct HTTP method and parameters
4. **Response handling** - Parse JSON response structures
5. **Error handling** - Implement retry logic for 401 errors

Generate code in whatever language the user requests (Python, JavaScript, Java, Go, etc.) based on these API principles.
