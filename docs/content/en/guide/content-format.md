---
title: Content Format Standard
weight: 10
---

This document describes the standard structure for writing documentation in large projects. The goal is to ensure consistency, maintainability, and clarity for all team members.

<!--more-->

## Overview

Each document in the project should follow this structure:

1. **Introduction** - Overview of the feature/module
2. **Business Logic** - Explanation of business processes
3. **Implementation Logic** - Technical implementation details
4. **API Reference** - Complete API documentation
5. **Testing** - Testing guidelines
6. **Troubleshooting** - Common issue resolution

---

## 1. Introduction

This section provides an overview of the feature or module.

### Purpose

Briefly describe the purpose of this feature in the system.

### Scope

- What this feature **can do**
- What this feature **cannot do**
- Related modules/services

### Prerequisites

| Requirement | Version | Notes |
| :---------- | :------ | :---- |
| Node.js | >= 18.0 | Required |
| Redis | >= 7.0 | For caching |
| PostgreSQL | >= 15 | Primary database |

---

## 2. Business Logic

### Business Process

Describe the main business flow of the feature.

```mermaid
flowchart TD
    A[User sends request] --> B{Validate token}
    B -->|Valid| C[Process business logic]
    B -->|Invalid| D[Return 401 error]
    C --> E[Save to database]
    E --> F[Return result]
```

### Business Rules

| # | Rule | Description |
| :- | :--- | :---------- |
| 1 | Authentication required | All requests must have a valid token |
| 2 | Rate limiting | Maximum 100 requests/minute/user |
| 3 | Validation | Input data must pass validation |

### Special Cases

- **Case 1**: When user hasn't verified email → Allow read-only, no write operations
- **Case 2**: When system is overloaded → Return 503 with retry-after header

---

## 3. Implementation Logic

### Technical Architecture

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   API Gateway   │────▶│   Auth Service  │────▶│   User Service  │
└─────────────────┘     └─────────────────┘     └─────────────────┘
         │                                               │
         │                                               ▼
         │                                      ┌─────────────────┐
         └─────────────────────────────────────▶│    Database     │
                                                └─────────────────┘
```

### Detailed Processing Flow

{{< steps >}}

### Step 1: Receive Request

Client sends request to API Gateway. Gateway performs:
- Validate request format
- Extract JWT token from header
- Forward to corresponding service

### Step 2: Authentication

Auth Service checks:
- Is the token valid?
- Has the token expired?
- Does the user have access rights?

### Step 3: Process Business Logic

Service processes business logic:
- Validate input data
- Execute business logic
- Interact with database

### Step 4: Return Result

Package response and return to client with standard format.

{{< /steps >}}

### Directory Structure

{{< filetree/container >}}
  {{< filetree/folder name="src" >}}
    {{< filetree/folder name="controllers" >}}
      {{< filetree/file name="user.controller.ts" >}}
      {{< filetree/file name="auth.controller.ts" >}}
    {{< /filetree/folder >}}
    {{< filetree/folder name="services" >}}
      {{< filetree/file name="user.service.ts" >}}
      {{< filetree/file name="auth.service.ts" >}}
    {{< /filetree/folder >}}
    {{< filetree/folder name="repositories" >}}
      {{< filetree/file name="user.repository.ts" >}}
    {{< /filetree/folder >}}
    {{< filetree/folder name="dto" >}}
      {{< filetree/file name="create-user.dto.ts" >}}
      {{< filetree/file name="update-user.dto.ts" >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
{{< /filetree/container >}}

---

## 4. API Reference

### 4.1 Create New User

Create a new user account in the system.

#### Basic Information

| Property | Value |
| :------- | :---- |
| **Method** | `POST` |
| **URL** | `/api/v1/users` |
| **Authentication** | Bearer Token (Admin) |
| **Content-Type** | `application/json` |

#### Headers

| Header | Type | Required | Description |
| :----- | :--- | :------- | :---------- |
| `Authorization` | string | ✅ | Authentication token. Format: `Bearer <token>` |
| `Content-Type` | string | ✅ | Must be `application/json` |
| `X-Request-ID` | string | ❌ | ID for request tracking. Auto-generated if not provided |
| `Accept-Language` | string | ❌ | Response language. Default: `en` |

#### Request Body

```json
{
  "email": "user@example.com",
  "password": "SecureP@ss123",
  "fullName": "John Doe",
  "phoneNumber": "+14155551234",
  "role": "user",
  "metadata": {
    "department": "Engineering",
    "employeeId": "EMP001"
  }
}
```

#### Request Properties Details

| Property | Type | Required | Description | Constraints |
| :------- | :--- | :------- | :---------- | :---------- |
| `email` | string | ✅ | User's email address. Used as login username | Valid email, max 255 characters, unique in system |
| `password` | string | ✅ | Login password | Min 8 characters, must include uppercase, lowercase, number, and special character |
| `fullName` | string | ✅ | Full name | Min 2 characters, max 100 characters |
| `phoneNumber` | string | ❌ | Phone number | E.164 format (e.g., +14155551234) |
| `role` | string | ❌ | User role | One of: `user`, `admin`, `moderator`. Default: `user` |
| `metadata` | object | ❌ | Custom additional information | JSON object, max 10KB |
| `metadata.department` | string | ❌ | Department | Max 50 characters |
| `metadata.employeeId` | string | ❌ | Employee ID | Max 20 characters |

#### cURL

```bash
curl --request POST \
  --url 'https://api.example.com/api/v1/users' \
  --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkFkbWluIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c' \
  --header 'Content-Type: application/json' \
  --header 'X-Request-ID: req-123456' \
  --data '{
    "email": "user@example.com",
    "password": "SecureP@ss123",
    "fullName": "John Doe",
    "phoneNumber": "+14155551234",
    "role": "user",
    "metadata": {
      "department": "Engineering",
      "employeeId": "EMP001"
    }
  }'
```

#### Success Response

**Status Code:** `201 Created`

```json
{
  "success": true,
  "data": {
    "id": "usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF",
    "email": "user@example.com",
    "fullName": "John Doe",
    "phoneNumber": "+14155551234",
    "role": "user",
    "status": "pending_verification",
    "metadata": {
      "department": "Engineering",
      "employeeId": "EMP001"
    },
    "createdAt": "2024-02-20T10:30:00.000Z",
    "updatedAt": "2024-02-20T10:30:00.000Z"
  },
  "meta": {
    "requestId": "req-123456",
    "timestamp": "2024-02-20T10:30:00.000Z"
  }
}
```

#### Response Properties Details

| Property | Type | Description |
| :------- | :--- | :---------- |
| `success` | boolean | Request processing status. `true` if successful |
| `data.id` | string | Unique user ID, ULID format with `usr_` prefix |
| `data.email` | string | Registered email |
| `data.fullName` | string | Full name |
| `data.phoneNumber` | string | Phone number (if provided) |
| `data.role` | string | Assigned role |
| `data.status` | string | Account status: `pending_verification`, `active`, `suspended`, `deleted` |
| `data.metadata` | object | Additional information |
| `data.createdAt` | string | Creation timestamp (ISO 8601) |
| `data.updatedAt` | string | Last update timestamp (ISO 8601) |
| `meta.requestId` | string | Request ID for tracking |
| `meta.timestamp` | string | Request processing timestamp |

#### Error Responses

{{< tabs >}}

{{< tab name="400 Bad Request" >}}
**Cause:** Request body is not valid JSON format or missing required fields.

```json
{
  "success": false,
  "error": {
    "code": "BAD_REQUEST",
    "message": "Invalid request body",
    "details": "Unable to parse JSON body"
  },
  "meta": {
    "requestId": "req-123456",
    "timestamp": "2024-02-20T10:30:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="401 Unauthorized" >}}
**Cause:** Token is invalid, expired, or lacks admin privileges.

```json
{
  "success": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Token is invalid or has expired",
    "details": "Please log in again to get a new token"
  },
  "meta": {
    "requestId": "req-123456",
    "timestamp": "2024-02-20T10:30:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="409 Conflict" >}}
**Cause:** Email already exists in the system.

```json
{
  "success": false,
  "error": {
    "code": "CONFLICT",
    "message": "Email is already in use",
    "details": "Email user@example.com already exists in the system"
  },
  "meta": {
    "requestId": "req-123456",
    "timestamp": "2024-02-20T10:30:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="422 Validation Error" >}}
**Cause:** Data does not meet validation requirements.

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid data",
    "details": [
      {
        "field": "password",
        "message": "Password must be at least 8 characters, including uppercase, lowercase, number, and special character"
      },
      {
        "field": "phoneNumber",
        "message": "Phone number does not match E.164 format"
      }
    ]
  },
  "meta": {
    "requestId": "req-123456",
    "timestamp": "2024-02-20T10:30:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="500 Internal Error" >}}
**Cause:** Unidentified server error.

```json
{
  "success": false,
  "error": {
    "code": "INTERNAL_ERROR",
    "message": "A system error has occurred",
    "details": "Please try again later or contact support"
  },
  "meta": {
    "requestId": "req-123456",
    "timestamp": "2024-02-20T10:30:00.000Z"
  }
}
```
{{< /tab >}}

{{< /tabs >}}

---

### 4.2 Get User Information

Get detailed information of a user by ID.

#### Basic Information

| Property | Value |
| :------- | :---- |
| **Method** | `GET` |
| **URL** | `/api/v1/users/{userId}` |
| **Authentication** | Bearer Token |
| **Content-Type** | `application/json` |

#### Path Parameters

| Parameter | Type | Required | Description |
| :-------- | :--- | :------- | :---------- |
| `userId` | string | ✅ | User ID to retrieve. Format: `usr_<ULID>` |

#### Headers

| Header | Type | Required | Description |
| :----- | :--- | :------- | :---------- |
| `Authorization` | string | ✅ | Authentication token. Format: `Bearer <token>` |

#### cURL

```bash
curl --request GET \
  --url 'https://api.example.com/api/v1/users/usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF' \
  --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IlVzZXIiLCJpYXQiOjE1MTYyMzkwMjJ9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```

#### Success Response

**Status Code:** `200 OK`

```json
{
  "success": true,
  "data": {
    "id": "usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF",
    "email": "user@example.com",
    "fullName": "John Doe",
    "phoneNumber": "+14155551234",
    "role": "user",
    "status": "active",
    "metadata": {
      "department": "Engineering",
      "employeeId": "EMP001"
    },
    "lastLoginAt": "2024-02-20T09:00:00.000Z",
    "createdAt": "2024-02-15T10:30:00.000Z",
    "updatedAt": "2024-02-20T09:00:00.000Z"
  },
  "meta": {
    "requestId": "req-789012",
    "timestamp": "2024-02-20T10:35:00.000Z"
  }
}
```

#### Error Responses

{{< tabs >}}

{{< tab name="401 Unauthorized" >}}
```json
{
  "success": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Token is invalid or has expired"
  },
  "meta": {
    "requestId": "req-789012",
    "timestamp": "2024-02-20T10:35:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="403 Forbidden" >}}
**Cause:** User does not have permission to view another user's information.

```json
{
  "success": false,
  "error": {
    "code": "FORBIDDEN",
    "message": "Access to this resource is denied",
    "details": "You can only view your own information"
  },
  "meta": {
    "requestId": "req-789012",
    "timestamp": "2024-02-20T10:35:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="404 Not Found" >}}
**Cause:** User ID does not exist.

```json
{
  "success": false,
  "error": {
    "code": "NOT_FOUND",
    "message": "User not found",
    "details": "User with ID usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF does not exist"
  },
  "meta": {
    "requestId": "req-789012",
    "timestamp": "2024-02-20T10:35:00.000Z"
  }
}
```
{{< /tab >}}

{{< /tabs >}}

---

### 4.3 List Users (with Pagination)

Get a list of users with pagination, filtering, and sorting support.

#### Basic Information

| Property | Value |
| :------- | :---- |
| **Method** | `GET` |
| **URL** | `/api/v1/users` |
| **Authentication** | Bearer Token (Admin) |

#### Query Parameters

| Parameter | Type | Required | Description | Default |
| :-------- | :--- | :------- | :---------- | :------ |
| `page` | integer | ❌ | Page number (starting from 1) | `1` |
| `limit` | integer | ❌ | Records per page | `20` |
| `sort` | string | ❌ | Sort field | `createdAt` |
| `order` | string | ❌ | Order: `asc` or `desc` | `desc` |
| `status` | string | ❌ | Filter by status | - |
| `role` | string | ❌ | Filter by role | - |
| `search` | string | ❌ | Search by email or name | - |

#### cURL

```bash
curl --request GET \
  --url 'https://api.example.com/api/v1/users?page=1&limit=10&status=active&sort=createdAt&order=desc' \
  --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkFkbWluIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```

#### Success Response

**Status Code:** `200 OK`

```json
{
  "success": true,
  "data": [
    {
      "id": "usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF",
      "email": "user1@example.com",
      "fullName": "John Doe",
      "role": "user",
      "status": "active",
      "createdAt": "2024-02-20T10:30:00.000Z"
    },
    {
      "id": "usr_01HQ3K5XJPZ8VWMN4YGCR2BGHI",
      "email": "user2@example.com",
      "fullName": "Jane Smith",
      "role": "admin",
      "status": "active",
      "createdAt": "2024-02-19T08:00:00.000Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "totalItems": 156,
    "totalPages": 16,
    "hasNextPage": true,
    "hasPrevPage": false
  },
  "meta": {
    "requestId": "req-345678",
    "timestamp": "2024-02-20T10:40:00.000Z"
  }
}
```

---

## 5. Testing

### Unit Tests

Test files for this module:

{{< filetree/container >}}
  {{< filetree/folder name="tests" >}}
    {{< filetree/folder name="unit" >}}
      {{< filetree/file name="user.service.spec.ts" >}}
      {{< filetree/file name="user.controller.spec.ts" >}}
      {{< filetree/file name="auth.service.spec.ts" >}}
    {{< /filetree/folder >}}
    {{< filetree/folder name="integration" >}}
      {{< filetree/file name="user.api.spec.ts" >}}
      {{< filetree/file name="auth.api.spec.ts" >}}
    {{< /filetree/folder >}}
    {{< filetree/folder name="e2e" >}}
      {{< filetree/file name="user-flow.spec.ts" >}}
    {{< /filetree/folder >}}
  {{< /filetree/folder >}}
{{< /filetree/container >}}

### Running Tests

```bash
# Run all unit tests
npm run test:unit

# Run integration tests
npm run test:integration

# Run e2e tests
npm run test:e2e

# Run tests with coverage
npm run test:coverage
```

### Important Test Cases

| Test Case | Description | Expected Result |
| :-------- | :---------- | :-------------- |
| TC-001 | Create user with valid data | Status 201, user created |
| TC-002 | Create user with duplicate email | Status 409, error CONFLICT |
| TC-003 | Create user with weak password | Status 422, validation error |
| TC-004 | Get non-existent user | Status 404, error NOT_FOUND |
| TC-005 | Access without token | Status 401, error UNAUTHORIZED |

---

## 6. Troubleshooting

### Common Errors

{{< callout type="error" >}}
**Error: "Token is invalid" (401)**

**Possible causes:**
- Token has expired
- Token format is incorrect
- Secret key mismatch

**Resolution:**
1. Check if token has correct format `Bearer <token>`
2. Decode token to check expiration time
3. Log in again to get a new token
{{< /callout >}}

{{< callout type="warning" >}}
**Error: "Rate limit exceeded" (429)**

**Cause:** Exceeded limit of 100 requests/minute

**Resolution:**
1. Check `Retry-After` header for wait time
2. Implement exponential backoff in client
3. Contact admin if limit increase is needed
{{< /callout >}}

### Support Contact

If you encounter issues you cannot resolve:

- **Email:** support@example.com
- **Slack:** #api-support
- **Documentation:** https://docs.example.com

---

## 7. Changelog

| Version | Date | Changes |
| :------ | :--- | :------ |
| v1.2.0 | 2024-02-20 | Added `metadata` field for user |
| v1.1.0 | 2024-02-01 | Added pagination API |
| v1.0.0 | 2024-01-15 | Initial release |

---

## Related Documentation

{{< cards >}}
  {{< card link="../markdown" title="Markdown" icon="document-text" >}}
  {{< card link="../syntax-highlighting" title="Syntax Highlighting" icon="code" >}}
  {{< card link="../diagrams" title="Diagrams" icon="chart-bar" >}}
{{< /cards >}}
