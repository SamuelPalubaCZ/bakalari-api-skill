# Student At School Module (`/api/3/user/student-at-school`)

## Overview

The student at school module provides information about whether the student is currently present at school. This is part of the access control system (Přístupový systém).

## Endpoint

### Student Presence Status
```
GET /api/3/user/student-at-school
```

Returns whether the student is currently present at school.

**Authentication**: Required
**Content-Type**: `application/x-www-form-urlencoded`

## Response

**API Version**: 3.43.0+

Returns a boolean value:
```json
false
```

Or:
```json
true
```

## Response Values

| Value | Meaning |
|-------|---------|
| `true` | Student is currently at school |
| `false` | Student is not at school |

## Requirements

### Module Availability

This endpoint requires:
- **Access System Module** (Přístupový systém) enabled on the school server
- **Permission**: `CanShowStudentPresentAtSchool`

Check enabled modules in `/api/3/user` response:

```json
{
  "EnabledModules": [
    {
      "Module": "AccessSystem",
      "Rights": [
        "CanShowStudentPresentAtSchool"
      ]
    }
  ]
}
```

## Usage Example

```http
GET /api/3/user/student-at-school HTTP/1.1
Authorization: Bearer <token>
```

## Use Cases

1. **Parent checking** - Parents can verify if child arrived at school
2. **Attendance apps** - Real-time presence status
3. **Security systems** - Access control integration
4. **Automated notifications** - Alert when student leaves/arrives

## Implementation Notes

1. **Check module availability** - Verify AccessSystem is enabled
2. **Handle 403/404** - Module not available for this school
3. **Poll appropriately** - Don't poll too frequently (respect rate limits)
4. **Cache briefly** - Status can change quickly

## Error Handling

### 401 Unauthorized
```json
{
  "Message": "Authorization has been denied for this request."
}
```
Token is expired or invalid.

### 403 Forbidden
Module not available or user lacks permission.

### 405 Method Not Allowed
```json
{
  "Message": "The requested resource does not support http method 'POST'."
}
```
Wrong HTTP method (must be GET).

## Caching Strategy

- **Very short cache**: 1-2 minutes maximum
- **Real-time data**: Status changes frequently
- **Don't over-poll**: Respect API rate limits

## Notes

- Requires API version 3.43.0 or higher
- Only available if school has access control system
- Returns boolean (true/false), not detailed location
- Status is based on access control system checkpoints
- May not be available for all schools or users

## Related Modules

- **User** (`/api/3/user`) - Check module availability
- **Absence** (`/api/3/absence/student`) - Detailed absence records
- **Timetable** (`/api/3/timetable/actual`) - Expected schedule

## Technical Details

### Access Control System

The access control system (Přístupový systém) is a module that:
- Tracks student entry/exit via ID cards or other methods
- Provides real-time presence information
- May include features like:
  - Entry/exit logging
  - Cafeteria access
  - Library access
  - Room access control

This endpoint provides a simple boolean status from that system.
