---
title: useFormAction
---

# useFormAction

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useFormAction.html)

解析 URL 到组件层级中最近的路由，而不是应用的当前 URL。

[`Form`](../components/Form) 内部使用此 Hook 来解析 `action` 到最近的路由，但也可以通用使用。

```tsx
import { useFormAction } from "react-router";

function SomeComponent() {
  // 最近的路由 URL
  let action = useFormAction();

  // 最近的路由 URL + "destroy"
  let destroyAction = useFormAction("destroy");
}
```

## 函数签名

```tsx
function useFormAction(
  action?: string,
  {
    relative,
  }: {
    relative?: RelativeRoutingType;
  } = ,
): string {}
```

## 参数

### action

要追加到最近路由 URL 的 action。默认为最近的路由 URL。

### options.relative

解析 action 时使用的相对路由类型。默认为 `"route"`。

## 返回值

解析后的 action URL。
