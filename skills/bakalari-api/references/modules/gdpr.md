# GDPR Module (`/api/3/gdpr`)

## Overview

The GDPR (General Data Protection Regulation) module provides information about data protection commissioners and privacy contacts at the school.

## Endpoint

### Data Protection Commissioners
```
GET /api/3/gdpr/commissioners
```

Returns information about GDPR/data protection commissioners.

**Authentication**: Required
**Content-Type**: `application/x-www-form-urlencoded`

## Response Structure

```json
{
  "Commissioners": [
    {
      "Id": "ABC01",
      "Name": "Jméno Příjmení",
      "Mobile": "123 456 789",
      "Phone": "",
      "Email": "gdpr@gdpr.gdpr",
      "Web": "gdpr.com",
      "DataBox": ""
    }
  ]
}
```

## Response Fields

### Commissioner Object

| Field | Type | Description |
|-------|------|-------------|
| `Id` | string | Commissioner identifier |
| `Name` | string | Full name |
| `Mobile` | string | Mobile phone number |
| `Phone` | string | Office phone number |
| `Email` | string | Email address |
| `Web` | string | Website URL |
| `DataBox` | string | Data box identifier (Czech datová schránka) |

## Usage Example

```http
GET /api/3/gdpr/commissioners HTTP/1.1
Authorization: Bearer <token>
```

## Display Considerations

1. **Contact list**: Display as a contact list
2. **Multiple contacts**: May have more than one commissioner
3. **Contact options**: Show all available contact methods
4. **Privacy focus**: This is GDPR-related data

## Common Use Cases

1. **Privacy inquiries** - Contact for privacy questions
2. **Data requests** - GDPR data access requests
3. **Consent management** - Privacy consent questions
4. **Official contacts** - School privacy officers

## Caching Strategy

- **Commissioner data**: Cache for 7 days
- **Rarely changes**: Contact information is stable

## Notes

- Commissioners are the school's GDPR representatives
- Contact the commissioner for privacy-related matters
- May include multiple commissioners for different roles
- Data box is used for official Czech government communication
- This module may not be available for all schools
- Required by GDPR compliance for Czech schools

## Related Information

- **GDPR compliance**: Schools must designate data protection officers
- **Student data**: Commissioners handle student privacy matters
- **Parental rights**: Contact for parental data access requests
