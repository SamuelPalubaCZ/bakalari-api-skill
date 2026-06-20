# Subject Themes Module (`/api/3/subjects/themes`)

## Overview

The subject themes module returns a list of lesson themes for a specific subject throughout the school year.

## Endpoint

### Get Subject Themes
```
GET /api/3/subjects/themes/{subject_id}
```

Returns all lesson themes for the specified subject.

**Authentication**: Required
**Content-Type**: `application/x-www-form-urlencoded`

## URL Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `subject_id` | string | Subject identifier (URL-encoded if needed) |

**Note**: Spaces in subject_id should be replaced with `%20`.

## Response Structure

```json
{
  "Subject": {
    "Id": "28",
    "Abbrev": "ČJL",
    "Name": "Český jazyk a literatura"
  },
  "Themes": [
    {
      "Date": "2019-09-03T00:00:00+02:00",
      "Theme": "Světový realismus",
      "Note": "",
      "HourCaption": "1. hod",
      "LessonLabel": "1"
    },
    {
      "Date": "2019-09-05T00:00:00+02:00",
      "Theme": "D a VJR",
      "Note": "",
      "HourCaption": "6. hod",
      "LessonLabel": "2"
    }
  ]
}
```

## Response Fields

### Subject Object
| Field | Type | Description |
|-------|------|-------------|
| `Id` | string | Subject identifier |
| `Abbrev` | string | Subject abbreviation |
| `Name` | string | Full subject name |

### Theme Object
| Field | Type | Description |
|-------|------|-------------|
| `Date` | datetime | Date of the lesson |
| `Theme` | string | Lesson theme/topic |
| `Note` | string | Additional notes |
| `HourCaption` | string | Hour caption (e.g., "1. hod") |
| `LessonLabel` | string | Lesson number/label |

## Usage Example

```http
GET /api/3/subjects/themes/28 HTTP/1.1
Authorization: Bearer <token>
```

### With URL Encoding (if spaces in ID)
```http
GET /api/3/subjects/themes/ABC%20123 HTTP/1.1
Authorization: Bearer <token>
```

## Use Cases

1. **Curriculum overview** - View topics covered throughout year
2. **Lesson planning** - See what themes are planned
3. **Study review** - Review what topics were covered
4. **Absentee planning** - Plan missed lessons
5. **Exam preparation** - Review themes for exam topics

## Common Use Cases

1. **Study planning** - Plan study based on themes
2. **Catch-up work** - Find themes for missed lessons
3. **Year overview** - See full year curriculum
4. **Subject detail** - Detailed view of subject content

## Caching Strategy

- **Full year data**: Cache for 1 day
- **Subject-specific**: Cache per subject
- **Updates**: Teachers may update themes

## Notes

- Returns themes for entire school year
- Organized chronologically by date
- Themes are entered by teachers
- May be empty if no themes entered
- Useful for curriculum planning
- Can help with exam preparation

## Related Modules

- **Subjects** (`/api/3/subjects`) - Get subject list
- **Timetable** (`/api/3/timetable/actual`) - Lesson schedule
