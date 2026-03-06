---
title: useNavigationType
---

# useNavigationType

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useNavigationType.html)

返回当前的 [`Navigation`](https://api.reactrouter.com/v7/types/react-router.Navigation.html) action，描述路由器如何到达当前 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html)，可能是通过 [`History`](https://developer.mozilla.org/en-US/docs/Web/API/History) 栈上的 pop、push 或 replace。

## 函数签名

```tsx
function useNavigationType(): NavigationType;
```

## 返回值

当前的 [`NavigationType`](https://api.reactrouter.com/v7/enums/react-router.NavigationType.html)（`"POP"`、`"PUSH"` 或 `"REPLACE"`）。
