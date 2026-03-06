---
title: .client 模块
---

# `.client` 模块

[MODES: framework]

## 概述

你可能有在浏览器中使用模块副作用的文件或依赖。你可以在文件名上使用 `*.client.ts` 或将文件放在 `.client` 目录中，以强制将它们排除在服务端包之外。

```ts filename=feature-check.client.ts
// 这会破坏服务端
export const supportsVibrationAPI =
  "vibrate" in window.navigator;
```

注意，从此模块导出的值在服务端上都是 `undefined`，因此唯一可以使用它们的地方是在 [`useEffect`][use_effect] 和用户事件（如点击处理器）中。

```ts
import { supportsVibrationAPI } from "./feature-check.client.ts";

console.log(supportsVibrationAPI);
// 服务端: undefined
// 客户端: true | false
```

<docs-info>

如果你需要更精细地控制客户端/服务端包中包含的内容，请查看 [`vite-env-only` 插件](https://github.com/pcattori/vite-env-only)。

</docs-info>

## 使用模式

### 单个文件

通过在文件名中添加 `.client` 将单个文件标记为仅客户端：

```txt
app/
├── utils.client.ts        👈 仅客户端文件
├── feature-detection.client.ts
└── root.tsx
```

### 客户端目录

通过在目录名中使用 `.client` 将整个目录标记为仅客户端：

```txt
app/
├── .client/               👈 整个目录为仅客户端
│   ├── analytics.ts
│   ├── feature-detection.ts
│   └── browser-utils.ts
├── components/
└── root.tsx
```

## 示例

### 浏览器特性检测

```ts filename=app/utils/browser.client.ts
export const canUseDOM = typeof window !== "undefined";

export const hasWebGL = !!window.WebGLRenderingContext;

export const supportsVibrationAPI =
  "vibrate" in window.navigator;
```

### 仅客户端库

```ts filename=app/analytics.client.ts
// 这在服务端会报错
import { track } from "some-browser-only-analytics-lib";

export function trackEvent(eventName: string, data: any) {
  track(eventName, data);
}
```

### 使用客户端模块

```tsx filename=app/routes/dashboard.tsx
import { useEffect } from "react";
import {
  canUseDOM,
  supportsLocalStorage,
  supportsVibrationAPI,
} from "../utils/browser.client.ts";
import { trackEvent } from "../analytics.client.ts";

export default function Dashboard() {
  useEffect(() => {
    // 这些值在服务端上为 undefined
    if (canUseDOM && supportsVibrationAPI) {
      console.log("Device supports vibration");
    }

    // 安全的 localStorage 使用
    const savedTheme =
      supportsLocalStorage.getItem("theme");
    if (savedTheme) {
      document.body.className = savedTheme;
    }

    trackEvent("dashboard_viewed", {
      timestamp: Date.now(),
    });
  }, []);

  return <div>Dashboard</div>;
}
```

[use_effect]: https://react.dev/reference/react/useEffect
