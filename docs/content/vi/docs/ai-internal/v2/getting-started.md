---
title: Bắt đầu
weight: 1
---

## Bắt đầu với AI Internal v2

Đây là hướng dẫn bắt đầu cho **AI Internal v2** (phiên bản mới nhất).

### Cài đặt

```bash
npm install @company/ai-internal@2.x
```

### Cách dùng cơ bản

```javascript
import { AI } from '@company/ai-internal';

const ai = new AI({
  version: 2,
  apiKey: process.env.AI_API_KEY
});

const result = await ai.process({ input: 'Hello' });
```

### Mới trong v2

- **API `process()` mới** – Giao diện đơn giản hơn
- **Hỗ trợ streaming** – Phản hồi theo thời gian thực
- **TypeScript** – Định nghĩa kiểu đầy đủ
