---
title: entry.client.tsx
order: 4
---

# entry.client.tsx

[MODES: framework]

## 概述

<docs-info>
此文件是可选的
</docs-info>

此文件是浏览器的入口文件，负责注水[服务端入口模块][server-entry]生成的标记。

这是在浏览器中运行的第一段代码。你可以在这里初始化其他客户端代码，如客户端库、添加仅客户端的 Provider 等。

```tsx filename=app/entry.client.tsx
import { startTransition, StrictMode } from "react";
import { hydrateRoot } from "react-dom/client";
import { HydratedRouter } from "react-router/dom";

startTransition(() => {
  hydrateRoot(
    document,
    <StrictMode>
      <HydratedRouter />
    </StrictMode>,
  );
});
```

## 生成 `entry.client.tsx`

默认情况下，React Router 会为你处理客户端应用的注水。你可以通过以下命令显示默认的客户端入口文件：

```shellscript nonumber
npx react-router reveal
```

[server-entry]: ./entry.server.tsx
