# Subjects Module (`/api/3/subjects`)

## Overview

The subjects module provides a catalog of all subjects with teacher assignments and contact information.

## Endpoint

### All Subjects
```
GET /api/3/subjects
```

Returns all subjects with teacher assignments.

**Authentication**: Required

## Response Structure

```json
{
  "Subjects": [
    {
      "Id": "string",
      "Abbrev": "MAT",
      "Name": "Matematika",
      "Teachers": [
        {
          "Id": "string",
          "Abbrev": "NOV",
          "Name": "Novák Jan",
          "Email": "novak@school.cz",
          "Phone": "+420 123 456 789",
          "Room": "201"
        }
      ]
    }
  ]
}
```

## Response Fields

### Subject Object

| Field | Type | Description |
|-------|------|-------------|
| `Id` | string | Unique subject identifier |
| `Abbrev` | string | Subject abbreviation |
| `Name` | string | Full subject name |
| `Teachers` | array | Teachers assigned to subject |

### Teacher Object

| Field | Type | Description |
|-------|------|-------------|
| `Id` | string | Teacher identifier |
| `Abbrev` | string | Teacher abbreviation |
| `Name` | string | Full teacher name |
| `Email` | string | Teacher email (optional) |
| `Phone` | string | Contact phone (optional) |
| `Room` | string | Default classroom (optional) |

## Usage Example

```http
GET /api/3/subjects HTTP/1.1
Authorization: Bearer <token>
```

## Subject Abbreviations

Common Czech subject abbreviations:

| Abbrev | Subject |
|--------|---------|
| CJL | Český jazyk a literatura |
| MAT | Matematika |
| ANJ | Anglický jazyk |
| NJM | Německý jazyk |
| FRA | Francouzský jazyk |
| P RJ | Ruský jazyk |
| ZSV | Základy společenských věd |
| DĚP | Dějepis |
| VLR | Vlastivěda |
| PRV | Přírodopis |
| FYZ | Fyzika |
| CHM | Chemie |
| ZEM | Zeměpis |
| BIO | Biologie |
| ZPV | Základy výpočetní techniky |
| HUV | Hudební výchova |
| VJV | Výtvarná výchova |
| TIK | Tělesná výchova |
| PČ | Pracovní činnosti |

## Display Considerations

1. **Alphabetical order**: Sort by name or abbreviation
2. **Group by category**: Group related subjects
3. **Teacher cards**: Show teacher information
4. **Contact options**: Display email and phone
5. **Room location**: Show default classroom

## Common Use Cases

1. **Subject catalog** - Display all subjects
2. **Teacher lookup** - Find teachers by subject
3. **Contact information** - Get teacher contacts
4. **Subject details** - Show subject information
5. **Timetable reference** - Cross-reference with timetable

## Caching Strategy

- **Subject list**: Cache for 7 days (rarely changes)
- **Teacher assignments**: Cache for 1 day

## Notes

- Subject information is relatively static
- Teacher assignments may change yearly
- Contact information may be restricted
- Email and phone are optional fields
- Room is default location, may vary

## Empty Response

Rare, but possible:

```json
{
  "Subjects": []
}
```

This would indicate no subjects are configured (unlikely).
