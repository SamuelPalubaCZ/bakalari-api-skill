# Marks Count New Module (`/api/3/marks/count-new`)

## Overview

The marks count new module returns the number of new/unviewed grades.

## Endpoint

### Count New Grades
```
GET /api/3/marks/count-new
```

Returns the number of new grades that haven't been viewed yet.

**Authentication**: Required
**Content-Type**: `application/x-www-form-urlencoded`

## Response

Returns a number representing the count of new grades.

```http
200 OK
0
```

## Behavior

- Returns `0` if no new grades
- Returns positive number for new grades
- Count resets after grades are viewed in the app
- May be related to push notification state

## Usage Example

```http
GET /api/3/marks/count-new HTTP/1.1
Authorization: Bearer <token>
```

## Use Cases

1. **Badge count** - Show number of new grades
2. **Notification** - Alert for new grades
3. **Quick check** - Check if there are new grades without fetching all

## Common Use Cases

1. **UI badges** - Display new grades count in app icon
2. **Notifications** - Trigger notifications for new grades
3. **Dashboard** - Show new grades status on dashboard

## Caching Strategy

- **Brief cache**: Cache for 2-5 minutes
- **Real-time data**: Count changes when grades are added

## Notes

- Typically returns 0 unless there are unviewed grades
- Related to push notification system
- Count resets after viewing grades in mobile app
- May require specific timing to catch non-zero values
- Useful for notification badges
