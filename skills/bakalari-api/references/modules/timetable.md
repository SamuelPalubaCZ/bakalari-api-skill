# Timetable Module (`/api/3/timetable/*`)

## Overview

The timetable module provides access to student schedules, lesson information, and permanent timetables.

## Endpoints

### Actual Timetable (Current Week)
```
GET /api/3/timetable/actual
GET /api/3/timetable/actual/<date>
```

Returns the timetable for the current week or specific week.

**Authentication**: Required

**Query Parameters**:
- `date` (optional): ISO date string to get specific week

### Permanent Timetable
```
GET /api/3/timetable/permanent
```

Returns the permanent timetable for the entire school year.

**Authentication**: Required

## Response Structure

```json
{
  "Hours": [
    {
      "Id": "string",
      "Caption": "7:30 - 8:15",
      "Order": 1
    }
  ],
  "Days": [
    {
      "Date": "2025-06-16T00:00:00+02:00",
      "DayOfWeek": 1,
      "Lessons": [
        {
          "HourId": "string",
          "Subject": {
            "Id": "string",
            "Abbrev": "MAT",
            "Name": "Matematika"
          },
          "Teacher": {
            "Id": "string",
            "Abbrev": "NOV",
            "Name": "Novák Jan"
          },
          "Room": {
            "Id": "string",
            "Abbrev": "201",
            "Name": "Třída 201"
          },
          "Class": {
            "Id": "string",
            "Abbrev": "1.A",
            "Name": "1.A"
          },
          "Change": {
            "Type": "Substitution",
            "Description": "Zastupování"
          },
          "Absent": false,
          "Theme": "Introduction to Algebra"
        }
      ],
      "IsFreeDay": false,
      "IsHoliday": false,
      "HolidayName": null
    }
  ],
  "WeekRange": {
    "FromDate": "2025-06-16T00:00:00+02:00",
    "ToDate": "2025-06-22T23:59:59+02:00"
  }
}
```

## Response Fields

### Hours (Time Slots)

| Field | Type | Description |
|-------|------|-------------|
| `Id` | string | Hour identifier |
| `Caption` | string | Time range display |
| `Order` | number | Hour order in day (1, 2, 3...) |

### Days

| Field | Type | Description |
|-------|------|-------------|
| `Date` | datetime | Date of the day |
| `DayOfWeek` | number | Day of week (1=Monday, 7=Sunday) |
| `Lessons` | array | Array of lessons for the day |
| `IsFreeDay` | boolean | If day has no school |
| `IsHoliday` | boolean | If day is a holiday |
| `HolidayName` | string | Holiday name (if applicable) |

### Lesson

| Field | Type | Description |
|-------|------|-------------|
| `HourId` | string | Reference to hour slot |
| `Subject` | object | Subject information |
| `Teacher` | object | Teacher information |
| `Room` | object | Room/location information |
| `Class` | object | Class information |
| `Change` | object | Change information (if any) |
| `Absent` | boolean | If lesson is cancelled |
| `Theme` | string | Lesson topic/theme |

## Change Types

| Type | Description | Display |
|------|-------------|---------|
| `Substitution` | Teacher substitute | Show replacement teacher |
| `RoomChanged` | Room changed only | Show new room |
| `Added` | New lesson added | Highlight as new |
| `Removed` | Lesson cancelled | Show as cancelled |
| `Canceled` | Lesson cancelled | Show as cancelled |
| `HourChanged` | Time changed | Show new time |

## Week Request

### Get Current Week
```http
GET /api/3/timetable/actual HTTP/1.1
Authorization: Bearer <token>
```

### Get Specific Week
```http
GET /api/3/timetable/actual/2025-06-23 HTTP/1.1
Authorization: Bearer <token>
```

The date can be any day within the desired week.

## Permanent Timetable

The permanent timetable shows the standard weekly schedule without substitutions:

```json
{
  "Hours": [...],
  "Days": [
    {
      "DayOfWeek": 1,
      "Lessons": [...]
    },
    {
      "DayOfWeek": 2,
      "Lessons": [...]
    }
  ]
}
```

**Differences from actual timetable**:
- No date-specific information
- No changes/substitutions
- Shows standard weekly pattern
- Repeats throughout school year

## Special Days

### Free Day
```json
{
  "Date": "2025-06-16T00:00:00+02:00",
  "IsFreeDay": true,
  "Lessons": []
}
```

### Holiday
```json
{
  "Date": "2025-12-25T00:00:00+01:00",
  "IsHoliday": true,
  "HolidayName": "1. svátek vánoční",
  "Lessons": []
}
```

## Display Considerations

### Time Grid
Display hours in a grid format:
- Columns: Days of week (Mon-Fri)
- Rows: Time slots (hours)
- Cells: Lesson information

### Change Indicators
Visual indicators for changes:
- Substitutions: Different color or icon
- Room changes: Highlight room field
- Cancelled lessons: Grayed out or strikethrough
- New lessons: Highlighted or marked

### Free Days
Show clearly in the timetable:
- Display holiday name
- Gray out entire day column
- Show no lessons

## Common Use Cases

1. **Weekly view** - Display current week's schedule
2. **Day view** - Show today's lessons only
3. **Permanent schedule** - Show standard weekly pattern
4. **Change notifications** - Alert to substitutions and changes
5. **Room finder** - Help locate classrooms
6. **Free periods** - Identify free time slots

## Caching Strategy

- **Actual timetable**: Cache for 1 hour
- **Permanent timetable**: Cache for 24 hours
- **Free days**: Cache for 1 week

## Notes

- Week starts on Monday (DayOfWeek = 1)
- Hours may vary by school (some start 7:30, others 8:00)
- Break times between hours are not in the API
- Some schools have alternating weeks (A/B weeks)
- Changes are updated daily by school administration
