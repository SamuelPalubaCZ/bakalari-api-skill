# Events Module (`/api/3/events/*`)

## Overview

The events module provides access to school events, exams, and calendar information.

## Endpoints

### All Events
```
GET /api/3/events
```

Returns all school events (personal + public).

**Authentication**: Required

### My Events
```
GET /api/3/events/my
```

Returns user's personal events.

**Authentication**: Required

### Public Events
```
GET /api/3/events/public
```

Returns public school events.

**Authentication**: Required

## Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `from` | date | Optional | Start date (ISO format) |
| `to` | date | Optional | End date (ISO format) |

## Response Structure

```json
{
  "Events": [
    {
      "Id": "string",
      "Date": "2025-06-20T00:00:00+02:00",
      "EndDate": "2025-06-20T23:59:59+02:00",
      "Title": "Třídní schůzka",
      "Description": "Rodičovská schůzka",
      "Type": "school_event",
      "Location": "Třída 201",
      "Classes": [
        {
          "Id": "string",
          "Abbrev": "1.A",
          "Name": "1.A"
        }
      ],
      "Teachers": [
        {
          "Id": "string",
          "Abbrev": "NOV",
          "Name": "Novák Jan"
        }
      ],
      "IsPublic": true,
      "Attachments": [],
      "Time": "17:00",
      "EndTime": "18:30"
    }
  ]
}
```

## Response Fields

### Event Object

| Field | Type | Description |
|-------|------|-------------|
| `Id` | string | Unique event identifier |
| `Date` | datetime | Event start date |
| `EndDate` | datetime | Event end date |
| `Title` | string | Event title |
| `Description` | string | Event description |
| `Type` | string | Event type |
| `Location` | string | Event location |
| `Classes` | array | Associated classes |
| `Teachers` | array | Associated teachers |
| `IsPublic` | boolean | Public or personal event |
| `Attachments` | array | Attached files |
| `Time` | string | Start time (HH:MM) |
| `EndTime` | string | End time (HH:MM) |

## Event Types

| Type | Description | Example |
|------|-------------|---------|
| `school_event` | General school event | Assembly, celebration |
| `exam` | Exam date | Math test, physics exam |
| `parent_meeting` | Parent/teacher meeting | Class meeting |
| `holiday` | Holiday or celebration | School holiday |
| `other` | Other events | Field trip, competition |

## Usage Examples

### Get All Events
```http
GET /api/3/events HTTP/1.1
Authorization: Bearer <token>
```

### Get Personal Events
```http
GET /api/3/events/my HTTP/1.1
Authorization: Bearer <token>
```

### Get Public Events for Month
```http
GET /api/3/events/public?from=2025-06-01T00:00:00%2B02:00&to=2025-06-30T23:59:59%2B02:00 HTTP/1.1
Authorization: Bearer <token>
```

## Endpoint Differences

### `/api/3/events`
- Returns all events (personal + public)
- Most comprehensive view
- May include events not directly relevant to user

### `/api/3/events/my`
- Returns only user's personal events
- Events directly involving the user
- Smaller, more focused response

### `/api/3/events/public`
- Returns only public school events
- General school calendar
- No personal events

## Display Considerations

1. **Calendar view**: Display events in calendar format
2. **List view**: Chronological list of upcoming events
3. **Event types**: Use different colors/icons for types
4. **Personal vs public**: Visually distinguish
5. **Upcoming vs past**: Focus on future events
6. **Time display**: Show date, time, and location

## Event Categories

### By Type
- **Exams**: High importance, highlight prominently
- **Meetings**: Parent/teacher conferences
- **Holidays**: School closure dates
- **Activities**: Field trips, competitions
- **General**: Other school events

### By Audience
- **Personal**: Directly involves user
- **Class**: For user's class
- **School**: For entire school

## Common Use Cases

1. **Calendar view** - Display events in calendar format
2. **Upcoming events** - Show next 7-30 days
3. **Exam schedule** - Filter by exam type
4. **Parent meetings** - Show meeting dates
5. **School holidays** - Display school closure dates

## Caching Strategy

- **Upcoming events**: Cache for 1 hour
- **Past events**: Cache for 24 hours
- **Public events**: Cache for 1 hour

## Notes

- Events are created by school administration
- Exam dates may be added by teachers
- Personal events include user-specific activities
- Public events visible to all school members
- Date filtering is server-side
- Events may be updated or cancelled

## Empty Response

```json
{
  "Events": []
}
```

Normal when no events match the date range.

## All-Day Events

Events without specific time:

```json
{
  "Date": "2025-06-20T00:00:00+02:00",
  "EndDate": "2025-06-20T23:59:59+02:00",
  "Time": null,
  "EndTime": null
}
```

Display as "all-day" events in calendar.
