---
title: renderMatches
---

# renderMatches

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.renderMatches.html)

将 [`matchRoutes`](../utils/matchRoutes) 的结果渲染为 React 元素。

## 函数签名

```tsx
function renderMatches(
  matches: RouteMatch[] | null,
): React.ReactElement | null;
```

## 参数

### matches

要渲染的[路由匹配](https://api.reactrouter.com/v7/interfaces/react-router.RouteMatch.html)数组

## 返回值

渲染匹配路由的 React 元素，如果没有匹配则返回 `null`
