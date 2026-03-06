---
title: getRSCStream
unstable: true
---

# unstable_getRSCStream

[MODES: data]

<br />
<br />

<docs-warning>此 API 是实验性的，可能在次要/补丁版本中发生破坏性变更。请谨慎使用，并**密切**关注发布说明中的相关变更。</docs-warning>

## 概述

获取预渲染的 [RSC](https://react.dev/reference/rsc/server-components) 流用于注水。通常直接传递给你的 `react-server-dom-xyz/client` 的 `createFromReadableStream`。

```tsx
import { startTransition, StrictMode } from "react";
import { hydrateRoot } from "react-dom/client";
import {
  unstable_getRSCStream as getRSCStream,
  unstable_RSCHydratedRouter as RSCHydratedRouter,
} from "react-router";
import type { unstable_RSCPayload as RSCPayload } from "react-router";

createFromReadableStream(getRSCStream()).then(
  (payload: RSCServerPayload) => {
    startTransition(async () => {
      hydrateRoot(
        document,
        <StrictMode>
          <RSCHydratedRouter {...props} />
        </StrictMode>,
        {
          // 选项
        },
      );
    });
  },
);
```

## 函数签名

```tsx
function getRSCStream(): ReadableStream;
```

## 返回值

包含用于注水的 [RSC](https://react.dev/reference/rsc/server-components) 数据的 [`ReadableStream`](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream)。
