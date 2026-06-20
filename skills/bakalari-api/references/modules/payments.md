# Payments Module (`/api/3/payments`)

## Overview

The payments module provides access to class fund (třídní fond) information, including balance, payment history, and payment instructions for adding funds.

## Endpoints

### Class Fund Summary
```
GET /api/3/payments/classfund/summary
```

Returns the current status of the class fund account.

**Authentication**: Required
**Content-Type**: `application/json`

**Response**:
```json
{
  "StudentNameWithClass": "Novák Jan (1.A)",
  "Balance": 1000.00,
  "Spent": -5052.97
}
```

### Payment History
```
GET /api/3/payments/classfund
```

Returns all payments (income and expenses) grouped by month.

**Authentication**: Required
**Content-Type**: `application/json`

**Query Parameters**:
- `sort` (optional): `asc` (oldest to newest) or `desc` (newest to oldest)
- `search` (optional): Search string (non-empty)

**Response**:
```json
{
  "MonthlyData": [
    {
      "Date": "2024-09-01T00:00:00+02:00",
      "MonthlyBalance": 160,
      "Items": [
        {
          "Description": "Kino 1.A",
          "Date": "2024-09-04T00:00:00+02:00",
          "Amount": -40
        },
        {
          "Description": "Příjem z banky (Jana Nováková)",
          "Date": "2024-09-02T00:00:00+02:00",
          "Amount": 200
        }
      ]
    }
  ]
}
```

### Payment Information
```
GET /api/3/payments/classfund/paymentsinfo
```

Returns information for making payments to the class fund.

**Authentication**: Required
**Content-Type**: `application/json`

**Response**:
```json
{
  "Instructions": "Pro platbu do třídního fondu použijte níže uvedený bankovní účet, specifický a variabilní symbol.",
  "BankAccount": "76327632/0300",
  "VariableSymbol": 123456789,
  "SpecificSymbol": 1111,
  "Amount": 0,
  "Message": "1.A  Novák Jan třídní fond"
}
```

## Response Fields

### Summary Object
| Field | Type | Description |
|-------|------|-------------|
| `StudentNameWithClass` | string | Student name with class |
| `Balance` | number | Current account balance |
| `Spent` | number | Total amount spent (negative) |

### Monthly Data Object
| Field | Type | Description |
|-------|------|-------------|
| `Date` | datetime | First day of the month |
| `MonthlyBalance` | number | Balance change for the month |
| `Items` | array | Array of payment items |

### Payment Item Object
| Field | Type | Description |
|-------|------|-------------|
| `Description` | string | Payment description |
| `Date` | datetime | Payment date |
| `Amount` | number | Payment amount (positive=income, negative=expense) |

### Payment Info Object
| Field | Type | Description |
|-------|------|-------------|
| `Instructions` | string | Payment instructions |
| `BankAccount` | string | Bank account number |
| `VariableSymbol` | number | Variable symbol for payment |
| `SpecificSymbol` | number | Specific symbol for payment |
| `Amount` | number | Suggested/default amount |
| `Message` | string | Payment message for recipient |

## Usage Examples

### Get Fund Summary
```http
GET /api/3/payments/classfund/summary HTTP/1.1
Authorization: Bearer <token>
```

### Get Payment History (Descending)
```http
GET /api/3/payments/classfund?sort=desc HTTP/1.1
Authorization: Bearer <token>
```

### Search Payments
```http
GET /api/3/payments/classfund?search=kino HTTP/1.1
Authorization: Bearer <token>
```

### Get Payment Information
```http
GET /api/3/payments/classfund/paymentsinfo HTTP/1.1
Authorization: Bearer <token>
```

## Query Parameters

### Sort
- `asc`: Oldest payments first
- `desc`: Newest payments first (default if not specified)

### Search
- Any non-empty string to filter descriptions
- Case-insensitive search
- URL-encode special characters

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
  "Message": "The requested resource does not support http method 'POST'."
}
```
Wrong HTTP method (must be GET).

### 400 Bad Request (Empty Search)
```json
{
  "Message": "The request is invalid.",
  "ModelState": {
    "$type": "HttpError",
    "search.String": {
      "$type": "String[]",
      "$values": ["A value is required but was not present in the request."]
    }
  }
}
```
Occurs when `search` parameter is empty (`?search=`).

## Display Considerations

1. **Balance display**: Show current balance prominently
2. **Income vs expenses**: Use green for income, red for expenses
3. **Monthly grouping**: Group payments by month
4. **Payment info**: Display banking details clearly
5. **QR code**: Generate QR code from payment info

## Common Use Cases

1. **Check balance** - View current fund status
2. **Payment history** - Review past payments
3. **Add funds** - Get payment information for deposits
4. **Expense tracking** - Monitor where money was spent
5. **Search** - Find specific payments

## Caching Strategy

- **Balance**: Cache for 5 minutes
- **Payment history**: Cache for 15 minutes
- **Payment info**: Cache for 1 hour

## Notes

- Amounts are positive for income, negative for expenses
- Monthly balance shows net change for the month
- Payment info is used for generating QR codes in mobile apps
- Bank account format: `account number / bank code`
- Variable symbol identifies the student
- Search searches in description fields
- Default sort is descending (newest first)

## Related Modules

- **User** (`/api/3/user`) - Student information
- **Subjects** (`/api/3/subjects`) - Related subject payments
