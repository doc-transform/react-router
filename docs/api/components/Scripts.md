---
title: Scripts
---

# Scripts

[MODES: framework]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.Scripts.html)

渲染应用的客户端运行时。应在文档的 [`<body>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/body) 中渲染。

如果使用服务端渲染，你可以省略 `<Scripts/>`，应用将作为传统的无 JavaScript Web 应用运行，仅依赖 HTML 和浏览器行为。

```tsx
import { Scripts } from "react-router";

export default function Root() {
  return (
    <html>
      <head />
      <body>
        <Scripts />
      </body>
    </html>
  );
}
```

## 函数签名

```tsx
function Scripts(
  scriptProps: ScriptsProps,
): React.JSX.Element | null;
```

## Props

### scriptProps

扩展到 [`<script>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script) 标签上的额外 props，如 [`crossOrigin`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLScriptElement/crossOrigin)、[`nonce`](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Global_attributes/nonce) 等。
