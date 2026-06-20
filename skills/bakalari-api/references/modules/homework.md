# Homework Module (`/api/3/homeworks`)

## Overview

The homework module provides access to homework assignments, due dates, and completion status.

## Endpoint

```
GET /api/3/homeworks
```

Returns homework assignments for the student.

**Authentication**: Required

## Query Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `from` | date | Optional | Start date (ISO format) |
| `to` | date | Optional | End date (ISO format) |

## Response Structure

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
          "Size": 12345,
          "Url": "https://..."
        }
      ]
    }
  ]
}
```

## Response Fields

### Homework Object

| Field | Type | Description |
|-------|------|-------------|
| `Id` | string | Unique homework identifier |
| `Date` | datetime | When homework was assigned |
| `Subject` | object | Subject information |
| `Teacher` | object | Teacher who assigned homework |
| `Content` | string | Homework description/instructions |
| `EndDate` | datetime | Due date |
| `Completed` | boolean | Completion status |
| `Attachments` | array | Attached files (if any) |

### Attachment Object

| Field | Type | Description |
|-------|------|-------------|
| `Id` | string | Attachment identifier |
| `Name` | string | File name |
| `Size` | number | File size in bytes |
| `Url` | string | Download URL |

## Usage Examples

### Get All Homework
```http
GET /api/3/homeworks HTTP/1.1
Authorization: Bearer <token>
```

### Get Homework for Date Range
```http
GET /api/3/homeworks?from=2025-06-01T00:00:00%2B02:00&to=2025-06-30T23:59:59%2B02:00 HTTP/1.1
Authorization: Bearer <token>
```

### Get Upcoming Homework (next 7 days)
```http
GET /api/3/homeworks?from=2025-06-20T00:00:00%2B02:00&to=2025-06-27T23:59:59%2B02:00 HTTP/1.1
Authorization: Bearer <token>
```

## Date Filtering

### Common Date Ranges

| Purpose | From | To |
|---------|------|-----|
| Current week | Start of week | End of week |
| Upcoming | Today | 7 days ahead |
| Past homework | Start of semester | Yesterday |
| All semester | Semester start | Semester end |

**Important**: Always URL encode ISO dates:
- `+` becomes `%2B`
- `:` becomes `%3A`

## Display Considerations

1. **Sort by due date**: Most urgent first
2. **Group by status**: Separate completed/pending
3. **Show subject**: Clearly display subject name/abbreviation
4. **Days remaining**: Show time until due date
5. **Overdue highlighting**: Mark past-due items
6. **Attachment indicators**: Show attachment icon

## Status Management

### Completed Homework
```json
{
  "Completed": true
}
```
- Display with checkmark or green color
- Can be filtered out or moved to bottom

### Pending Homework
```json
{
  "Completed": false,
  "EndDate": "2025-06-22T23:59:59+02:00"
}
```
- Show prominently
- Calculate days/hours remaining

### Overdue Homework
```json
{
  "Completed": false,
  "EndDate": "2025-06-18T23:59:59+02:00"  // Past date
}
```
- Highlight in red
- Show as "overdue" with days late

## Common Use Cases

1. **Homework list** - Display all pending homework
2. **Due soon** - Show homework due within 3 days
3. **By subject** - Filter homework by specific subject
4. **Completed history** - Show past completed homework
5. **Attachment download** - Access attached files

## Caching Strategy

- **Pending homework**: Cache for 30 minutes
- **Completed homework**: Cache for 1 hour
- **Past homework**: Cache for 24 hours

## Notes

- Homework may be updated or cancelled by teachers
- Completion status may be managed by teachers or self-reported
- Attachments require authentication to download
- Date range filtering is server-side
- Empty array indicates no homework matches criteria

## Empty Response

```json
{
  "Homeworks": []
}
```

This is normal when no homework matches the date range.
