---
title: useMatches
---

# useMatches

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useMatches.html)

返回当前激活的路由匹配项，用于访问父/子路由的 `loaderData` 或路由的 [`handle`](../../start/framework/route-module#handle) 属性。

## 函数签名

```tsx
function useMatches(): UIMatch[];
```

## 返回值

当前路由层级的 [UI 匹配项](https://api.reactrouter.com/v7/interfaces/react-router.UIMatch.html) 数组。
