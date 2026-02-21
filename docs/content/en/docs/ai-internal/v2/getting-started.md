---
title: Getting Started
weight: 1
---

## Getting Started with AI Internal v2

This is the getting started guide for **AI Internal v2** (latest).

### Installation

```bash
npm install @company/ai-internal@2.x
```

### Basic Usage

```javascript
import { AI } from '@company/ai-internal';

const ai = new AI({
  version: 2,
  apiKey: process.env.AI_API_KEY
});

const result = await ai.process({ input: 'Hello' });
```

### What's New in v2

- **New `process()` API** - Simplified interface
- **Streaming support** - Real-time responses
- **TypeScript** - Full type definitions
