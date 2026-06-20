# Bakalari API v3 - Data Models

Common data structures and response formats used throughout the API.

## Common Objects

### Subject
```json
{
  "Id": "string",
  "Abbrev": "string",
  "Name": "string"
}
```

**Fields**:
- `Id`: Unique subject identifier
- `Abbrev`: Subject abbreviation (e.g., "MAT", "CJL")
- `Name`: Full subject name (e.g., "Matematika", "Český jazyk")

---

### Teacher
```json
{
  "Id": "string",
  "Abbrev": "string",
  "Name": "string",
  "Type": "string"
}
```

**Fields**:
- `Id`: Unique teacher identifier
- `Abbrev`: Teacher abbreviation (e.g., "NOV", "RNA")
- `Name`: Full teacher name
- `Type": Teacher type (if applicable)

---

### Class
```json
{
  "Id": "string",
  "Abbrev": "string",
  "Name": "string"
}
```

**Fields**:
- `Id`: Unique class identifier
- `Abbrev`: Class abbreviation (e.g., "1.A", "4.B")
- `Name`: Full class name

---

### Room
```json
{
  "Id": "string",
  "Abbrev": "string",
  "Name": "string"
}
```

**Fields**:
- `Id`: Unique room identifier
- `Abbrev`: Room abbreviation (e.g., "201", "GYM")
- `Name`: Full room name

---

## Module-Specific Models

### User Response (`/api/3/user`)

```json
{
  "UserId": "string",
  "UserType": "student|parent|teacher",
  "FirstName": "string",
  "LastName": "string",
  "ClassId": "string",
  "ClassName": "string",
  "SchoolId": "string",
  "SchoolName": "string",
  "Modules": [
    {
      "Name": "marks",
      "Enabled": true
    },
    {
      "Name": "timetable",
      "Enabled": true
    }
  ],
  "Semesters": [
    {
      "Id": "string",
      "Name": "string",
      "FromDate": "2025-09-01T00:00:00+02:00",
      "ToDate": "2026-01-31T23:59:59+01:00"
    }
  ]
}
```

**User Types**:
- `student`: Regular student
- `parent`: Parent/guardian (can access children's data)
- `teacher`: Teacher with elevated permissions

---

### Marks Response (`/api/3/marks`)

```json
{
  "Subjects": [
    {
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
      "Averages": {
        "Calculated": 1.5,
        "StudyAverage": 1.7,
        "RoundAverage": 2
      },
      "Marks": [
        {
          "Id": "string",
          "Date": "2025-06-15T00:00:00+02:00",
          "Mark": "1",
          "MarkText": "Výborně",
          "Weight": 1,
          "Type": "Písemka",
          "TeacherNote": "Dobrá práce",
          "Points": null,
          "MaxPoints": null,
          "IsSanction": false,
          "IsCountToAverage": true
        }
      ],
      "FinalMarks": [
        {
          "Id": "string",
          "Date": "2026-01-31T00:00:00+01:00",
          "Mark": "1",
          "MarkText": "Výborně"
        }
      ]
    }
  ]
}
```

**Grade Formats**:
- Traditional: `1`, `1-`, `2`, `2+`, `3`, `4`, `5`
- Points-based: `Points` field with number value
- Text: `MarkText` with description

**Grade Scale** (traditional):
- `1`: Excellent (Výborně)
- `1-`: Excellent minus
- `2`: Good (Chvalitebně)
- `2+`: Good plus
- `3`: Satisfactory (Dostatečně)
- `4`: Poor (Nedostatečně)
- `5`: Very poor (Nedostatečně)

---

### Timetable Response (`/api/3/timetable/actual`)

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
          "Absent": false
        }
      ],
      "IsFreeDay": false,
      "IsHoliday": false,
      "HolidayName": null
    }
  ]
}
```

**Change Types**:
- `Substitution`: Teacher substitution
- `RoomChanged`: Room changed only
- `Added`: New lesson added
- `Removed`: Lesson cancelled
- `Canceled`: Lesson cancelled
- `HourChanged`: Time changed

---

### Homework Response (`/api/3/homeworks`)

```json
{
  "Homeworks": [
    {
      "Id": "string",
      "Date": "2025-06-20T00:00:00+02:00",
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
      "Content": "Procvičit příklady str. 45",
      "EndDate": "2025-06-22T23:59:59+02:00",
      "Completed": false,
      "Attachments": [
        {
          "Id": "string",
          "Name": "cviceni.pdf",
          "Url": "https://..."
        }
      ]
    }
  ]
}
```

---

### Events Response (`/api/3/events`)

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
      "Attachments": []
    }
  ]
}
```

**Event Types**:
- `school_event`: General school event
- `exam`: Exam date
- `parent_meeting`: Parent/teacher meeting
- `holiday`: Holiday or celebration
- `other`: Other event

---

### Komens Messages Response (`/api/3/komens/messages/*`)

```json
{
  "Messages": [
    {
      "Id": "string",
      "Date": "2025-06-20T10:30:00+02:00",
      "Subject": "Zpráva rodičům",
      "Body": "Dobrý den, mám připomínku k známkám...",
      "Sender": {
        "Id": "string",
        "Name": "Novák Jan",
        "Type": "parent"
      },
      "Recipients": [
        {
          "Id": "string",
          "Name": "Novák Jan",
          "Type": "teacher"
        }
      ],
      "IsRead": false,
      "HasAttachments": true,
      "Attachments": [
        {
          "Id": "string",
          "Name": "dokument.pdf",
          "Size": 12345,
          "Url": "https://..."
        }
      ]
    }
  ],
  "UnreadCount": 3
}
```

**Message Types**:
- `received`: Messages in inbox
- `sent`: Messages sent by user
- `noticeboard`: School announcements

---

### Absence Response (`/api/3/absence/student`)

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
      "Excused": true
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
  ]
}
```

**Absence Types**:
- `ok`: Present (přítomen)
- `missed`: Absent (chybějící)
- `late`: Late (zpoždění)
- `soon`: Left early (časné odchody)
- `school`: School event absence

---

### Substitutions Response (`/api/3/substitutions`)

```json
{
  "Substitutions": [
    {
      "Date": "2025-06-20T00:00:00+02:00",
      "Hour": {
        "Id": "string",
        "Caption": "7:30 - 8:15"
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
  ]
}
```

**Substitution Types**:
- `Substitution`: Teacher substitution
- `RoomChanged`: Room changed only
- `Added`: New lesson added
- `Removed`: Lesson cancelled
- `Canceled`: Lesson cancelled
- `HourChanged`: Time changed

---

## Date Format

All dates use ISO 8601 format with timezone:
```
2025-06-20T00:00:00+02:00
```

**Components**:
- Date: `YYYY-MM-DD`
- Time: `HH:MM:SS`
- Timezone: `±HH:MM`

**Examples**:
- Full datetime: `2025-06-20T14:30:00+02:00`
- Date only: `2025-06-20T00:00:00+02:00`
- End of day: `2025-06-20T23:59:59+02:00`

---

## Empty Responses

Some endpoints return empty arrays when no data is available:
```json
{
  "Homeworks": [],
  "Events": []
}
```

This is normal and indicates no matching records.

---

## Pagination

For endpoints with large datasets, responses may include pagination:

```json
{
  "Data": [...],
  "Page": 1,
  "PageSize": 50,
  "TotalPages": 5,
  "TotalItems": 245
}
```

**Query Parameters** (when supported):
- `page`: Page number (1-based)
- `pageSize`: Items per page

---

## Attachment Structure

Attachments are referenced by ID with download URLs:

```json
{
  "Attachments": [
    {
      "Id": "string",
      "Name": "document.pdf",
      "Size": 12345,
      "Url": "https://school.cz/api/3/files/attachment-id",
      "Type": "application/pdf"
    }
  ]
}
```

Download requires authentication (Bearer token).
