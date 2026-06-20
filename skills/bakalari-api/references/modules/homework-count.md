# Homework Count Module (`/api/3/homeworks/count-actual`)

## Overview

The homework count module returns the number of current/actual homework assignments.

## Endpoint

### Count Actual Homework
```
GET /api/3/homeworks/count-actual
```

Returns the number of current homework assignments.

**Authentication**: Required
**Content-Type**: `application/x-www-form-urlencoded`

## Response

Returns a number representing the count of actual homework assignments.

```http
200 OK
5
```

## Usage Example

```http
GET /api/3/homeworks/count-actual HTTP/1.1
Authorization: Bearer <token>
```

## Use Cases

1. **Badge count** - Show number of pending homework
2. **Notification** - Alert for new homework
3. **Quick overview** - Show homework count without fetching all

## Common Use Cases

1. **UI badges** - Display homework count in app icon
2. **Dashboard widgets** - Show quick homework status
3. **Notifications** - Trigger notifications for new homework

## Caching Strategy

- **Brief cache**: Cache for 5 minutes
- **Real-time data**: Count can change frequently

## Notes

- Returns a simple number, not detailed homework
- Count represents current/active homework only
- Useful for badges and notifications
- Faster than fetching full homework list
