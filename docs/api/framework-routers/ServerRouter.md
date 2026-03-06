---
title: ServerRouter
---

# ServerRouter

[MODES: framework]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.ServerRouter.html)

框架模式下 React Router 应用的服务端入口点。此组件用于在服务器响应中生成 HTML。参见 [`entry.server.tsx`](../framework-conventions/entry.server.tsx)。

## 函数签名

```tsx
function ServerRouter({
  context,
  url,
  nonce,
}: ServerRouterProps): ReactElement;
```

## Props

### context

包含 manifest、路由模块和其他渲染所需数据的入口上下文。

### nonce

可选的 `nonce`，用于 [内容安全策略 (CSP)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CSP) 合规，允许内联脚本安全运行。

### url

当前正在处理的请求的 URL。
