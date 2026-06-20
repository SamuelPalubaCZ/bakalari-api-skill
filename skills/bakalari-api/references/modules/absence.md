# Absence Module (`/api/3/absence/*`)

## Overview

The absence module provides access to student attendance records and absence statistics.

## Endpoint

### Student Absence
```
GET /api/3/absence/student
```

Returns absence records for the student.

**Authentication**: Required

## Response Structure

```json
{
  "Absence": [
    {
      "Date": "2025-06-20T00:00:00+02:00",
      "Subject": {
        "Id": "string",
        "Abbrev": "MAT",
        "Name": "Matematika"
      },
      "Type": "missed",
      "TypeText": "Chybějící",
      "Hours": 2,
      "Note": "Omluveno",
      "Excused": true,
      "LessonIds": ["id1", "id2"]
    }
  ],
  "Statistics": [
    {
      "Subject": {
        "Id": "string",
        "Abbrev": "MAT",
        "Name": "Matematika"
      },
      "Ok": 45,
      "Missed": 2,
      "Late": 1,
      "Soon": 0,
      "School": 0,
      "Total": 48
    }
  ],
  "TotalStatistics": {
    "Ok": 280,
    "Missed": 8,
    "Late": 3,
    "Soon": 1,
    "School": 2,
    "Total": 294
  }
}
```

## Response Fields

### Absence Record

| Field | Type | Description |
|-------|------|-------------|
| `Date` | datetime | Date of absence |
| `Subject` | object | Subject information |
| `Type` | string | Absence type code |
| `TypeText` | string | Human-readable absence type |
| `Hours` | number | Number of hours absent |
| `Note` | string | Absence note/comment |
| `Excused` | boolean | If absence is excused |
| `LessonIds` | array | Affected lesson IDs |

### Statistics by Subject

| Field | Type | Description |
|-------|------|-------------|
| `Subject` | object | Subject information |
| `Ok` | number | Hours present |
| `Missed` | number | Hours missed |
| `Late` | number | Late arrivals |
| `Soon` | number | Early departures |
| `School` | number | School-related absence |
| `Total` | number | Total hours |

### Total Statistics

Overall absence statistics across all subjects:
- `Ok`: Total hours present
- `Missed`: Total hours missed
- `Late`: Total late arrivals
- `Soon`: Total early departures
- `School`: Total school-related absences
- `Total`: Grand total hours

## Absence Types

| Type | TypeText | Description | Counted |
|------|---------|-------------|--------|
| `ok` | Přítomen | Present | No |
| `missed` | Chybějící | Absent | Yes |
| `late` | Zpoždění | Late arrival | Partial |
| `soon` | Časný odchod | Early departure | Partial |
| `school` | Školní akce | School event | No |

## Usage Example

```http
GET /api/3/absence/student HTTP/1.1
Authorization: Bearer <token>
```

## Display Considerations

### List View
1. **Chronological order**: Most recent first
2. **Subject grouping**: Group by subject
3. **Excused status**: Show excused/unexcused clearly
4. **Type indicators**: Use icons for different types
5. **Hours count**: Show number of hours

### Statistics View
1. **By subject**: Per-subject breakdown
2. **Overall totals**: Summary statistics
3. **Visual charts**: Bar or pie charts
4. **Attendance rate**: Calculate percentage

### Color Coding
- **Present (ok)**: Green or neutral
- **Excused absence**: Yellow or neutral
- **Unexcused absence**: Red
- **Late**: Orange
- **Early departure**: Orange

## Excused vs Unexcused

### Excused Absence
```json
{
  "Excused": true,
  "Note": "Omluveno - nemoc"
}
```
- Not counted in statistics
- Often requires parent note
- May show in audit trail

### Unexcused Absence
```json
{
  "Excused": false,
  "Note": null
}
```
- Counted in missed hours
- May affect grades
- Requires attention

## Statistics Calculation

### Attendance Rate

```
Attendance Rate = (Ok / Total) × 100%
```

Example:
```
Attendance Rate = (280 / 294) × 100% = 95.2%
```

### Absence Rate

```
Absence Rate = (Missed / Total) × 100%
```

## Common Use Cases

1. **Attendance overview** - Show attendance rate
2. **Absence list** - Display all absences
3. **Statistics** - Show by-subject breakdown
4. **Unexcused alerts** - Highlight unexcused absences
5. **Trends** - Track attendance over time

## Caching Strategy

- **Absence records**: Cache for 1 hour
- **Statistics**: Cache for 1 hour
- **Historical data**: Cache for 24 hours

## Notes

- Attendance is recorded by teachers
- Excused absences may require parent confirmation
- Late arrivals count as partial absence
- School-related absences are not negative
- Statistics may include current school year only
- Some absences may be modified later

## Empty Response

```json
{
  "Absence": [],
  "Statistics": [],
  "TotalStatistics": {
    "Ok": 0,
    "Missed": 0,
    "Late": 0,
    "Soon": 0,
    "School": 0,
    "Total": 0
  }
}
```

Normal at start of school year or if no absences recorded.
