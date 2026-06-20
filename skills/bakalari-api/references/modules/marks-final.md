# Final Marks Module (`/api/3/marks/final`)

## Overview

The final marks module provides access to historical report cards and final grades from all semesters throughout the student's academic career. This is different from current grades - these are the finalized grades that appear on report cards.

## Endpoint

### Final Report Cards
```
GET /api/3/marks/final
```

Returns all final grades and report cards from the beginning of studies.

**Authentication**: Required

## Response Structure

```json
{
  "CertificateTerms": [
    {
      "FinalMarks": [
        {
          "MarkDate": "2008-01-24T20:56:00+01:00",
          "EditDate": "2008-01-24T20:56:00+01:00",
          "MarkText": "1",
          "SubjectId": "26",
          "Id": "O100("
        },
        {
          "MarkDate": "2008-01-24T20:56:00+01:00",
          "EditDate": "2008-01-24T20:56:00+01:00",
          "MarkText": "2",
          "SubjectId": "28",
          "Id": "O101."
        }
      ],
      "Subjects": [
        {
          "Id": "26",
          "Abbrev": "Chv",
          "Name": "Chování"
        },
        {
          "Id": "28",
          "Abbrev": "ČJL",
          "Name": "Český jazyk a literatura"
        },
        {
          "Id": "10",
          "Abbrev": "M",
          "Name": "Matematika"
        }
      ],
      "GradeName": "první",
      "Grade": 1,
      "YearInSchool": 1,
      "SchoolYear": "2017/18",
      "Semester": "1",
      "SemesterName": "1.",
      "Repeated": false,
      "Closed": true,
      "AchievementText": "prospěl s vyznamenáním",
      "MarksAverage": 1.23,
      "AbsentHours": 23,
      "NotExcusedHours": 0,
      "CertificateDate": "2018-01-31T00:00:00+01:00"
    },
    {
      "FinalMarks": [
        {
          "MarkDate": "2018-06-22T13:50:00+02:00",
          "EditDate": "2018-06-22T13:50:00+02:00",
          "MarkText": "1",
          "SubjectId": "26",
          "Id": "O100)"
        }
      ],
      "Subjects": [...],
      "GradeName": "první",
      "Grade": 1,
      "YearInSchool": 1,
      "SchoolYear": "2017/18",
      "Semester": "2",
      "SemesterName": "2.",
      "Repeated": false,
      "Closed": true,
      "AchievementText": "prospěl s vyznamenáním",
      "MarksAverage": 1.23,
      "AbsentHours": 11,
      "NotExcusedHours": 0,
      "CertificateDate": "2018-06-29T00:00:00+02:00"
    }
  ]
}
```

## Response Fields

### Certificate Term Object

| Field | Type | Description |
|-------|------|-------------|
| `FinalMarks` | array | Array of final grade objects |
| `Subjects` | array | Array of subject information |
| `GradeName` | string | Grade level name (e.g., "první", "druhý") |
| `Grade` | number | Grade level number (1, 2, 3...) |
| `YearInSchool` | number | Year in school |
| `SchoolYear` | string | School year (e.g., "2017/18") |
| `Semester` | string | Semester number ("1", "2") |
| `SemesterName` | string | Semester name ("1.", "2.") |
| `Repeated` | boolean | If semester was repeated |
| `Closed` | boolean | If term is closed/finalized |
| `AchievementText` | string | Achievement description |
| `MarksAverage` | number | Grade average for the term |
| `AbsentHours` | number | Total absent hours |
| `NotExcusedHours` | number | Unexcused absent hours |
| `CertificateDate` | datetime | Date report card issued |

### Final Mark Object

| Field | Type | Description |
|-------|------|-------------|
| `MarkDate` | datetime | When grade was assigned |
| `EditDate` | datetime | When grade was last edited |
| `MarkText` | string | Grade value (1-5) |
| `SubjectId` | string | Subject identifier |
| `Id` | string | Unique grade identifier |

## Achievement Texts (Czech)

| Text | English |
|------|---------|
| `prospěl s vyznamenáním` | Passed with distinction |
| `prospěl` | Passed |
| `neprospěl` | Failed |
| `podmínečně propuštěn` | Conditionally released |

## Grade Names (Czech)

| Czech | English |
|-------|---------|
| `první` | First (1st grade) |
| `druhý` | Second (2nd grade) |
| `třetí` | Third (3rd grade) |
| `čtvrtý` | Fourth (4th grade) |
| `pátý` | Fifth (5th grade) |
| `šestý` | Sixth (6th grade) |
| `sedmý` | Seventh (7th grade) |
| `osmý` | Eighth (8th grade) |
| `devátý` | Ninth (9th grade) |

## Usage Example

```http
GET /api/3/marks/final HTTP/1.1
Authorization: Bearer <token>
```

## Display Considerations

1. **Group by school year**: Organize by academic year
2. **Show both semesters**: Display 1st and 2nd semester together
3. **Achievement highlight**: Emphasize the achievement text
4. **Grade progression**: Show how grades changed over years
5. **Attendance summary**: Include absent hours
6. **Historical view**: This is historical data, not current grades

## Common Use Cases

1. **Academic history** - View all past report cards
2. **Grade trends** - Track performance over years
3. **Official records** - Access final grades for applications
4. **Parent meetings** - Show complete academic history
5. **Archive purposes** - Historical documentation

## Caching Strategy

- **Complete history**: Cache for 24 hours
- **Historical data**: Rarely changes once finalized

## Notes

- This returns historical data from completed semesters
- Data is finalized and doesn't change (except corrections)
- Includes all semesters from beginning of studies
- Certificate date indicates when report card was issued
- Average is calculated from final grades only
- Absence hours are totals for the entire semester
- Achievement text indicates overall performance

## Differences from Current Marks

| Feature | Current Marks | Final Marks |
|---------|---------------|--------------|
| Endpoint | `/api/3/marks` | `/api/3/marks/final` |
| Data | Current semester grades | All historical grades |
| Updates | Can change | Finalized (rarely changes) |
| Subject info | Included in response | Included in response |
| Averages | Calculated | Calculated |
| Absence | Not included | Included (hours) |
| Confirmation | May need confirmation | Already confirmed |

## Related Modules

- **Marks** (`/api/3/marks`) - Current semester grades
- **Absence** (`/api/3/absence/student`) - Detailed attendance records
