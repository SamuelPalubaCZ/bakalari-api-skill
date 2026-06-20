# Marks What-If Calculator (`/api/3/marks/what-if`)

## Overview

The what-if calculator allows predicting grade averages based on hypothetical grades. This is a POST endpoint that accepts existing grades and hypothetical future grades, then calculates the predicted average.

## Endpoint

### Calculate Predicted Average
```
POST /api/3/marks/what-if
```

Calculate grade average with hypothetical grades.

**Authentication**: Required
**Content-Type**: `application/json; charset=utf-8`

## Request Structure

Send a copy of the subject's grades from `/api/3/marks` with hypothetical grades (having `Id: null`):

```json
[
  {
    "Id": "BNMPSLI70(",
    "MarkText": "3",
    "Weight": 5,
    "MaxPoints": 0,
    "SubjectId": "28"
  },
  {
    "Id": "BNMVP:T:1N",
    "MarkText": "3-",
    "Weight": 3,
    "MaxPoints": 0,
    "SubjectId": "28"
  },
  {
    "Id": null,
    "MarkText": "1",
    "Weight": 3,
    "MaxPoints": 0,
    "SubjectId": "28"
  }
]
```

## Request Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `Id` | string | For existing grades | Grade identifier (null for hypothetical) |
| `MarkText` | string | Yes | Grade value (1-5, or points) |
| `Weight` | number | Yes | Grade weight (1-5+) |
| `MaxPoints` | number | Yes | Maximum points (0 for traditional grades) |
| `SubjectId` | string | Yes | Subject identifier |

## Response Structure

```json
{
  "Marks": [
    {
      "MarkDate": "2020-01-28T00:00:00+01:00",
      "EditDate": "2020-02-17T08:33:00+01:00",
      "Caption": "Májovci + R + L",
      "Theme": "",
      "MarkText": "3",
      "TeacherId": "UZBNM",
      "Type": "O",
      "TypeNote": "písemná práce",
      "Weight": 5,
      "SubjectId": "28",
      "IsNew": false,
      "IsPoints": false,
      "CalculatedMarkText": "",
      "ClassRankText": null,
      "Id": "BNMPSLI70(",
      "PointsText": "",
      "MaxPoints": 0
    },
    {
      "MarkDate": "2020-02-21T15:34:12+01:00",
      "EditDate": "2020-02-21T15:34:12+01:00",
      "Caption": null,
      "Theme": null,
      "MarkText": "1",
      "TeacherId": null,
      "Type": null,
      "TypeNote": null,
      "Weight": 3,
      "SubjectId": "28",
      "IsNew": false,
      "IsPoints": false,
      "CalculatedMarkText": "",
      "ClassRankText": null,
      "Id": null,
      "PointsText": "",
      "MaxPoints": 0
    }
  ],
  "Subject": {
    "Id": "28",
    "Abbrev": "ČJL ",
    "Name": "Český jazyk a literatura"
  },
  "AverageText": "2,59 ",
  "TemporaryMark": "",
  "SubjectNote": "",
  "TemporaryMarkNote": "",
  "PointsOnly": false,
  "MarkPredictionEnabled": true
}
```

## Response Fields

### Main Result
| Field | Type | Description |
|-------|------|-------------|
| `Marks` | array | All grades (existing + hypothetical) |
| `Subject` | object | Subject information |
| `AverageText` | string | Predicted average (formatted) |
| `TemporaryMark` | string | Temporary mark placeholder |
| `SubjectNote` | string | Subject note |
| `TemporaryMarkNote` | string | Temporary note |
| `PointsOnly` | boolean | If subject uses points only |
| `MarkPredictionEnabled` | boolean | If prediction is allowed for this subject |

## Usage Examples

### Calculate What-If Average

```http
POST /api/3/marks/what-if HTTP/1.1
Content-Type: application/json; charset=utf-8
Authorization: Bearer <token>

[
  {"Id":"existing1","MarkText":"3","Weight":5,"MaxPoints":0,"SubjectId":"28"},
  {"Id":null,"MarkText":"1","Weight":3,"MaxPoints":0,"SubjectId":"28"},
  {"Id":null,"MarkText":"2","Weight":2,"MaxPoints":0,"SubjectId":"28"}
]
```

## Error Handling

### 403 Forbidden
```json
{
  "Message": "An error has occurred."
}
```
Occurs when `MarkPredictionEnabled` is false for the subject.

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

## Common Use Cases

1. **Grade planning** - See how future grades might affect average
2. **Goal setting** - Calculate what grades needed to reach target average
3. **Risk assessment** - Understand impact of potential poor grades
4. **Academic planning** - Plan study strategy

## Implementation Notes

1. **Get current grades**: Fetch from `/api/3/marks` for the subject
2. **Add hypothetical grades**: Include with `Id: null`
3. **Send to endpoint**: POST the combined array
4. **Parse response**: Extract `AverageText` for predicted average

## Display Considerations

1. **Show current vs predicted**: Compare current and predicted averages
2. **Highlight hypothetical grades**: Distinguish hypothetical from existing
3. **Grade scenarios**: Allow multiple what-if calculations
4. **Average impact**: Show how much average changes

## Caching Strategy

- **Don't cache**: Calculations are real-time and user-specific
- **Request is quick**: No need for caching

## Notes

- Only works for subjects with `MarkPredictionEnabled: true`
- Hypothetical grades must have `Id: null`
- Existing grades should be included exactly as from `/api/3/marks`
- Average calculation includes all grades (existing + hypothetical)
- Response returns all grades back with the calculated average
- Useful for students to plan grade improvement strategies

## Related Modules

- **Marks** (`/api/3/marks`) - Get current grades to use as base
- **Final Marks** (`/api/3/marks/final`) - Historical final grades
