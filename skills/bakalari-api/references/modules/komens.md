# Komens Module (`/api/3/komens/*`)

## Overview

The Komens module provides access to the messaging system (Komens is the name of the messaging module). It includes received messages, sent messages, and school noticeboard.

## Endpoints

### Received Messages
```
GET /api/3/komens/messages/received
```

Returns messages received by the user.

**Authentication**: Required

### Sent Messages
```
GET /api/3/komens/messages/sent
```

Returns messages sent by the user.

**Authentication**: Required

### Noticeboard
```
GET /api/3/komens/messages/noticeboard
```

Returns school noticeboard messages (announcements).

**Authentication**: Required

### Message Details
```
GET /api/3/komens/messages/<message_id>
```

Returns detailed information about a specific message.

**Authentication**: Required

**Parameters**:
- `message_id`: Message identifier

## Response Structure

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
      ],
      "IsNoticeboard": false,
      "CanReply": true
    }
  ],
  "UnreadCount": 3
}
```

## Response Fields

### Message Object

| Field | Type | Description |
|-------|------|-------------|
| `Id` | string | Unique message identifier |
| `Date` | datetime | When message was sent |
| `Subject` | string | Message subject/title |
| `Body` | string | Message content |
| `Sender` | object | Sender information |
| `Recipients` | array | List of recipients |
| `IsRead` | boolean | Read status |
| `HasAttachments` | boolean | If message has attachments |
| `Attachments` | array | Attached files |
| `IsNoticeboard` | boolean | If from noticeboard |
| `CanReply` | boolean | If user can reply |

### Sender/Recipient Object

| Field | Type | Description |
|-------|------|-------------|
| `Id` | string | User identifier |
| `Name` | string | User name |
| `Type` | string | User type (teacher, parent, student) |

### Attachment Object

| Field | Type | Description |
|-------|------|-------------|
| `Id` | string | Attachment identifier |
| `Name` | string | File name |
| `Size` | number | File size in bytes |
| `Url` | string | Download URL |

## Endpoint Differences

### Received Messages
- Messages in user's inbox
- From teachers, parents, or school
- Can mark as read

### Sent Messages
- Messages sent by user
- Shows delivery status
- Limited history

### Noticeboard
- School announcements
- One-way communication
- Visible to relevant groups
- Cannot reply

## Usage Examples

### Get Received Messages
```http
GET /api/3/komens/messages/received HTTP/1.1
Authorization: Bearer <token>
```

### Get Sent Messages
```http
GET /api/3/komens/messages/sent HTTP/1.1
Authorization: Bearer <token>
```

### Get Noticeboard
```http
GET /api/3/komens/messages/noticeboard HTTP/1.1
Authorization: Bearer <token>
```

### Get Message Details
```http
GET /api/3/komens/messages/abc123 HTTP/1.1
Authorization: Bearer <token>
```

## Message Types

### By Direction
- **Received**: Messages to user
- **Sent**: Messages from user
- **Noticeboard**: School announcements

### By Sender Type
- **From teacher**: Messages from teachers
- **From parent**: Messages from parents
- **From school**: Official school communications
- **From system**: Automated messages

## Display Considerations

### Message List
1. **Sort by date**: Most recent first
2. **Unread indicators**: Show bold or dot for unread
3. **Sender info**: Display name and type
4. **Subject preview**: Show first part of body
5. **Attachment icon**: Indicate attachments

### Message Detail
1. **Full headers**: Show sender, recipients, date
2. **Body content**: Full message text
3. **Attachments**: List with download options
4. **Reply option**: Show if reply available

### Noticeboard
1. **Priority indicators**: Important announcements
2. **Date grouping**: Group by date
3. **Expiration**: Show if time-limited

## Read/Unread Status

### Unread Message
```json
{
  "IsRead": false
}
```
- Display prominently
- Count in unread badge

### Read Message
```json
{
  "IsRead": true
}
```
- Normal display
- Not counted in unread

## Attachment Handling

Attachments require authentication:

```http
GET https://school.cz/api/3/files/attachment-id HTTP/1.1
Authorization: Bearer <token>
```

**File types**:
- PDF documents
- Images
- Office documents
- Other formats

## Common Use Cases

1. **Inbox view** - List received messages
2. **Message detail** - View full message
3. **Compose reply** - Reply to messages
4. **Noticeboard** - View school announcements
5. **Unread count** - Badge with unread messages
6. **Attachment download** - Access attached files

## Caching Strategy

- **Message list**: Cache for 5 minutes
- **Message detail**: Cache for 1 hour
- **Noticeboard**: Cache for 30 minutes
- **Unread count**: Cache for 2 minutes

## Notes

- Messages may have multiple recipients
- Noticeboard messages are one-way communication
- Attachments have size limits
- Message history may be limited
- Read status is synchronized across devices
- Some messages may not allow replies

## Empty Response

```json
{
  "Messages": [],
  "UnreadCount": 0
}
```

Normal when no messages exist.

## Sending Messages

If sending is supported (endpoint availability varies):

```
POST /api/3/komens/messages
```

**Body** (URL-encoded):
- `recipients`: Recipient IDs
- `subject`: Message subject
- `body`: Message content
- `attachments`: Attachment IDs (if any)
