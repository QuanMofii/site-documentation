---
title: Chuẩn Định Dạng Nội Dung
weight: 10
---

Tài liệu này mô tả cấu trúc chuẩn cho việc viết documentation trong các dự án lớn. Mục tiêu là đảm bảo tính nhất quán, dễ bảo trì và dễ hiểu cho tất cả thành viên trong team.

<!--more-->

## Tổng Quan

Mỗi tài liệu trong dự án cần tuân theo cấu trúc sau:

1. **Giới thiệu** - Mô tả tổng quan về tính năng/module
2. **Business Logic** - Giải thích nghiệp vụ và quy trình
3. **Logic Thực Thi** - Chi tiết kỹ thuật triển khai
4. **API Reference** - Tài liệu API đầy đủ
5. **Testing** - Hướng dẫn kiểm thử
6. **Troubleshooting** - Xử lý sự cố thường gặp

---

## 1. Giới Thiệu

Phần này cung cấp cái nhìn tổng quan về tính năng hoặc module.

### Mục đích

Mô tả ngắn gọn mục đích của tính năng này trong hệ thống.

### Phạm vi

- Những gì tính năng này **làm được**
- Những gì tính năng này **không làm được**
- Các module/service liên quan

### Yêu cầu tiên quyết

| Yêu cầu | Phiên bản | Ghi chú |
| :------ | :-------- | :------ |
| Node.js | >= 18.0   | Bắt buộc |
| Redis   | >= 7.0    | Cho caching |
| PostgreSQL | >= 15  | Database chính |

---

## 2. Business Logic

### Quy trình nghiệp vụ

Mô tả luồng nghiệp vụ chính của tính năng.

```mermaid
flowchart TD
    A[Người dùng gửi yêu cầu] --> B{Xác thực token}
    B -->|Hợp lệ| C[Xử lý nghiệp vụ]
    B -->|Không hợp lệ| D[Trả về lỗi 401]
    C --> E[Lưu database]
    E --> F[Trả kết quả]
```

### Các quy tắc nghiệp vụ

| STT | Quy tắc | Mô tả |
| :-- | :------ | :---- |
| 1 | Xác thực bắt buộc | Tất cả request phải có token hợp lệ |
| 2 | Rate limiting | Tối đa 100 request/phút/user |
| 3 | Validation | Dữ liệu đầu vào phải qua validation |

### Các trường hợp đặc biệt

- **Trường hợp 1**: Khi user chưa verify email → Chỉ cho phép đọc, không cho phép ghi
- **Trường hợp 2**: Khi hệ thống quá tải → Trả về 503 và retry-after header

---

## 3. Logic Thực Thi

### Kiến trúc kỹ thuật

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

### Luồng xử lý chi tiết

{{< steps >}}

### Bước 1: Nhận Request

Client gửi request đến API Gateway. Gateway thực hiện:
- Validate format request
- Extract JWT token từ header
- Forward đến service tương ứng

### Bước 2: Xác thực

Auth Service kiểm tra:
- Token có hợp lệ không
- Token có hết hạn không
- User có quyền truy cập không

### Bước 3: Xử lý nghiệp vụ

Service xử lý logic nghiệp vụ:
- Validate dữ liệu đầu vào
- Thực hiện business logic
- Tương tác với database

### Bước 4: Trả kết quả

Đóng gói response và trả về client với format chuẩn.

{{< /steps >}}

### Cấu trúc thư mục

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

### 4.1 Tạo người dùng mới

Tạo một tài khoản người dùng mới trong hệ thống.

#### Thông tin cơ bản

| Thuộc tính | Giá trị |
| :--------- | :------ |
| **Method** | `POST` |
| **URL** | `/api/v1/users` |
| **Authentication** | Bearer Token (Admin) |
| **Content-Type** | `application/json` |

#### Headers

| Header | Kiểu | Bắt buộc | Mô tả |
| :----- | :--- | :------- | :---- |
| `Authorization` | string | ✅ | Token xác thực. Format: `Bearer <token>` |
| `Content-Type` | string | ✅ | Phải là `application/json` |
| `X-Request-ID` | string | ❌ | ID để tracking request. Nếu không truyền, hệ thống tự sinh |
| `Accept-Language` | string | ❌ | Ngôn ngữ response. Mặc định: `vi` |

#### Request Body

```json
{
  "email": "user@example.com",
  "password": "SecureP@ss123",
  "fullName": "Nguyễn Văn A",
  "phoneNumber": "+84901234567",
  "role": "user",
  "metadata": {
    "department": "Engineering",
    "employeeId": "EMP001"
  }
}
```

#### Chi tiết các thuộc tính Request

| Thuộc tính | Kiểu | Bắt buộc | Mô tả | Ràng buộc |
| :--------- | :--- | :------- | :---- | :-------- |
| `email` | string | ✅ | Địa chỉ email của người dùng. Dùng làm username đăng nhập | Email hợp lệ, tối đa 255 ký tự, unique trong hệ thống |
| `password` | string | ✅ | Mật khẩu đăng nhập | Tối thiểu 8 ký tự, phải có chữ hoa, chữ thường, số và ký tự đặc biệt |
| `fullName` | string | ✅ | Họ và tên đầy đủ | Tối thiểu 2 ký tự, tối đa 100 ký tự |
| `phoneNumber` | string | ❌ | Số điện thoại | Format E.164 (ví dụ: +84901234567) |
| `role` | string | ❌ | Vai trò của người dùng | Một trong: `user`, `admin`, `moderator`. Mặc định: `user` |
| `metadata` | object | ❌ | Thông tin bổ sung tùy chỉnh | Object JSON, tối đa 10KB |
| `metadata.department` | string | ❌ | Phòng ban | Tối đa 50 ký tự |
| `metadata.employeeId` | string | ❌ | Mã nhân viên | Tối đa 20 ký tự |

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
    "fullName": "Nguyễn Văn A",
    "phoneNumber": "+84901234567",
    "role": "user",
    "metadata": {
      "department": "Engineering",
      "employeeId": "EMP001"
    }
  }'
```

#### Response thành công

**Status Code:** `201 Created`

```json
{
  "success": true,
  "data": {
    "id": "usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF",
    "email": "user@example.com",
    "fullName": "Nguyễn Văn A",
    "phoneNumber": "+84901234567",
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

#### Chi tiết các thuộc tính Response

| Thuộc tính | Kiểu | Mô tả |
| :--------- | :--- | :---- |
| `success` | boolean | Trạng thái xử lý request. `true` nếu thành công |
| `data.id` | string | ID duy nhất của user, format ULID với prefix `usr_` |
| `data.email` | string | Email đã đăng ký |
| `data.fullName` | string | Họ tên đầy đủ |
| `data.phoneNumber` | string | Số điện thoại (nếu có) |
| `data.role` | string | Vai trò được gán |
| `data.status` | string | Trạng thái tài khoản: `pending_verification`, `active`, `suspended`, `deleted` |
| `data.metadata` | object | Thông tin bổ sung |
| `data.createdAt` | string | Thời điểm tạo (ISO 8601) |
| `data.updatedAt` | string | Thời điểm cập nhật cuối (ISO 8601) |
| `meta.requestId` | string | ID của request để tracking |
| `meta.timestamp` | string | Thời điểm xử lý request |

#### Response lỗi

{{< tabs >}}

{{< tab name="400 Bad Request" >}}
**Nguyên nhân:** Request body không đúng format JSON hoặc thiếu trường bắt buộc.

```json
{
  "success": false,
  "error": {
    "code": "BAD_REQUEST",
    "message": "Request body không hợp lệ",
    "details": "Không thể parse JSON body"
  },
  "meta": {
    "requestId": "req-123456",
    "timestamp": "2024-02-20T10:30:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="401 Unauthorized" >}}
**Nguyên nhân:** Token không hợp lệ, hết hạn, hoặc không có quyền admin.

```json
{
  "success": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Token không hợp lệ hoặc đã hết hạn",
    "details": "Vui lòng đăng nhập lại để lấy token mới"
  },
  "meta": {
    "requestId": "req-123456",
    "timestamp": "2024-02-20T10:30:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="409 Conflict" >}}
**Nguyên nhân:** Email đã tồn tại trong hệ thống.

```json
{
  "success": false,
  "error": {
    "code": "CONFLICT",
    "message": "Email đã được sử dụng",
    "details": "Email user@example.com đã tồn tại trong hệ thống"
  },
  "meta": {
    "requestId": "req-123456",
    "timestamp": "2024-02-20T10:30:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="422 Validation Error" >}}
**Nguyên nhân:** Dữ liệu không đạt yêu cầu validation.

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Dữ liệu không hợp lệ",
    "details": [
      {
        "field": "password",
        "message": "Mật khẩu phải có ít nhất 8 ký tự, bao gồm chữ hoa, chữ thường, số và ký tự đặc biệt"
      },
      {
        "field": "phoneNumber",
        "message": "Số điện thoại không đúng format E.164"
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
**Nguyên nhân:** Lỗi server không xác định.

```json
{
  "success": false,
  "error": {
    "code": "INTERNAL_ERROR",
    "message": "Đã xảy ra lỗi hệ thống",
    "details": "Vui lòng thử lại sau hoặc liên hệ support"
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

### 4.2 Lấy thông tin người dùng

Lấy thông tin chi tiết của một người dùng theo ID.

#### Thông tin cơ bản

| Thuộc tính | Giá trị |
| :--------- | :------ |
| **Method** | `GET` |
| **URL** | `/api/v1/users/{userId}` |
| **Authentication** | Bearer Token |
| **Content-Type** | `application/json` |

#### Path Parameters

| Parameter | Kiểu | Bắt buộc | Mô tả |
| :-------- | :--- | :------- | :---- |
| `userId` | string | ✅ | ID của user cần lấy thông tin. Format: `usr_<ULID>` |

#### Headers

| Header | Kiểu | Bắt buộc | Mô tả |
| :----- | :--- | :------- | :---- |
| `Authorization` | string | ✅ | Token xác thực. Format: `Bearer <token>` |

#### cURL

```bash
curl --request GET \
  --url 'https://api.example.com/api/v1/users/usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF' \
  --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IlVzZXIiLCJpYXQiOjE1MTYyMzkwMjJ9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```

#### Response thành công

**Status Code:** `200 OK`

```json
{
  "success": true,
  "data": {
    "id": "usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF",
    "email": "user@example.com",
    "fullName": "Nguyễn Văn A",
    "phoneNumber": "+84901234567",
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

#### Response lỗi

{{< tabs >}}

{{< tab name="401 Unauthorized" >}}
```json
{
  "success": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "Token không hợp lệ hoặc đã hết hạn"
  },
  "meta": {
    "requestId": "req-789012",
    "timestamp": "2024-02-20T10:35:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="403 Forbidden" >}}
**Nguyên nhân:** User không có quyền xem thông tin của user khác.

```json
{
  "success": false,
  "error": {
    "code": "FORBIDDEN",
    "message": "Không có quyền truy cập tài nguyên này",
    "details": "Bạn chỉ có thể xem thông tin của chính mình"
  },
  "meta": {
    "requestId": "req-789012",
    "timestamp": "2024-02-20T10:35:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="404 Not Found" >}}
**Nguyên nhân:** User ID không tồn tại.

```json
{
  "success": false,
  "error": {
    "code": "NOT_FOUND",
    "message": "Không tìm thấy người dùng",
    "details": "User với ID usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF không tồn tại"
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

### 4.3 Danh sách người dùng (có phân trang)

Lấy danh sách người dùng với hỗ trợ phân trang, lọc và sắp xếp.

#### Thông tin cơ bản

| Thuộc tính | Giá trị |
| :--------- | :------ |
| **Method** | `GET` |
| **URL** | `/api/v1/users` |
| **Authentication** | Bearer Token (Admin) |

#### Query Parameters

| Parameter | Kiểu | Bắt buộc | Mô tả | Mặc định |
| :-------- | :--- | :------- | :---- | :------- |
| `page` | integer | ❌ | Số trang (bắt đầu từ 1) | `1` |
| `limit` | integer | ❌ | Số record mỗi trang | `20` |
| `sort` | string | ❌ | Trường sắp xếp | `createdAt` |
| `order` | string | ❌ | Thứ tự: `asc` hoặc `desc` | `desc` |
| `status` | string | ❌ | Lọc theo status | - |
| `role` | string | ❌ | Lọc theo role | - |
| `search` | string | ❌ | Tìm kiếm theo email hoặc tên | - |

#### cURL

```bash
curl --request GET \
  --url 'https://api.example.com/api/v1/users?page=1&limit=10&status=active&sort=createdAt&order=desc' \
  --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkFkbWluIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```

#### Response thành công

**Status Code:** `200 OK`

```json
{
  "success": true,
  "data": [
    {
      "id": "usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF",
      "email": "user1@example.com",
      "fullName": "Nguyễn Văn A",
      "role": "user",
      "status": "active",
      "createdAt": "2024-02-20T10:30:00.000Z"
    },
    {
      "id": "usr_01HQ3K5XJPZ8VWMN4YGCR2BGHI",
      "email": "user2@example.com",
      "fullName": "Trần Thị B",
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

Các file test cho module này:

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

### Chạy tests

```bash
# Chạy tất cả unit tests
npm run test:unit

# Chạy integration tests
npm run test:integration

# Chạy e2e tests
npm run test:e2e

# Chạy tests với coverage
npm run test:coverage
```

### Test cases quan trọng

| Test Case | Mô tả | Expected Result |
| :-------- | :---- | :-------------- |
| TC-001 | Tạo user với dữ liệu hợp lệ | Status 201, user được tạo |
| TC-002 | Tạo user với email trùng | Status 409, error CONFLICT |
| TC-003 | Tạo user với password yếu | Status 422, validation error |
| TC-004 | Lấy user không tồn tại | Status 404, error NOT_FOUND |
| TC-005 | Truy cập không có token | Status 401, error UNAUTHORIZED |

---

## 6. Troubleshooting

### Các lỗi thường gặp

{{< callout type="error" >}}
**Lỗi: "Token không hợp lệ" (401)**

**Nguyên nhân có thể:**
- Token đã hết hạn
- Token bị sai format
- Secret key không khớp

**Cách xử lý:**
1. Kiểm tra token có đúng format `Bearer <token>` không
2. Decode token để kiểm tra thời gian hết hạn
3. Đăng nhập lại để lấy token mới
{{< /callout >}}

{{< callout type="warning" >}}
**Lỗi: "Rate limit exceeded" (429)**

**Nguyên nhân:** Vượt quá giới hạn 100 request/phút

**Cách xử lý:**
1. Kiểm tra header `Retry-After` để biết thời gian chờ
2. Implement exponential backoff trong client
3. Liên hệ admin nếu cần tăng limit
{{< /callout >}}

### Liên hệ hỗ trợ

Nếu gặp vấn đề không thể tự xử lý:

- **Email:** support@example.com
- **Slack:** #api-support
- **Documentation:** https://docs.example.com

---

## 7. Changelog

| Phiên bản | Ngày | Thay đổi |
| :-------- | :--- | :------- |
| v1.2.0 | 2024-02-20 | Thêm field `metadata` cho user |
| v1.1.0 | 2024-02-01 | Thêm API phân trang |
| v1.0.0 | 2024-01-15 | Release đầu tiên |

---

## Tài liệu liên quan

{{< cards >}}
  {{< card link="../markdown" title="Markdown" icon="document-text" >}}
  {{< card link="../syntax-highlighting" title="Syntax Highlighting" icon="code" >}}
  {{< card link="../diagrams" title="Diagrams" icon="chart-bar" >}}
{{< /cards >}}
