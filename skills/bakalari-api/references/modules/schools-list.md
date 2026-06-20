# Schools List Module

## Overview

The schools list module provides access to a public API for discovering Bakalari schools and their URLs. This is useful for finding the correct base URL for a school.

## Base URL

```
https://sluzby.bakalari.cz/api/v1
```

**Authentication**: Not required (public API)

## Endpoints

### Get Municipalities List
```
GET https://sluzby.bakalari.cz/api/v1/municipality
Accept: application/json
```

Returns a list of all municipalities with their school counts.

**Response**:
```json
[
  {
    "name": "",
    "schoolCount": 2
  },
  {
    "name": "Albrechtice",
    "schoolCount": 1
  },
  {
    "name": "Aš",
    "schoolCount": 3
  }
]
```

### Get Schools in Municipality
```
GET https://sluzby.bakalari.cz/api/v1/municipality/{municipality}
Accept: application/json
```

Returns all schools in a specific municipality.

**URL Parameters**:
- `municipality`: Municipality name (URL-encoded)

**Example**: `Benešov u Prahy` → `Bene%C5%A1ov%20u%20Prahy`

**Note**: For municipalities with dots (e.g., `Ostrava-Mar.Hory`), use only the part before the dot (`Ostrava-Mar`).

**Response**:
```json
{
  "name": "beroun",
  "schools": [
    {
      "id": "SYHKTAAAAB",
      "name": "Mateřská škola Montessori Beroun a Základní škola s.r.o.",
      "schoolUrl": "https://montessori-beroun.bakalari.cz"
    },
    {
      "id": "SYDATAAABA",
      "name": "Základní škola, Beroun - Závodí, Komenského 249",
      "schoolUrl": "https://zavodi.bakalari.cz"
    }
  ]
}
```

## Response Fields

### Municipality Object
| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Municipality name |
| `schoolCount` | number | Number of schools in municipality |

### School Object
| Field | Type | Description |
|-------|------|-------------|
| `id` | string | School identifier |
| `name` | string | Full school name |
| `schoolUrl` | string | School's Bakalari base URL |

## Usage Examples

### Get All Municipalities
```http
GET https://sluzby.bakalari.cz/api/v1/municipality HTTP/1.1
Accept: application/json
```

### Get Schools in City
```http
GET https://sluzby.bakalari.cz/api/v1/municipality/beroun HTTP/1.1
Accept: application/json
```

### With Special Characters
```http
GET https://sluzby.bakalari.cz/api/v1/municipality/Bene%C5%A1ov%20u%20Prahy HTTP/1.1
Accept: application/json
```

## XML Format

Without `Accept: application/json`, the API returns XML format:

```xml
<ArrayOfmunicipalityInfo>
  <municipalityInfo>
    <name>Albrechtice</name>
    <schoolCount>1</schoolCount>
  </municipalityInfo>
</ArrayOfOfmunicipalityInfo>
```

## Common Use Cases

1. **School discovery** - Find schools by municipality
2. **URL lookup** - Get the base URL for a school
3. **School selection** - Build school selection UI
4. **Configuration** - Discover school URLs for app setup

## Implementation Notes

1. **No authentication** - This is a public API
2. **Rate limiting** - Be respectful with request frequency
3. **URL encoding** - Properly encode municipality names
4. **Handle dots** - Remove dots from municipality names
5. **Cache results** - School data rarely changes

## Caching Strategy

- **Municipalities list**: Cache for 1 week
- **Schools by municipality**: Cache for 1 week
- **Public data**: Safe to cache aggressively

## Notes

- This is a separate service from the main API
- Hosted on `sluzby.bakalari.cz`
- Returns both JSON and XML formats
- School URL is the base for API calls
- Municipality names require URL encoding
- Schools with dots in names use special handling
- Data is relatively stable but may update annually

## Related Information

- **School URL** - Use `schoolUrl` as base for API calls
- **Authentication** - Required for main API, not for school list
- **API Version** - Main API is at `/api/3/` on school URL
