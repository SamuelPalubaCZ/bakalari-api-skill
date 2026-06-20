# Marks Measures Module (`/api/3/marks/measures`)

## Overview

The marks measures module provides access to disciplinary measures and commendations (pedagogical measures) recorded throughout the student's studies. This includes both positive (commendations) and negative (disciplinary actions) records.

## Endpoint

### Pedagogical Measures
```
GET /api/3/marks/measures
```

Returns all disciplinary measures and commendations for the entire study period.

**Authentication**: Required

## Response Structure

```json
{
  "PedagogicalMeasures": [
    {
      "SchoolYear": "2018/19",
      "Semester": "2",
      "TypeLabel": "důtka třídního učitele",
      "Date": "2019-02-18T00:00:00+01:00",
      "TypeId": "dTU",
      "Text": "Důtka třídního učitele za nevhodné chování během vyučování"
    },
    {
      "SchoolYear": "2017/18",
      "Semester": "2",
      "TypeLabel": "pochvala třídního učitele",
      "Date": "2018-04-16T00:00:00+02:00",
      "TypeId": "pTU",
      "Text": "za účast v olympiádách z odborných předmětů"
    }
  ]
}
```

## Response Fields

### Pedagogical Measure Object

| Field | Type | Description |
|-------|------|-------------|
| `SchoolYear` | string | School year (e.g., "2018/19") |
| `Semester` | string | Semester ("1", "2") |
| `TypeLabel` | string | Human-readable type description |
| `Date` | datetime | When measure was recorded |
| `TypeId` | string | Type identifier code |
| `Text` | string | Detailed description/reason |

## Measure Types

### Positive Measures (Commendations)

| TypeId | TypeLabel | Description |
|--------|-----------|-------------|
| `pTU` | pochvala třídního učitele | Class teacher commendation |
| `pŘ` | pochvala ředitele | Principal commendation |
| `pO` | pochvala ostatní | Other commendation |

### Negative Measures (Disciplinary Actions)

| TypeId | TypeLabel | Description |
|--------|-----------|-------------|
| `dTU` | důtka třídního učitele | Class teacher reprimand |
| `dŘ` | důtka ředitele | Principal reprimand |
| `dO` | důtka ostatní | Other reprimand |
| `vT` | vyloučení z třídy | Exclusion from class |
| `vŠ` | vyloučení ze školy | Expulsion from school |

## Usage Example

```http
GET /api/3/marks/measures HTTP/1.1
Authorization: Bearer <token>
```

## Display Considerations

1. **Separate positive/negative**: Group commendations separately from disciplinary actions
2. **Chronological order**: Most recent first
3. **Type indicators**: Use icons or colors for different types
4. **School year grouping**: Group by academic year
5. **Text display**: Show full description text

### Color Coding

| Type | Color | Icon |
|------|-------|------|
| Commendations | Green | 🏆 |
| Reprimands | Orange | ⚠️ |
| Exclusions | Red | ❌ |

## Common Use Cases

1. **Behavior record** - View all disciplinary history
2. **Achievement showcase** - Display commendations
3. **Parent meetings** - Discuss behavior patterns
4. **School records** - Official behavior documentation
5. **Trend analysis** - Track behavior over time

## Caching Strategy

- **Complete history**: Cache for 24 hours
- **Historical data**: Rarely changes

## Notes

- Returns complete history from beginning of studies
- Includes both positive and negative measures
- Text descriptions provide context and reasons
- TypeId codes are used for categorization
- Data is organized by school year and semester
- Measures are recorded by teachers or administration
- May affect eligibility for certain school activities

## Measure Impact

### Commendations
- May be considered for awards
- Can be mentioned in recommendations
- Positive for applications

### Disciplinary Actions
- May affect eligibility for activities
- Could impact recommendations
- Serious measures may have long-term consequences

## Related Modules

- **Marks** (`/api/3/marks`) - Academic grades
- **Absence** (`/api/3/absence/student`) - Attendance records
- **User** (`/api/3/user`) - Student profile
