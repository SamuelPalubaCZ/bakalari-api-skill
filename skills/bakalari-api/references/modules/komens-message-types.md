# Komens Message Types Module (`/api/3/komens/message-types`)

## Overview

The message types module provides information about available message types for sending messages through the Komens system. It returns the types of accounts that can receive messages and the corresponding recipients (teachers).

## Endpoint

### Get Message Types
```
GET /api/3/komens/message-types
```

Returns all available message types and their recipient options.

**Authentication**: Required
**Content-Type**: `application/json`

## Response Structure

```json
{
  "MessageTypes": [
    {
      "Abbreviation": "OBECNA",
      "Name": "Obecná zpráva",
      "ShowEmailNotification": false,
      "HasTitle": true,
      "RecipientsTypes": [
        {
          "Code": "U",
          "Name": "učitel",
          "ShowCopyForClassTeacher": false,
          "ShowCopyForDirector": false,
          "ShowCopyForParent": false,
          "TypeOfSelection": "ONE",
          "Recipients": [
            {
              "Code": "AABBC",
              "IsDefault": false,
              "Abbreviation": "",
              "DisplayName": "Jan Novák Ing.",
              "Name": "Jan Novák Ing."
            }
          ]
        },
        {
          "Code": "UV",
          "Name": "učitelé - všichni",
          "ShowCopyForClassTeacher": false,
          "ShowCopyForDirector": false,
          "ShowCopyForParent": false,
          "TypeOfSelection": "ZERO",
          "Recipients": []
        }
      ],
      "SuperType": "Message",
      "ShowConfirmation": true
    },
    {
      "Abbreviation": "OMLUVENKA",
      "Name": "Omluvení absence",
      "ShowEmailNotification": false,
      "HasTitle": false,
      "RecipientsTypes": [
        {
          "Code": "U",
          "Name": "učitel",
          "ShowCopyForClassTeacher": false,
          "ShowCopyForDirector": false,
          "ShowCopyForParent": false,
          "TypeOfSelection": "ONE",
          "Recipients": [
            {
              "Code": "AABBC",
              "IsDefault": true,
              "Abbreviation": "",
              "DisplayName": "Jan Novák Ing.",
              "Name": "Jan Novák Ing."
            }
          ]
        }
      ],
      "SuperType": "Message",
      "ShowConfirmation": false
    }
  ],
  "Recipients": [
    {
      "Name": "Jan Novák Ing.",
      "Abbreviation": "",
      "Code": "AABBC",
      "DisplayName": "Jan Novák Ing."
    }
  ]
}
```

## Response Fields

### Message Type Object

| Field | Type | Description |
|-------|------|-------------|
| `Abbreviation` | string | Message type code |
| `Name` | string | Human-readable type name |
| `ShowEmailNotification` | boolean | If email notification option is available |
| `HasTitle` | boolean | If message title field is used |
| `RecipientsTypes` | array | Available recipient types |
| `SuperType` | string | Category (usually "Message") |
| `ShowConfirmation` | boolean | If read confirmation is available |

### Recipient Type Object

| Field | Type | Description |
|-------|------|-------------|
| `Code` | string | Recipient type code |
| `Name` | string | Human-readable type name |
| `ShowCopyForClassTeacher` | boolean | If class teacher copy option shown |
| `ShowCopyForDirector` | boolean | If director copy option shown |
| `ShowCopyForParent` | boolean | If parent copy option shown |
| `TypeOfSelection` | string | Selection type ("ONE", "ZERO", "MULTI") |
| `Recipients` | array | Available recipients |

### Recipient Object

| Field | Type | Description |
|-------|------|-------------|
| `Code` | string | Recipient identifier |
| `IsDefault` | boolean | If this is the default recipient |
| `Abbreviation` | string | Recipient abbreviation |
| `DisplayName` | string | Display name |
| `Name` | string | Full name |

### Global Recipients

The `Recipients` array at the end contains all recipients at the school.

## Selection Types

| Type | Meaning | Description |
|------|---------|-------------|
| `ONE` | Single selection | Must select exactly one recipient |
| `ZERO` | No selection | No recipients needed |
| `MULTI` | Multiple selection | Can select multiple recipients |

## Common Message Types

### OBECNA (General Message)
- `HasTitle`: true
- `ShowConfirmation`: true
- Can send to individual teachers or all teachers

### OMLUVENKA (Absence Excuse)
- `HasTitle`: false
- `ShowConfirmation`: false
- Typically sends to class teacher

## Usage Example

```http
GET /api/3/komens/message-types HTTP/1.1
Authorization: Bearer <token>
```

## Use Cases

1. **Get available types** - Determine what message types are available
2. **Get recipients** - Find available recipients for each type
3. **Form validation** - Know what fields to show in UI
4. **Default selection** - Find default recipient for type

## Implementation Notes

1. **Call before sending** - Get types before using `/api/3/komens/message`
2. **Use recipient codes** - Use `Code` field when sending messages
3. **Check HasTitle** - Some message types don't allow titles
4. **Handle selection types** - ONE requires exactly one recipient

## Common Use Cases

1. **Message composition UI** - Build form based on available types
2. **Recipient selection** - Show available recipients
3. **Message validation** - Ensure correct field usage
4. **Type selection** - Allow user to choose message type

## Caching Strategy

- **Cache message types**: Cache for 1 hour
- **Rarely changes**: Message types are stable

## Notes

- Message types determine available fields and recipients
- Recipient codes are used when sending messages
- HasTitle affects whether Title field is allowed
- ShowConfirmation affects confirmation behavior
- Global recipients list is for reference/completion

## Related Modules

- **Komens Send** (`/api/3/komens/message`) - Send messages
- **Received Messages** (`/api/3/komens/messages/received`) - View messages
