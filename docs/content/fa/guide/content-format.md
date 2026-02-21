---
title: استاندارد فرمت محتوا
weight: 10
---

این سند ساختار استاندارد برای نوشتن مستندات در پروژه‌های بزرگ را توضیح می‌دهد. هدف اطمینان از یکپارچگی، قابلیت نگهداری و وضوح برای همه اعضای تیم است.

<!--more-->

## نمای کلی

هر سند در پروژه باید از این ساختار پیروی کند:

1. **مقدمه** - نمای کلی ویژگی/ماژول
2. **منطق کسب‌وکار** - توضیح فرآیندهای کسب‌وکار
3. **منطق پیاده‌سازی** - جزئیات فنی پیاده‌سازی
4. **مرجع API** - مستندات کامل API
5. **تست** - راهنمای تست
6. **عیب‌یابی** - حل مشکلات رایج

---

## 1. مقدمه

این بخش نمای کلی از ویژگی یا ماژول را ارائه می‌دهد.

### هدف

به طور خلاصه هدف این ویژگی در سیستم را توضیح دهید.

### دامنه

- این ویژگی **چه کاری می‌تواند انجام دهد**
- این ویژگی **چه کاری نمی‌تواند انجام دهد**
- ماژول‌ها/سرویس‌های مرتبط

### پیش‌نیازها

| نیازمندی | نسخه | یادداشت |
| :------- | :--- | :------ |
| Node.js | >= 18.0 | الزامی |
| Redis | >= 7.0 | برای کش |
| PostgreSQL | >= 15 | پایگاه داده اصلی |

---

## 2. منطق کسب‌وکار

### فرآیند کسب‌وکار

جریان اصلی کسب‌وکار ویژگی را توضیح دهید.

```mermaid
flowchart TD
    A[کاربر درخواست ارسال می‌کند] --> B{اعتبارسنجی توکن}
    B -->|معتبر| C[پردازش منطق کسب‌وکار]
    B -->|نامعتبر| D[بازگشت خطای 401]
    C --> E[ذخیره در پایگاه داده]
    E --> F[بازگشت نتیجه]
```

### قوانین کسب‌وکار

| # | قانون | توضیحات |
| :- | :---- | :------ |
| 1 | احراز هویت الزامی | همه درخواست‌ها باید توکن معتبر داشته باشند |
| 2 | محدودیت نرخ | حداکثر 100 درخواست/دقیقه/کاربر |
| 3 | اعتبارسنجی | داده‌های ورودی باید از اعتبارسنجی عبور کنند |

### موارد خاص

- **مورد 1**: وقتی کاربر ایمیل را تأیید نکرده → فقط خواندن مجاز، عملیات نوشتن غیرمجاز
- **مورد 2**: وقتی سیستم بیش از حد بارگذاری شده → بازگشت 503 با هدر retry-after

---

## 3. منطق پیاده‌سازی

### معماری فنی

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

### جریان پردازش جزئی

{{< steps >}}

### مرحله 1: دریافت درخواست

کلاینت درخواست را به API Gateway ارسال می‌کند. Gateway انجام می‌دهد:
- اعتبارسنجی فرمت درخواست
- استخراج توکن JWT از هدر
- ارسال به سرویس مربوطه

### مرحله 2: احراز هویت

Auth Service بررسی می‌کند:
- آیا توکن معتبر است؟
- آیا توکن منقضی شده؟
- آیا کاربر حق دسترسی دارد؟

### مرحله 3: پردازش منطق کسب‌وکار

سرویس منطق کسب‌وکار را پردازش می‌کند:
- اعتبارسنجی داده‌های ورودی
- اجرای منطق کسب‌وکار
- تعامل با پایگاه داده

### مرحله 4: بازگشت نتیجه

بسته‌بندی پاسخ و بازگشت به کلاینت با فرمت استاندارد.

{{< /steps >}}

### ساختار دایرکتوری

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

## 4. مرجع API

### 4.1 ایجاد کاربر جدید

ایجاد یک حساب کاربری جدید در سیستم.

#### اطلاعات پایه

| ویژگی | مقدار |
| :---- | :---- |
| **متد** | `POST` |
| **URL** | `/api/v1/users` |
| **احراز هویت** | Bearer Token (Admin) |
| **Content-Type** | `application/json` |

#### هدرها

| هدر | نوع | الزامی | توضیحات |
| :-- | :-- | :----- | :------ |
| `Authorization` | string | ✅ | توکن احراز هویت. فرمت: `Bearer <token>` |
| `Content-Type` | string | ✅ | باید `application/json` باشد |
| `X-Request-ID` | string | ❌ | شناسه برای ردیابی درخواست. در صورت عدم ارائه خودکار تولید می‌شود |
| `Accept-Language` | string | ❌ | زبان پاسخ. پیش‌فرض: `fa` |

#### بدنه درخواست

```json
{
  "email": "user@example.com",
  "password": "SecureP@ss123",
  "fullName": "علی محمدی",
  "phoneNumber": "+989121234567",
  "role": "user",
  "metadata": {
    "department": "Engineering",
    "employeeId": "EMP001"
  }
}
```

#### جزئیات ویژگی‌های درخواست

| ویژگی | نوع | الزامی | توضیحات | محدودیت‌ها |
| :---- | :-- | :----- | :------ | :-------- |
| `email` | string | ✅ | آدرس ایمیل کاربر. به عنوان نام کاربری ورود استفاده می‌شود | ایمیل معتبر، حداکثر 255 کاراکتر، یکتا در سیستم |
| `password` | string | ✅ | رمز عبور ورود | حداقل 8 کاراکتر، باید شامل حروف بزرگ، کوچک، عدد و کاراکتر خاص باشد |
| `fullName` | string | ✅ | نام کامل | حداقل 2 کاراکتر، حداکثر 100 کاراکتر |
| `phoneNumber` | string | ❌ | شماره تلفن | فرمت E.164 (مثال: +989121234567) |
| `role` | string | ❌ | نقش کاربر | یکی از: `user`, `admin`, `moderator`. پیش‌فرض: `user` |
| `metadata` | object | ❌ | اطلاعات اضافی سفارشی | آبجکت JSON، حداکثر 10KB |
| `metadata.department` | string | ❌ | بخش | حداکثر 50 کاراکتر |
| `metadata.employeeId` | string | ❌ | شناسه کارمند | حداکثر 20 کاراکتر |

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
    "fullName": "علی محمدی",
    "phoneNumber": "+989121234567",
    "role": "user",
    "metadata": {
      "department": "Engineering",
      "employeeId": "EMP001"
    }
  }'
```

#### پاسخ موفق

**کد وضعیت:** `201 Created`

```json
{
  "success": true,
  "data": {
    "id": "usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF",
    "email": "user@example.com",
    "fullName": "علی محمدی",
    "phoneNumber": "+989121234567",
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

#### جزئیات ویژگی‌های پاسخ

| ویژگی | نوع | توضیحات |
| :---- | :-- | :------ |
| `success` | boolean | وضعیت پردازش درخواست. `true` در صورت موفقیت |
| `data.id` | string | شناسه یکتای کاربر، فرمت ULID با پیشوند `usr_` |
| `data.email` | string | ایمیل ثبت شده |
| `data.fullName` | string | نام کامل |
| `data.phoneNumber` | string | شماره تلفن (در صورت ارائه) |
| `data.role` | string | نقش اختصاص داده شده |
| `data.status` | string | وضعیت حساب: `pending_verification`, `active`, `suspended`, `deleted` |
| `data.metadata` | object | اطلاعات اضافی |
| `data.createdAt` | string | زمان ایجاد (ISO 8601) |
| `data.updatedAt` | string | زمان آخرین به‌روزرسانی (ISO 8601) |
| `meta.requestId` | string | شناسه درخواست برای ردیابی |
| `meta.timestamp` | string | زمان پردازش درخواست |

#### پاسخ‌های خطا

{{< tabs >}}

{{< tab name="400 Bad Request" >}}
**علت:** بدنه درخواست فرمت JSON معتبر نیست یا فیلدهای الزامی وجود ندارند.

```json
{
  "success": false,
  "error": {
    "code": "BAD_REQUEST",
    "message": "بدنه درخواست نامعتبر است",
    "details": "امکان تجزیه بدنه JSON وجود ندارد"
  },
  "meta": {
    "requestId": "req-123456",
    "timestamp": "2024-02-20T10:30:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="401 Unauthorized" >}}
**علت:** توکن نامعتبر، منقضی شده یا فاقد امتیازات مدیر است.

```json
{
  "success": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "توکن نامعتبر یا منقضی شده است",
    "details": "لطفاً دوباره وارد شوید تا توکن جدید دریافت کنید"
  },
  "meta": {
    "requestId": "req-123456",
    "timestamp": "2024-02-20T10:30:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="409 Conflict" >}}
**علت:** ایمیل قبلاً در سیستم وجود دارد.

```json
{
  "success": false,
  "error": {
    "code": "CONFLICT",
    "message": "ایمیل قبلاً استفاده شده است",
    "details": "ایمیل user@example.com قبلاً در سیستم وجود دارد"
  },
  "meta": {
    "requestId": "req-123456",
    "timestamp": "2024-02-20T10:30:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="422 Validation Error" >}}
**علت:** داده‌ها الزامات اعتبارسنجی را برآورده نمی‌کنند.

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "داده‌ها نامعتبر هستند",
    "details": [
      {
        "field": "password",
        "message": "رمز عبور باید حداقل 8 کاراکتر باشد، شامل حروف بزرگ، کوچک، عدد و کاراکتر خاص"
      },
      {
        "field": "phoneNumber",
        "message": "شماره تلفن با فرمت E.164 مطابقت ندارد"
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
**علت:** خطای ناشناخته سرور.

```json
{
  "success": false,
  "error": {
    "code": "INTERNAL_ERROR",
    "message": "خطای سیستمی رخ داده است",
    "details": "لطفاً بعداً دوباره تلاش کنید یا با پشتیبانی تماس بگیرید"
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

### 4.2 دریافت اطلاعات کاربر

دریافت اطلاعات جزئی یک کاربر با شناسه.

#### اطلاعات پایه

| ویژگی | مقدار |
| :---- | :---- |
| **متد** | `GET` |
| **URL** | `/api/v1/users/{userId}` |
| **احراز هویت** | Bearer Token |
| **Content-Type** | `application/json` |

#### پارامترهای مسیر

| پارامتر | نوع | الزامی | توضیحات |
| :------ | :-- | :----- | :------ |
| `userId` | string | ✅ | شناسه کاربر برای دریافت. فرمت: `usr_<ULID>` |

#### هدرها

| هدر | نوع | الزامی | توضیحات |
| :-- | :-- | :----- | :------ |
| `Authorization` | string | ✅ | توکن احراز هویت. فرمت: `Bearer <token>` |

#### cURL

```bash
curl --request GET \
  --url 'https://api.example.com/api/v1/users/usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF' \
  --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IlVzZXIiLCJpYXQiOjE1MTYyMzkwMjJ9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```

#### پاسخ موفق

**کد وضعیت:** `200 OK`

```json
{
  "success": true,
  "data": {
    "id": "usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF",
    "email": "user@example.com",
    "fullName": "علی محمدی",
    "phoneNumber": "+989121234567",
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

#### پاسخ‌های خطا

{{< tabs >}}

{{< tab name="401 Unauthorized" >}}
```json
{
  "success": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "توکن نامعتبر یا منقضی شده است"
  },
  "meta": {
    "requestId": "req-789012",
    "timestamp": "2024-02-20T10:35:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="403 Forbidden" >}}
**علت:** کاربر مجوز مشاهده اطلاعات کاربر دیگر را ندارد.

```json
{
  "success": false,
  "error": {
    "code": "FORBIDDEN",
    "message": "دسترسی به این منبع رد شده است",
    "details": "شما فقط می‌توانید اطلاعات خود را مشاهده کنید"
  },
  "meta": {
    "requestId": "req-789012",
    "timestamp": "2024-02-20T10:35:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="404 Not Found" >}}
**علت:** شناسه کاربر وجود ندارد.

```json
{
  "success": false,
  "error": {
    "code": "NOT_FOUND",
    "message": "کاربر یافت نشد",
    "details": "کاربر با شناسه usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF وجود ندارد"
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

### 4.3 لیست کاربران (با صفحه‌بندی)

دریافت لیست کاربران با پشتیبانی از صفحه‌بندی، فیلتر و مرتب‌سازی.

#### اطلاعات پایه

| ویژگی | مقدار |
| :---- | :---- |
| **متد** | `GET` |
| **URL** | `/api/v1/users` |
| **احراز هویت** | Bearer Token (Admin) |

#### پارامترهای کوئری

| پارامتر | نوع | الزامی | توضیحات | پیش‌فرض |
| :------ | :-- | :----- | :------ | :------ |
| `page` | integer | ❌ | شماره صفحه (از 1 شروع می‌شود) | `1` |
| `limit` | integer | ❌ | تعداد رکورد در هر صفحه | `20` |
| `sort` | string | ❌ | فیلد مرتب‌سازی | `createdAt` |
| `order` | string | ❌ | ترتیب: `asc` یا `desc` | `desc` |
| `status` | string | ❌ | فیلتر بر اساس وضعیت | - |
| `role` | string | ❌ | فیلتر بر اساس نقش | - |
| `search` | string | ❌ | جستجو بر اساس ایمیل یا نام | - |

#### cURL

```bash
curl --request GET \
  --url 'https://api.example.com/api/v1/users?page=1&limit=10&status=active&sort=createdAt&order=desc' \
  --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkFkbWluIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```

#### پاسخ موفق

**کد وضعیت:** `200 OK`

```json
{
  "success": true,
  "data": [
    {
      "id": "usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF",
      "email": "user1@example.com",
      "fullName": "علی محمدی",
      "role": "user",
      "status": "active",
      "createdAt": "2024-02-20T10:30:00.000Z"
    },
    {
      "id": "usr_01HQ3K5XJPZ8VWMN4YGCR2BGHI",
      "email": "user2@example.com",
      "fullName": "مریم احمدی",
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

## 5. تست

### تست‌های واحد

فایل‌های تست برای این ماژول:

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

### اجرای تست‌ها

```bash
# اجرای همه تست‌های واحد
npm run test:unit

# اجرای تست‌های یکپارچگی
npm run test:integration

# اجرای تست‌های e2e
npm run test:e2e

# اجرای تست‌ها با پوشش
npm run test:coverage
```

### موارد تست مهم

| مورد تست | توضیحات | نتیجه مورد انتظار |
| :------- | :------ | :--------------- |
| TC-001 | ایجاد کاربر با داده‌های معتبر | وضعیت 201، کاربر ایجاد شد |
| TC-002 | ایجاد کاربر با ایمیل تکراری | وضعیت 409، خطای CONFLICT |
| TC-003 | ایجاد کاربر با رمز عبور ضعیف | وضعیت 422، خطای اعتبارسنجی |
| TC-004 | دریافت کاربر ناموجود | وضعیت 404، خطای NOT_FOUND |
| TC-005 | دسترسی بدون توکن | وضعیت 401، خطای UNAUTHORIZED |

---

## 6. عیب‌یابی

### خطاهای رایج

{{< callout type="error" >}}
**خطا: "توکن نامعتبر است" (401)**

**علل احتمالی:**
- توکن منقضی شده است
- فرمت توکن نادرست است
- کلید مخفی مطابقت ندارد

**راه‌حل:**
1. بررسی کنید توکن فرمت صحیح `Bearer <token>` دارد
2. توکن را رمزگشایی کنید تا زمان انقضا را بررسی کنید
3. دوباره وارد شوید تا توکن جدید دریافت کنید
{{< /callout >}}

{{< callout type="warning" >}}
**خطا: "محدودیت نرخ بیش از حد" (429)**

**علت:** بیش از محدودیت 100 درخواست/دقیقه

**راه‌حل:**
1. هدر `Retry-After` را برای زمان انتظار بررسی کنید
2. پس‌رفت نمایی را در کلاینت پیاده‌سازی کنید
3. در صورت نیاز به افزایش محدودیت با مدیر تماس بگیرید
{{< /callout >}}

### تماس با پشتیبانی

اگر با مشکلاتی مواجه شدید که نمی‌توانید حل کنید:

- **ایمیل:** support@example.com
- **Slack:** #api-support
- **مستندات:** https://docs.example.com

---

## 7. تاریخچه تغییرات

| نسخه | تاریخ | تغییرات |
| :--- | :---- | :------ |
| v1.2.0 | 2024-02-20 | افزودن فیلد `metadata` برای کاربر |
| v1.1.0 | 2024-02-01 | افزودن API صفحه‌بندی |
| v1.0.0 | 2024-01-15 | انتشار اولیه |

---

## مستندات مرتبط

{{< cards >}}
  {{< card link="../markdown" title="Markdown" icon="document-text" >}}
  {{< card link="../syntax-highlighting" title="هایلایت نحو" icon="code" >}}
  {{< card link="../diagrams" title="نمودارها" icon="chart-bar" >}}
{{< /cards >}}
