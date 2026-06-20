# Marks Module (`/api/3/marks`)

## Overview

The marks module provides access to student grades, evaluations, and academic performance data.

## Endpoints

### All Marks
```
GET /api/3/marks
```

Returns all grades and evaluations for the student.

**Authentication**: Required

### Final Marks
```
GET /api/3/marks/final
```

Returns final semester grades.

**Authentication**: Required

### What-If Calculator
```
GET /api/3/marks/what-if
```

Grade prediction calculator for "what-if" scenarios.

**Authentication**: Required

## Response Structure

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

## Response Fields

### Subject Information

| Field | Type | Description |
|-------|------|-------------|
| `Subject.Id` | string | Subject identifier |
| `Subject.Abbrev` | string | Subject abbreviation |
| `Subject.Name` | string | Full subject name |

### Teacher Information

| Field | Type | Description |
|-------|------|-------------|
| `Teacher.Id` | string | Teacher identifier |
| `Teacher.Abbrev` | string | Teacher abbreviation |
| `Teacher.Name` | string | Full teacher name |

### Averages

| Field | Type | Description |
|-------|------|-------------|
| `Calculated` | number | Precise calculated average |
| `StudyAverage` | number | Study average (may differ) |
| `RoundAverage` | number | Rounded average for display |

### Individual Grade

| Field | Type | Description |
|-------|------|-------------|
| `Id` | string | Grade identifier |
| `Date` | datetime | When grade was assigned |
| `Mark` | string | Grade value (1-5 or points) |
| `MarkText` | string | Grade description |
| `Weight` | number | Grade weight (1-5) |
| `Type` | string | Grade type |
| `TeacherNote` | string | Optional teacher comment |
| `Points` | number | Points (if point-based) |
| `MaxPoints` | number | Maximum possible points |
| `IsSanction` | boolean | If grade is a sanction |
| `IsCountToAverage` | boolean | If included in average |

## Grade Types

### Traditional Scale

| Grade | Description | Points equivalent |
|-------|-------------|-------------------|
| 1 | Výborně (Excellent) | 1.0 |
| 1- | Excellent minus | 1.5 |
| 2 | Chvalitebně (Good) | 2.0 |
| 2+ | Good plus | 1.5 |
| 3 | Dostatečně (Satisfactory) | 3.0 |
| 4 | Nedostatečně (Poor) | 4.0 |
| 5 | Nedostatečně (Very poor) | 5.0 |

### Grade Types (Purpose)

| Type | Description |
|------|-------------|
| `Písemka` | Written test |
| `Ústní zkouška` | Oral exam |
| `Důležitá` | Important |
| `Písemná práce` | Written work |
| `Domácí úkol` | Homework |
| `Třídní práce` | Classwork |
| `Ostatní` | Other |
| `Sanction` | Sanction/Penalty |

## Grade Weights

Grades can be weighted differently:

| Weight | Meaning |
|--------|---------|
| 1 | Normal weight |
| 2 | Important (counts double) |
| 3+ | Very important (counts multiple times) |

## Point-Based Grading

Some subjects use points instead of traditional grades:

```json
{
  "Mark": "25",
  "Points": 25,
  "MaxPoints": 30,
  "MarkText": "25/30"
}
```

## Final Marks

Final semester grades:

```json
{
  "FinalMarks": [
    {
      "Id": "string",
      "Date": "2026-01-31T00:00:00+01:00",
      "Mark": "1",
      "MarkText": "Výborně",
      "SemesterId": "string"
    }
  ]
}
```

## What-If Calculator

Calculate predicted averages with hypothetical grades:

**Query Parameters** (examples):
- Subject ID
- Hypothetical grade value
- Grade weight

**Response**:
```json
{
  "PredictedAverage": 1.8,
  "CurrentAverage": 1.5,
  "Change": +0.3
}
```

## Usage Examples

### Get All Grades
```http
GET /api/3/marks HTTP/1.1
Authorization: Bearer <token>
```

### Get Final Grades
```http
GET /api/3/marks/final HTTP/1.1
Authorization: Bearer <token>
```

### Calculate What-If
```http
GET /api/3/marks/what-if?subject=<id>&grade=2&weight=2 HTTP/1.1
Authorization: Bearer <token>
```

## Display Considerations

1. **Sort by date**: Most recent grades first
2. **Group by subject**: Organize grades under each subject
3. **Show averages**: Display calculated and rounded averages
4. **Grade colors**: Use colors for visual grade representation
5. **Show weights**: Indicate important grades
6. **Filter options**: Allow filtering by grade type or date range

## Common Use Cases

1. **Grade overview** - Display all grades in a list
2. **Subject detail** - Show grades for specific subject
3. **Average calculation** - Display subject and overall averages
4. **Grade trends** - Track performance over time
5. **What-if analysis** - Predict impact of future grades

## Notes

- Grades are typically updated daily by teachers
- Cache response for 5-15 minutes
- Average calculations include only `IsCountToAverage: true` grades
- Sanctions (`IsSanction: true`) may be displayed differently
- Final grades available only after semester ends
