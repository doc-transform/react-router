---
title: useOutlet
---

# useOutlet

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useOutlet.html)

返回此路由层级中子路由的元素。[`<Outlet>`](../components/Outlet) 内部使用此 Hook 来渲染子路由。

## 函数签名

```tsx
function useOutlet(
  context?: unknown,
): React.ReactElement | null;
```

## 参数

### context

传递给 outlet 的上下文。

## 返回值

子路由元素，如果没有匹配的子路由则返回 `null`。
