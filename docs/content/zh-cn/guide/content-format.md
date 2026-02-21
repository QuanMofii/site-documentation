---
title: 内容格式标准
weight: 10
---

本文档描述了大型项目中编写文档的标准结构。目标是确保所有团队成员的一致性、可维护性和清晰度。

<!--more-->

## 概述

项目中的每个文档都应遵循以下结构：

1. **简介** - 功能/模块概述
2. **业务逻辑** - 业务流程说明
3. **实现逻辑** - 技术实现细节
4. **API参考** - 完整的API文档
5. **测试** - 测试指南
6. **故障排除** - 常见问题解决

---

## 1. 简介

本节提供功能或模块的概述。

### 目的

简要描述此功能在系统中的目的。

### 范围

- 此功能**能做什么**
- 此功能**不能做什么**
- 相关模块/服务

### 前置条件

| 要求 | 版本 | 备注 |
| :--- | :--- | :--- |
| Node.js | >= 18.0 | 必需 |
| Redis | >= 7.0 | 用于缓存 |
| PostgreSQL | >= 15 | 主数据库 |

---

## 2. 业务逻辑

### 业务流程

描述功能的主要业务流程。

```mermaid
flowchart TD
    A[用户发送请求] --> B{验证令牌}
    B -->|有效| C[处理业务逻辑]
    B -->|无效| D[返回401错误]
    C --> E[保存到数据库]
    E --> F[返回结果]
```

### 业务规则

| # | 规则 | 描述 |
| :- | :-- | :--- |
| 1 | 需要认证 | 所有请求必须有有效令牌 |
| 2 | 速率限制 | 每用户最多100请求/分钟 |
| 3 | 验证 | 输入数据必须通过验证 |

### 特殊情况

- **情况1**: 当用户未验证邮箱时 → 仅允许读取，不允许写入操作
- **情况2**: 当系统过载时 → 返回503和retry-after头

---

## 3. 实现逻辑

### 技术架构

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

### 详细处理流程

{{< steps >}}

### 步骤1: 接收请求

客户端向API Gateway发送请求。Gateway执行：
- 验证请求格式
- 从头部提取JWT令牌
- 转发到相应服务

### 步骤2: 认证

Auth Service检查：
- 令牌是否有效？
- 令牌是否过期？
- 用户是否有访问权限？

### 步骤3: 处理业务逻辑

服务处理业务逻辑：
- 验证输入数据
- 执行业务逻辑
- 与数据库交互

### 步骤4: 返回结果

打包响应并以标准格式返回给客户端。

{{< /steps >}}

### 目录结构

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

## 4. API参考

### 4.1 创建新用户

在系统中创建新的用户账户。

#### 基本信息

| 属性 | 值 |
| :-- | :- |
| **方法** | `POST` |
| **URL** | `/api/v1/users` |
| **认证** | Bearer Token (Admin) |
| **Content-Type** | `application/json` |

#### 请求头

| 头部 | 类型 | 必需 | 描述 |
| :-- | :-- | :-- | :--- |
| `Authorization` | string | ✅ | 认证令牌。格式: `Bearer <token>` |
| `Content-Type` | string | ✅ | 必须是 `application/json` |
| `X-Request-ID` | string | ❌ | 请求追踪ID。未提供时自动生成 |
| `Accept-Language` | string | ❌ | 响应语言。默认: `zh-cn` |

#### 请求体

```json
{
  "email": "user@example.com",
  "password": "SecureP@ss123",
  "fullName": "张三",
  "phoneNumber": "+8613812345678",
  "role": "user",
  "metadata": {
    "department": "Engineering",
    "employeeId": "EMP001"
  }
}
```

#### 请求属性详情

| 属性 | 类型 | 必需 | 描述 | 约束 |
| :-- | :-- | :-- | :--- | :--- |
| `email` | string | ✅ | 用户邮箱地址。用作登录用户名 | 有效邮箱，最多255字符，系统内唯一 |
| `password` | string | ✅ | 登录密码 | 最少8字符，必须包含大写、小写、数字和特殊字符 |
| `fullName` | string | ✅ | 全名 | 最少2字符，最多100字符 |
| `phoneNumber` | string | ❌ | 电话号码 | E.164格式（例如: +8613812345678） |
| `role` | string | ❌ | 用户角色 | `user`, `admin`, `moderator`之一。默认: `user` |
| `metadata` | object | ❌ | 自定义附加信息 | JSON对象，最大10KB |
| `metadata.department` | string | ❌ | 部门 | 最多50字符 |
| `metadata.employeeId` | string | ❌ | 员工ID | 最多20字符 |

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
    "fullName": "张三",
    "phoneNumber": "+8613812345678",
    "role": "user",
    "metadata": {
      "department": "Engineering",
      "employeeId": "EMP001"
    }
  }'
```

#### 成功响应

**状态码:** `201 Created`

```json
{
  "success": true,
  "data": {
    "id": "usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF",
    "email": "user@example.com",
    "fullName": "张三",
    "phoneNumber": "+8613812345678",
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

#### 响应属性详情

| 属性 | 类型 | 描述 |
| :-- | :-- | :--- |
| `success` | boolean | 请求处理状态。成功时为`true` |
| `data.id` | string | 唯一用户ID，带`usr_`前缀的ULID格式 |
| `data.email` | string | 注册邮箱 |
| `data.fullName` | string | 全名 |
| `data.phoneNumber` | string | 电话号码（如果提供） |
| `data.role` | string | 分配的角色 |
| `data.status` | string | 账户状态: `pending_verification`, `active`, `suspended`, `deleted` |
| `data.metadata` | object | 附加信息 |
| `data.createdAt` | string | 创建时间戳（ISO 8601） |
| `data.updatedAt` | string | 最后更新时间戳（ISO 8601） |
| `meta.requestId` | string | 追踪用请求ID |
| `meta.timestamp` | string | 请求处理时间戳 |

#### 错误响应

{{< tabs >}}

{{< tab name="400 Bad Request" >}}
**原因:** 请求体不是有效的JSON格式或缺少必需字段。

```json
{
  "success": false,
  "error": {
    "code": "BAD_REQUEST",
    "message": "请求体无效",
    "details": "无法解析JSON体"
  },
  "meta": {
    "requestId": "req-123456",
    "timestamp": "2024-02-20T10:30:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="401 Unauthorized" >}}
**原因:** 令牌无效、过期或缺少管理员权限。

```json
{
  "success": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "令牌无效或已过期",
    "details": "请重新登录以获取新令牌"
  },
  "meta": {
    "requestId": "req-123456",
    "timestamp": "2024-02-20T10:30:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="409 Conflict" >}}
**原因:** 邮箱已存在于系统中。

```json
{
  "success": false,
  "error": {
    "code": "CONFLICT",
    "message": "邮箱已被使用",
    "details": "邮箱 user@example.com 已存在于系统中"
  },
  "meta": {
    "requestId": "req-123456",
    "timestamp": "2024-02-20T10:30:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="422 Validation Error" >}}
**原因:** 数据不符合验证要求。

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "数据无效",
    "details": [
      {
        "field": "password",
        "message": "密码必须至少8个字符，包括大写、小写、数字和特殊字符"
      },
      {
        "field": "phoneNumber",
        "message": "电话号码不符合E.164格式"
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
**原因:** 未知服务器错误。

```json
{
  "success": false,
  "error": {
    "code": "INTERNAL_ERROR",
    "message": "发生系统错误",
    "details": "请稍后重试或联系支持"
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

### 4.2 获取用户信息

通过ID获取用户的详细信息。

#### 基本信息

| 属性 | 值 |
| :-- | :- |
| **方法** | `GET` |
| **URL** | `/api/v1/users/{userId}` |
| **认证** | Bearer Token |
| **Content-Type** | `application/json` |

#### 路径参数

| 参数 | 类型 | 必需 | 描述 |
| :-- | :-- | :-- | :--- |
| `userId` | string | ✅ | 要获取的用户ID。格式: `usr_<ULID>` |

#### 请求头

| 头部 | 类型 | 必需 | 描述 |
| :-- | :-- | :-- | :--- |
| `Authorization` | string | ✅ | 认证令牌。格式: `Bearer <token>` |

#### cURL

```bash
curl --request GET \
  --url 'https://api.example.com/api/v1/users/usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF' \
  --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IlVzZXIiLCJpYXQiOjE1MTYyMzkwMjJ9.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```

#### 成功响应

**状态码:** `200 OK`

```json
{
  "success": true,
  "data": {
    "id": "usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF",
    "email": "user@example.com",
    "fullName": "张三",
    "phoneNumber": "+8613812345678",
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

#### 错误响应

{{< tabs >}}

{{< tab name="401 Unauthorized" >}}
```json
{
  "success": false,
  "error": {
    "code": "UNAUTHORIZED",
    "message": "令牌无效或已过期"
  },
  "meta": {
    "requestId": "req-789012",
    "timestamp": "2024-02-20T10:35:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="403 Forbidden" >}}
**原因:** 用户没有查看其他用户信息的权限。

```json
{
  "success": false,
  "error": {
    "code": "FORBIDDEN",
    "message": "拒绝访问此资源",
    "details": "您只能查看自己的信息"
  },
  "meta": {
    "requestId": "req-789012",
    "timestamp": "2024-02-20T10:35:00.000Z"
  }
}
```
{{< /tab >}}

{{< tab name="404 Not Found" >}}
**原因:** 用户ID不存在。

```json
{
  "success": false,
  "error": {
    "code": "NOT_FOUND",
    "message": "用户未找到",
    "details": "ID为 usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF 的用户不存在"
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

### 4.3 用户列表（带分页）

获取支持分页、过滤和排序的用户列表。

#### 基本信息

| 属性 | 值 |
| :-- | :- |
| **方法** | `GET` |
| **URL** | `/api/v1/users` |
| **认证** | Bearer Token (Admin) |

#### 查询参数

| 参数 | 类型 | 必需 | 描述 | 默认值 |
| :-- | :-- | :-- | :--- | :---- |
| `page` | integer | ❌ | 页码（从1开始） | `1` |
| `limit` | integer | ❌ | 每页记录数 | `20` |
| `sort` | string | ❌ | 排序字段 | `createdAt` |
| `order` | string | ❌ | 顺序: `asc` 或 `desc` | `desc` |
| `status` | string | ❌ | 按状态过滤 | - |
| `role` | string | ❌ | 按角色过滤 | - |
| `search` | string | ❌ | 按邮箱或姓名搜索 | - |

#### cURL

```bash
curl --request GET \
  --url 'https://api.example.com/api/v1/users?page=1&limit=10&status=active&sort=createdAt&order=desc' \
  --header 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkFkbWluIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c'
```

#### 成功响应

**状态码:** `200 OK`

```json
{
  "success": true,
  "data": [
    {
      "id": "usr_01HQ3K5XJPZ8VWMN4YGCR2BDEF",
      "email": "user1@example.com",
      "fullName": "张三",
      "role": "user",
      "status": "active",
      "createdAt": "2024-02-20T10:30:00.000Z"
    },
    {
      "id": "usr_01HQ3K5XJPZ8VWMN4YGCR2BGHI",
      "email": "user2@example.com",
      "fullName": "李四",
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

## 5. 测试

### 单元测试

此模块的测试文件:

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

### 运行测试

```bash
# 运行所有单元测试
npm run test:unit

# 运行集成测试
npm run test:integration

# 运行e2e测试
npm run test:e2e

# 运行带覆盖率的测试
npm run test:coverage
```

### 重要测试用例

| 测试用例 | 描述 | 预期结果 |
| :------ | :--- | :------ |
| TC-001 | 使用有效数据创建用户 | 状态201，用户已创建 |
| TC-002 | 使用重复邮箱创建用户 | 状态409，错误CONFLICT |
| TC-003 | 使用弱密码创建用户 | 状态422，验证错误 |
| TC-004 | 获取不存在的用户 | 状态404，错误NOT_FOUND |
| TC-005 | 无令牌访问 | 状态401，错误UNAUTHORIZED |

---

## 6. 故障排除

### 常见错误

{{< callout type="error" >}}
**错误: "令牌无效" (401)**

**可能原因:**
- 令牌已过期
- 令牌格式不正确
- 密钥不匹配

**解决方法:**
1. 检查令牌是否为正确格式 `Bearer <token>`
2. 解码令牌检查过期时间
3. 重新登录获取新令牌
{{< /callout >}}

{{< callout type="warning" >}}
**错误: "超出速率限制" (429)**

**原因:** 超过100请求/分钟的限制

**解决方法:**
1. 检查 `Retry-After` 头获取等待时间
2. 在客户端实现指数退避
3. 如需增加限制请联系管理员
{{< /callout >}}

### 支持联系

如果遇到无法解决的问题:

- **邮箱:** support@example.com
- **Slack:** #api-support
- **文档:** https://docs.example.com

---

## 7. 变更日志

| 版本 | 日期 | 变更 |
| :-- | :--- | :--- |
| v1.2.0 | 2024-02-20 | 为用户添加`metadata`字段 |
| v1.1.0 | 2024-02-01 | 添加分页API |
| v1.0.0 | 2024-01-15 | 初始发布 |

---

## 相关文档

{{< cards >}}
  {{< card link="../markdown" title="Markdown" icon="document-text" >}}
  {{< card link="../syntax-highlighting" title="语法高亮" icon="code" >}}
  {{< card link="../diagrams" title="图表" icon="chart-bar" >}}
{{< /cards >}}
