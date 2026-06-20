# Komens Send Module (`/api/3/komens/message`)

## Overview

The Komens send module allows sending new messages through the Bakalari messaging system.

## Endpoint

### Send Message
```
POST /api/3/komens/message
```

Sends a new message to specified recipients.

**Authentication**: Required
**Content-Type**: `application/json; charset=utf-8`

## Request Body

```json
{
  "MessageType": "OBECNA",
  "Title": "Nadpis",
  "Text": "Text zprávy",
  "RecipientType": "U",
  "Recipients": ["AABBC"],
  "Lifetime": null,
  "DateFrom": null,
  "DateTo": null,
  "PreviousMessageId": null,
  "CopyForClassTeacher": false,
  "CopyForParent": false,
  "EmailNotification": false,
  "SendAsDirector": false,
  "RequireConfirmation": true,
  "TypeOfRatingId": null,
  "Scale": null,
  "Attachments": [],
  "DraftDate": null
}
```

## Request Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `MessageType` | string | Yes | Message type code (from message-types) |
| `Title` | string | Conditional | Message title (if HasTitle=true) |
| `Text` | string | Yes | Message body text |
| `RecipientType` | string | Yes | Recipient type code |
| `Recipients` | array | Yes | Array of recipient IDs |
| `Lifetime` | datetime | Optional | Message expiration |
| `DateFrom` | datetime | Optional | Message start date |
| `DateTo` | datetime | Optional | Message end date |
| `PreviousMessageId` | string | Optional | For reply/forward |
| `CopyForClassTeacher` | boolean | Optional | Copy to class teacher |
| `CopyForParent` | boolean | Optional | Copy to parent |
| `EmailNotification` | boolean | Optional | Send email notification |
| `SendAsDirector` | boolean | Optional | Send as director |
| `RequireConfirmation` | boolean | Optional | Require read confirmation |
| `TypeOfRatingId` | string | Optional | For message types with rating |
| `Scale` | number | Optional | Rating scale |
| `Attachments` | array | Optional | Attachment IDs |
| `DraftDate` | datetime | Optional | Draft date |

## Common Message Types

| Type | Name | HasTitle | Description |
|------|------|----------|-------------|
| `OBECNA` | Obecná zpráva | true | General message |
| `OMLUVENKA` | Omluvení absence | false | Absence excuse |

## Recipient Types

| Code | Name | Description |
|------|------|-------------|
| `U` | učitel | Teacher |
| `UV` | učitelé - všichni | All teachers |

## Response

### Success (HTTP 200)

```json
{
  "Message": ""
}
```

Returns an empty response on success.

## Error Handling

### 401 Unauthorized
```json
{
  "Message": "Authorization has been denied for this request."
}
```
Token is expired or invalid.

### 405 Method Not Allowed
```json
{
  "Message": "The requested resource does not support http method 'GET'."
}
```
Wrong HTTP method (must be POST).

## Usage Example

### Send General Message
```http
POST /api/3/komens/message HTTP/1.1
Content-Type: application/json; charset=utf-8
Authorization: Bearer <token>

{
  "MessageType": "OBECNA",
  "Title": "Meeting Request",
  "Text": "I would like to schedule a meeting.",
  "RecipientType": "U",
  "Recipients": ["AABBC"],
  "RequireConfirmation": true
}
```

## Implementation Notes

1. **Get message types**: Call `/api/3/komens/message-types` first
2. **Get recipients**: Use recipient list from message-types response
3. **Handle optional fields**: Some fields depend on message type
4. **Check HasTitle**: Some message types don't allow titles

## Common Use Cases

1. **Teacher communication** - Send messages to teachers
2. **Absence excuses** - Submit absence explanations
3. **General inquiries** - Ask questions to school staff
4. **Meeting requests** - Schedule appointments

## Caching Strategy

- **Don't cache**: Each message is unique
- **Cache message types**: Message types rarely change

## Notes

- Message types must match those from `/api/3/komens/message-types`
- Recipient IDs must be valid for the recipient type
- Some message types have different required fields
- Title may not be allowed for some message types
- Attachments must exist before sending
- Email notification may require additional setup

## Related Modules

- **Komens Message Types** (`/api/3/komens/message-types`) - Get available message types
- **Received Messages** (`/api/3/komens/messages/received`) - View sent messages
- **Sent Messages** (`/api/3/komens/messages/sent`) - View message history
