# DIabetes - API Documentation

## Base URL

```
http://localhost:5000/api
```

## Authentication

All endpoints (except `/auth/register` and `/auth/login`) require JWT token in Authorization header:

```
Authorization: Bearer <your_jwt_token>
```

## Response Format

All responses are JSON:

### Success Response (200)
```json
{
  "success": true,
  "data": { ... }
}
```

### Error Response (400+)
```json
{
  "success": false,
  "error": "Error message",
  "details": { ... }
}
```

---

## Authentication Endpoints

### Register User

**POST** `/auth/register`

Create a new user account.

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "securePassword123",
  "role": "patient" // or "clinician"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": 1,
    "email": "user@example.com",
    "role": "patient",
    "createdAt": "2024-01-15T10:30:00Z"
  }
}
```

---

### Login

**POST** `/auth/login`

Authenticate user and receive JWT token.

**Request Body:**
```json
{
  "email": "user@example.com",
  "password": "securePassword123"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": "24h",
    "user": {
      "id": 1,
      "email": "user@example.com",
      "role": "patient"
    }
  }
}
```

---

### Refresh Token

**POST** `/auth/refresh`

Get a new JWT token using existing token.

**Request Headers:**
```
Authorization: Bearer <expired_token>
```

**Response:**
```json
{
  "success": true,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": "24h"
  }
}
```

---

## Patient Endpoints

### List Patients

**GET** `/patients`

Get list of all patients (clinician only).

**Query Parameters:**
- `page` (optional, default: 1) - Page number
- `limit` (optional, default: 10) - Items per page
- `search` (optional) - Search by name/email

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "userId": 2,
      "dateOfBirth": "1990-05-15",
      "diabetesType": "type2",
      "diagnosisDate": "2015-03-20",
      "user": {
        "email": "patient@example.com"
      }
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 50
  }
}
```

---

### Get Patient Details

**GET** `/patients/:id`

Get detailed information about a specific patient.

**Response:**
```json
{
  "success": true,
  "data": {
    "id": 1,
    "userId": 2,
    "dateOfBirth": "1990-05-15",
    "diabetesType": "type2",
    "diagnosisDate": "2015-03-20",
    "user": {
      "id": 2,
      "email": "patient@example.com",
      "role": "patient"
    },
    "recentReadings": [
      {
        "id": 100,
        "readingValue": 145.5,
        "readingTime": "2024-01-15T10:30:00Z"
      }
    ]
  }
}
```

---

### Create Patient

**POST** `/patients`

Create a new patient record.

**Request Body:**
```json
{
  "userId": 2,
  "dateOfBirth": "1990-05-15",
  "diabetesType": "type2",
  "diagnosisDate": "2015-03-20"
}
```

**Response:** (201 Created)
```json
{
  "success": true,
  "data": {
    "id": 1,
    "userId": 2,
    "dateOfBirth": "1990-05-15",
    "diabetesType": "type2",
    "diagnosisDate": "2015-03-20",
    "createdAt": "2024-01-15T10:30:00Z"
  }
}
```

---

### Update Patient

**PUT** `/patients/:id`

Update patient information.

**Request Body:**
```json
{
  "dateOfBirth": "1990-05-15",
  "diabetesType": "type2"
}
```

**Response:**
```json
{
  "success": true,
  "data": { ... }
}
```

---

### Delete Patient

**DELETE** `/patients/:id`

Delete a patient record.

**Response:**
```json
{
  "success": true,
  "data": {
    "message": "Patient deleted successfully"
  }
}
```

---

## Glucose Reading Endpoints

### List Glucose Readings

**GET** `/glucose/readings`

Get list of glucose readings for authenticated patient.

**Query Parameters:**
- `patientId` (optional) - Filter by patient (clinician only)
- `startDate` (optional) - Filter from date (YYYY-MM-DD)
- `endDate` (optional) - Filter to date (YYYY-MM-DD)
- `page` (optional, default: 1)
- `limit` (optional, default: 20)

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "patientId": 1,
      "readingValue": 145.5,
      "readingTime": "2024-01-15T10:30:00Z",
      "beforeMeal": false,
      "afterMeal": true,
      "createdAt": "2024-01-15T10:30:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 150
  }
}
```

---

### Create Glucose Reading

**POST** `/glucose/readings`

Log a new glucose reading.

**Request Body:**
```json
{
  "readingValue": 145.5,
  "readingTime": "2024-01-15T10:30:00Z",
  "beforeMeal": false,
  "afterMeal": true,
  "notes": "After lunch"
}
```

**Response:** (201 Created)
```json
{
  "success": true,
  "data": {
    "id": 1,
    "patientId": 1,
    "readingValue": 145.5,
    "readingTime": "2024-01-15T10:30:00Z",
    "beforeMeal": false,
    "afterMeal": true,
    "createdAt": "2024-01-15T10:30:00Z"
  }
}
```

---

### Get Glucose Statistics

**GET** `/glucose/stats`

Get glucose statistics for a patient.

**Query Parameters:**
- `patientId` (optional) - Filter by patient (clinician only)
- `days` (optional, default: 7) - Last N days

**Response:**
```json
{
  "success": true,
  "data": {
    "average": 145.8,
    "highest": 200.5,
    "lowest": 95.2,
    "standardDeviation": 25.3,
    "readingsCount": 42,
    "period": "7 days"
  }
}
```

---

### Delete Glucose Reading

**DELETE** `/glucose/readings/:id`

Delete a glucose reading.

**Response:**
```json
{
  "success": true,
  "data": {
    "message": "Reading deleted successfully"
  }
}
```

---

## Medication Endpoints

### List Medications

**GET** `/medications`

Get medications for authenticated patient.

**Query Parameters:**
- `patientId` (optional) - Filter by patient (clinician only)
- `page` (optional, default: 1)
- `limit` (optional, default: 10)

**Response:**
```json
{
  "success": true,
  "data": [
    {
      "id": 1,
      "patientId": 1,
      "medicationName": "Metformin",
      "dosage": "500mg",
      "frequency": "Twice daily",
      "startDate": "2023-01-01",
      "endDate": null,
      "active": true
    }
  ]
}
```

---

### Create Medication

**POST** `/medications`

Add a new medication.

**Request Body:**
```json
{
  "medicationName": "Metformin",
  "dosage": "500mg",
  "frequency": "Twice daily",
  "startDate": "2023-01-01"
}
```

**Response:** (201 Created)
```json
{
  "success": true,
  "data": {
    "id": 1,
    "patientId": 1,
    "medicationName": "Metformin",
    "dosage": "500mg",
    "frequency": "Twice daily",
    "startDate": "2023-01-01",
    "createdAt": "2024-01-15T10:30:00Z"
  }
}
```

---

### Update Medication

**PUT** `/medications/:id`

Update medication information.

**Request Body:**
```json
{
  "dosage": "1000mg",
  "frequency": "Three times daily",
  "endDate": "2024-01-31"
}
```

**Response:**
```json
{
  "success": true,
  "data": { ... }
}
```

---

### Delete Medication

**DELETE** `/medications/:id`

Delete a medication record.

**Response:**
```json
{
  "success": true,
  "data": {
    "message": "Medication deleted successfully"
  }
}
```

---

## Error Codes

| Code | Message | Description |
|------|---------|-------------|
| 400 | Bad Request | Invalid request parameters |
| 401 | Unauthorized | Missing or invalid token |
| 403 | Forbidden | No permission to access resource |
| 404 | Not Found | Resource not found |
| 409 | Conflict | Resource already exists |
| 500 | Server Error | Internal server error |

---

## Rate Limiting

API requests are limited to 100 requests per minute per IP address.

**Headers:**
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 99
X-RateLimit-Reset: 1705324200
```

---

## Examples

### JavaScript/Fetch

```javascript
// Login
const response = await fetch('http://localhost:5000/api/auth/login', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    email: 'patient@example.com',
    password: 'password123'
  })
});
const { data } = await response.json();
const token = data.token;

// Get patient readings
const readingsResponse = await fetch('http://localhost:5000/api/glucose/readings', {
  headers: { 'Authorization': `Bearer ${token}` }
});
const readings = await readingsResponse.json();
console.log(readings);
```

### cURL

```bash
# Login
curl -X POST http://localhost:5000/api/auth/login \
  -H 'Content-Type: application/json' \
  -d '{"email":"patient@example.com","password":"password123"}'

# Get readings (replace TOKEN with actual token)
curl -X GET http://localhost:5000/api/glucose/readings \
  -H 'Authorization: Bearer TOKEN'
```
