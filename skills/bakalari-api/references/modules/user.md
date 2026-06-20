# User Module (`/api/3/user`)

## Overview

The user module provides information about the authenticated user, including profile details, school information, and available modules.

## Endpoint

```
GET /api/3/user
```

**Authentication**: Required

## Response Structure

```json
{
  "UserId": "string",
  "UserType": "student|parent|teacher",
  "FirstName": "string",
  "LastName": "string",
  "FullName": "string",
  "ClassId": "string",
  "ClassName": "string",
  "SchoolId": "string",
  "SchoolName": "string",
  "SchoolYearId": "string",
  "SchoolYearName": "string",
  "Modules": [
    {
      "Name": "string",
      "Enabled": true
    }
  ],
  "Semesters": [
    {
      "Id": "string",
      "Name": "string",
      "FromDate": "2025-09-01T00:00:00+02:00",
      "ToDate": "2026-01-31T23:59:59+01:00",
      "Current": true
    }
  ],
  "Permissions": {
    "CanViewMarks": true,
    "CanViewTimetable": true,
    "CanViewHomework": true
  }
}
```

## Response Fields

### User Information

| Field | Type | Description |
|-------|------|-------------|
| `UserId` | string | Unique user identifier |
| `UserType` | string | User type (student, parent, teacher) |
| `FirstName` | string | User's first name |
| `LastName` | string | User's last name |
| `FullName` | string | User's full name |

### Class Information

| Field | Type | Description |
|-------|------|-------------|
| `ClassId` | string | Class identifier |
| `ClassName` | string | Class name (e.g., "1.A", "4.B") |

### School Information

| Field | Type | Description |
|-------|------|-------------|
| `SchoolId` | string | School identifier |
| `SchoolName` | string | School name |
| `SchoolYearId` | string | Current school year ID |
| `SchoolYearName` | string | School year description |

### Modules

Array of available modules:

```json
"Modules": [
  {"Name": "marks", "Enabled": true},
  {"Name": "timetable", "Enabled": true},
  {"Name": "homeworks", "Enabled": false},
  {"Name": "komens", "Enabled": true},
  {"Name": "absence", "Enabled": true},
  {"Name": "events", "Enabled": true},
  {"Name": "subjects", "Enabled": true},
  {"Name": "substitutions", "Enabled": true}
]
```

**Common Module Names**:
- `marks` - Grades and evaluations
- `timetable` - Schedule and lessons
- `homeworks` - Assignments and tasks
- `komens` - Messaging system
- `absence` - Attendance records
- `events` - School events calendar
- `subjects` - Subject catalog
- `substitutions` - Schedule changes
- `payments` - Class fund payments
- `gdpr` - GDPR consents

### Semesters

Array of academic semesters:

```json
"Semesters": [
  {
    "Id": "string",
    "Name": "1. pololetí 2025/2026",
    "FromDate": "2025-09-01T00:00:00+02:00",
    "ToDate": "2026-01-31T23:59:59+01:00",
    "Current": true
  },
  {
    "Id": "string",
    "Name": "2. pololetí 2025/2026",
    "FromDate": "2026-02-01T00:00:00+01:00",
    "ToDate": "2026-06-30T23:59:59+02:00",
    "Current": false
  }
]
```

## User Types

### Student
```json
{
  "UserType": "student",
  "ClassId": "string",
  "ClassName": "1.A"
}
```
- Can access own grades, timetable, homework
- Standard student permissions

### Parent
```json
{
  "UserType": "parent",
  "Children": [
    {
      "UserId": "string",
      "FirstName": "Jan",
      "LastName": "Novák",
      "ClassId": "string",
      "ClassName": "1.A"
    }
  ]
}
```
- Can access children's data
- May require additional parameters for some endpoints

### Teacher
```json
{
  "UserType": "teacher",
  "TeacherId": "string"
}
```
- Elevated permissions
- Can enter grades and manage classes

## Usage

### Check Module Availability

Before accessing a module, check if it's enabled:

```json
{
  "Modules": [
    {"Name": "homeworks", "Enabled": false}
  ]
}
```

If a module is disabled, avoid making requests to its endpoints.

### Get Current Semester

Use the semester marked with `"Current": true` for filtering:

```json
{
  "Semesters": [
    {
      "Id": "sem1",
      "Name": "1. pololetí",
      "Current": true,
      "FromDate": "2025-09-01T00:00:00+02:00",
      "ToDate": "2026-01-31T23:59:59+01:00"
    }
  ]
}
```

Use `FromDate` and `ToDate` to filter relevant data.

## Common Use Cases

1. **Initial authentication check** - First request after login
2. **Feature availability** - Determine which features to show UI for
3. **User profile display** - Show user name and class
4. **Date filtering** - Get current semester date range
5. **Permission check** - Verify user has required permissions

## Notes

- This endpoint should be called once per session to cache user info
- Response doesn't change frequently - cache for 24 hours
- Always check module availability before accessing endpoints
- Use `ClassName` for display, `ClassId` for API calls
