---
title: useBeforeUnload
---

# useBeforeUnload

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useBeforeUnload.html)

设置一个在 [Window 的 `beforeunload` 事件](https://developer.mozilla.org/en-US/docs/Web/API/Window/beforeunload_event)触发时调用的回调。

## 函数签名

```tsx
function useBeforeUnload(
  callback: (event: BeforeUnloadEvent) => any,
  options?: {
    capture?: boolean;
  },
): void;
```

## 参数

### callback

当 [`beforeunload` 事件](https://developer.mozilla.org/en-US/docs/Web/API/Window/beforeunload_event)触发时调用的回调函数。

### options.capture

如果为 `true`，事件将在捕获阶段被捕获。默认为 `false`。

## 返回值

无返回值。
