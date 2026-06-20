# Bakalari API v3 - Endpoint Reference

Complete reference of all available API endpoints.

## Base URL Pattern

```
https://<school-domain>:<port>/api/3/<endpoint>
```

**Example**: `https://bakalari.gymnazium.cz:444/api/3/marks`

## Authentication Endpoints

### Login
```
POST /api/login
```

Authenticates user and returns tokens.

**Content-Type**: `application/x-www-form-urlencoded`

**Body Parameters**:
- `client_id` (required): `ANDR`
- `grant_type` (required): `password` or `refresh_token`
- `username` (required for password grant): User's username
- `password` (required for password grant): User's password
- `refresh_token` (required for refresh_token grant): Refresh token

**Response**: Token object with access_token, refresh_token, id_token

---

## Core Endpoints

### User Information
```
GET /api/3/user
```

Returns user profile, class information, and enabled modules.

**Authentication**: Required

**Response Fields**:
- User profile (name, class, type)
- School information
- Enabled modules list
- Current semester info
- User permissions

### Subjects
```
GET /api/3/subjects
```

Returns all subjects with teacher assignments.

**Authentication**: Required

**Response**: Array of subject objects with teacher information

---

## Marks/Grades Endpoints

### All Marks
```
GET /api/3/marks
```

Returns all grades and evaluations for the student.

**Authentication**: Required

**Response**: Subjects with detailed grade information including:
- Grade values (1, 1-, 2, 2+, etc. or points)
- Grade weights
- Grade types (exam, homework, classwork, etc.)
- Timestamps
- Teacher information
- Subject averages

### Final Marks
```
GET /api/3/marks/final
```

Returns final semester grades.

**Authentication**: Required

**Response**: Final grades for completed subjects

### What-If Calculator
```
POST /api/3/marks/what-if
```

Grade prediction calculator for "what-if" scenarios.

**Authentication**: Required
**Content-Type**: `application/json; charset=utf-8`

**Body**: Array of grade objects (existing + hypothetical with `Id: null`)

### Marks Measures
```
GET /api/3/marks/measures
```

Returns disciplinary measures and commendations (pedagogical measures).

**Authentication**: Required

**Response**: Array of disciplinary records and commendations

### Marks Confirmation
```
POST /api/3/marks/SetClassificationConfirmation
```

Confirms/signs selected grades.

**Authentication**: Required
**Content-Type**: `application/json`

**Body**: Array of grade IDs to confirm

---

## Timetable Endpoints

### Actual Timetable (Current Week)
```
GET /api/3/timetable/actual
GET /api/3/timetable/actual/<date>
```

Returns the timetable for the current week or specific week.

**Authentication**: Required

**Query Parameters**:
- `date` (optional): ISO date string for specific week

**Response**: Weekly timetable including:
- Lesson times and subjects
- Teacher assignments
- Room locations
- Substitutions and changes
- Holidays and free days

### Permanent Timetable
```
GET /api/3/timetable/permanent
```

Returns the permanent timetable for the entire school year.

**Authentication**: Required

**Response**: Full year timetable structure

---

## Homework Endpoints

### Homework List
```
GET /api/3/homeworks
```

Returns homework assignments.

**Authentication**: Required

**Query Parameters**:
- `from` (optional): Start date (ISO format)
- `to` (optional): End date (ISO format)

**Response**: Array of homework objects including:
- Assignment description
- Subject and teacher
- Due date
- Completion status
- Attachments

---

## Events Endpoints

### All Events
```
GET /api/3/events
```

Returns all school events.

**Authentication**: Required

**Query Parameters**:
- `from` (optional): Start date (ISO format)
- `to` (optional): End date (ISO format)

### My Events
```
GET /api/3/events/my
```

Returns user's personal events.

**Authentication**: Required

**Query Parameters**:
- `from` (optional): Start date (ISO format)
- `to` (optional): End date (ISO format)

### Public Events
```
GET /api/3/events/public
```

Returns public school events.

**Authentication**: Required

**Query Parameters**:
- `from` (optional): Start date (ISO format)
- `to` (optional): End date (ISO format)

**Event Types**:
- School events
- Exams
- Parent meetings
- Holidays
- Celebrations

---

## Komens (Messaging) Endpoints

### Received Messages
```
GET /api/3/komens/messages/received
```

Returns messages received by the user.

**Authentication**: Required

**Response**: Array of received messages with:
- Sender information
- Message body
- Timestamp
- Read/unread status
- Attachments

### Sent Messages
```
GET /api/3/komens/messages/sent
```

Returns messages sent by the user.

**Authentication**: Required

**Response**: Array of sent messages

### Noticeboard
```
GET /api/3/komens/messages/noticeboard
```

Returns school noticeboard messages.

**Authentication**: Required

**Response**: School announcements and notices

### Message Details
```
GET /api/3/komens/messages/<message_id>
```

Returns detailed information about a specific message.

**Authentication**: Required

**Parameters**:
- `message_id`: Message identifier

---

## Absence Endpoints

### Student Absence
```
GET /api/3/absence/student
```

Returns absence records for the student.

**Authentication**: Required

**Response**: Absence data including:
- Daily absence records
- Subject-specific absence statistics
- Absence types (ok, missed, late, soon, school)

---

## Substitutions Endpoints

### Substitutions List
```
GET /api/3/substitutions
```

Returns teacher substitutions and schedule changes.

**Authentication**: Required

**Query Parameters**:
- `from` (optional): Start date (ISO format)
- `to` (optional): End date (ISO format)

**Change Types**:
- `Removed` - Lesson cancelled
- `Added` - New lesson added
- `Canceled` - Lesson cancelled
- `RoomChanged` - Room changed only
- `Substitution` - Teacher substitute

---

## Additional Modules

### Student At School
```
GET /api/3/user/student-at-school
```

Returns whether student is currently present at school (access control system).

**Authentication**: Required
**Response**: Boolean (true/false)

### Payments - Class Fund
```
GET /api/3/payments/classfund/summary
```

Returns class fund balance and status.

**Authentication**: Required

```
GET /api/3/payments/classfund
```

Returns payment history grouped by month.

**Authentication**: Required

**Query Parameters**:
- `sort` (optional): `asc` or `desc`
- `search` (optional): Search string

```
GET /api/3/payments/classfund/paymentsinfo
```

Returns payment information for adding funds.

**Authentication**: Required

### GDPR
```
GET /api/3/gdpr/commissioners
```

Returns GDPR/data protection commissioner information.

**Authentication**: Required

### API Info
```
GET /api
```

Returns all available API versions.

**Authentication**: Not required

```
GET /api/3
```

Returns API version 3 information.

**Authentication**: Not required

### Campaign (Information Channel)
```
GET https://campaign.bakalari.cz/bannerinfo/{Location}/{CampaignCategoryCode}
```

Returns campaign banners and announcements.

**Authentication**: Not required

**Parameters**:
- `Location`: `mobileapp`, `LargeBanner`, or `SmallBannersPanel`
- `CampaignCategoryCode`: From `/api/3/user` response

---

## HTTP Methods

| Method | Usage | Endpoints |
|--------|-------|-----------|
| GET | Retrieve data | Most endpoints |
| POST | Submit data / Login | `/api/login` |

---

## Date Format

All dates use ISO 8601 format with timezone:
```
2025-06-20T00:00:00+02:00
```

When used in query strings, URL encode the date:
```
from=2025-06-20T00%3A00%3A00%2B02%3A00
```

---

## Common Response Patterns

### Success Response (HTTP 200)

```json
{
  "Field1": "Value1",
  "Field2": ["Array", "Values"],
  "Field3": {
    "Nested": "Object"
  }
}
```

### Error Response (HTTP 4xx/5xx)

```json
{
  "error": "error_code",
  "error_description": "Human readable message"
}
```

---

## Module Availability

Not all endpoints are available for all schools/users. Check `/api/3/user` to see enabled modules:

```json
{
  "Modules": [
    {"Name": "marks", "Enabled": true},
    {"Name": "timetable", "Enabled": true},
    {"Name": "homeworks", "Enabled": false},
    {"Name": "komens", "Enabled": true}
  ]
}
```

---

## Rate Limiting

The API may enforce rate limits:
- Recommended: 60-120 requests per minute
- Implement delays between requests (100-500ms)
- Use caching to reduce unnecessary requests

---

## Pagination

Some endpoints support pagination for large datasets. Check specific module documentation for pagination parameters.
