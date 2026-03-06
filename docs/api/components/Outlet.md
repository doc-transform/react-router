---
title: Outlet
---

# Outlet

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.Outlet.html)

渲染父路由的匹配子路由，如果没有匹配的子路由则不渲染任何内容。

```tsx
import { Outlet } from "react-router";

export default function SomeParent() {
  return (
    <div>
      <h1>Parent Content</h1>
      <Outlet />
    </div>
  );
}
```

## 函数签名

```tsx
function Outlet(
  props: OutletProps,
): React.ReactElement | null;
```

## Props

### context

为 outlet 下方的元素树提供上下文值。当父路由需要向子路由传递值时使用。

```tsx
<Outlet context={myContextValue} />
```

通过 [`useOutletContext`](../hooks/useOutletContext) 访问上下文。
