# Substitutions Module (`/api/3/substitutions`)

## Overview

The substitutions module provides information about teacher substitutions, room changes, and schedule modifications.

## Endpoint

### Substitutions List
```
GET /api/3/substitutions
```

Returns substitutions and schedule changes.

**Authentication**: Required

## Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `from` | date | Optional | Start date (ISO format) |
| `to` | date | Optional | End date (ISO format) |

## Response Structure

```json
{
  "Substitutions": [
    {
      "Id": "string",
      "Date": "2025-06-20T00:00:00+02:00",
      "Hour": {
        "Id": "string",
        "Caption": "7:30 - 8:15",
        "Order": 1
      },
      "Type": "Substitution",
      "TypeText": "Zastupování",
      "Original": {
        "Teacher": {
          "Id": "string",
          "Abbrev": "NOV",
          "Name": "Novák Jan"
        },
        "Subject": {
          "Id": "string",
          "Abbrev": "MAT",
          "Name": "Matematika"
        },
        "Room": {
          "Id": "string",
          "Abbrev": "201",
          "Name": "Třída 201"
        }
      },
      "Replacement": {
        "Teacher": {
          "Id": "string",
          "Abbrev": "RNA",
          "Name": "Rybář Antonín"
        },
        "Subject": {
          "Id": "string",
          "Abbrev": "MAT",
          "Name": "Matematika"
        },
        "Room": {
          "Id": "string",
          "Abbrev": "202",
          "Name": "Třída 202"
        }
      },
      "Class": {
        "Id": "string",
        "Abbrev": "1.A",
        "Name": "1.A"
      },
      "Note": "Nemoc"
    }
  ],
  "DateRange": {
    "FromDate": "2025-06-20T00:00:00+02:00",
    "ToDate": "2025-06-27T23:59:59+02:00"
  }
}
```

## Response Fields

### Substitution Object

| Field | Type | Description |
|-------|------|-------------|
| `Id` | string | Unique substitution identifier |
| `Date` | datetime | Date of substitution |
| `Hour` | object | Time slot information |
| `Type` | string | Change type code |
| `TypeText` | string | Human-readable change type |
| `Original` | object | Original schedule info |
| `Replacement` | object | New schedule info |
| `Class` | object | Affected class |
| `Note` | string | Reason for change |

## Change Types

| Type | TypeText | Description | Example |
|------|----------|-------------|---------|
| `Substitution` | Zastupování | Teacher substitute | Different teacher |
| `RoomChanged` | Změna učebny | Room changed | Different room |
| `Added` | Doplněno | Lesson added | New lesson |
| `Removed` | Zrušeno | Lesson cancelled | No lesson |
| `Canceled` | Zrušeno | Lesson cancelled | No lesson |
| `HourChanged` | Změna hodiny | Time changed | Different time |

## Change Type Details

### Substitution
Teacher is replaced, everything else stays the same:
```json
{
  "Type": "Substitution",
  "Original": {"Teacher": "Novák"},
  "Replacement": {"Teacher": "Rybář"}
}
```

### Room Changed
Only the room is different:
```json
{
  "Type": "RoomChanged",
  "Original": {"Room": "201"},
  "Replacement": {"Room": "202"}
}
```

### Added
New lesson was added:
```json
{
  "Type": "Added",
  "Original": null,
  "Replacement": {
    "Subject": "MAT",
    "Teacher": "Novák",
    "Room": "201"
  }
}
```

### Removed/Cancelled
Lesson was cancelled:
```json
{
  "Type": "Removed",
  "Original": {
    "Subject": "MAT",
    "Teacher": "Novák",
    "Room": "201"
  },
  "Replacement": null
}
```

## Usage Examples

### Get All Substitutions
```http
GET /api/3/substitutions HTTP/1.1
Authorization: Bearer <token>
```

### Get Substitutions for Date Range
```http
GET /api/3/substitutions?from=2025-06-20T00:00:00%2B02:00&to=2025-06-27T23:59:59%2B02:00 HTTP/1.1
Authorization: Bearer <token>
```

### Get Today's Substitutions
```http
GET /api/3/substitutions?from=2025-06-20T00:00:00%2B02:00&to=2025-06-20T23:59:59%2B02:00 HTTP/1.1
Authorization: Bearer <token>
```

## Display Considerations

1. **Sort by date**: Most recent or upcoming first
2. **Group by date**: Group substitutions by day
3. **Type indicators**: Use different colors/icons for types
4. **Show changes**: Clearly show what changed
5. **Reasons**: Display note (illness, conference, etc.)

### Visual Indicators

| Type | Color/Icon | Display |
|------|------------|---------|
| Substitution | Yellow | 🔄 Show new teacher |
| Room Changed | Blue | 🚪 Show new room |
| Added | Green | ➕ New lesson |
| Removed/Cancelled | Red | ❌ Lesson cancelled |
| Hour Changed | Orange | 🕐 Time changed |

## Common Use Cases

1. **Daily overview** - Show today's substitutions
2. **Weekly view** - Show substitutions for the week
3. **By class** - Filter by specific class
4. **By teacher** - Filter by teacher
5. **Change notifications** - Alert to changes

## Caching Strategy

- **Current day**: Cache for 5 minutes
- **Upcoming**: Cache for 1 hour
- **Past**: Cache for 24 hours

## Notes

- Substitutions are updated by school administration
- Changes may occur last-minute
- Note field explains reason for change
- Substitutions may be modified or cancelled
- Some changes may not affect the user
- Date range filtering is server-side

## Empty Response

```json
{
  "Substitutions": [],
  "DateRange": {
    "FromDate": "2025-06-20T00:00:00+02:00",
    "ToDate": "2025-06-27T23:59:59+02:00"
  }
}
```

Normal when no substitutions occur in the date range.

## Related Modules

Substitutions are closely related to:
- **Timetable** - Original schedule
- **Subjects** - Subject information
- **Teachers** - Teacher information
