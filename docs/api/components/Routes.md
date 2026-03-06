---
title: Routes
---

# Routes

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.Routes.html)

渲染与当前位置最佳匹配的 [`<Route>`](../components/Route) 分支。注意：这些路由不参与[数据加载](../../start/framework/route-module#loader)、[`action`](../../start/framework/route-module#action)、代码拆分或任何其他[路由模块](../../start/framework/route-module)功能。

```tsx
import { Route, Routes } from "react-router";

<Routes>
  <Route index element={<StepOne />} />
  <Route path="step-2" element={<StepTwo />} />
  <Route path="step-3" element={<StepThree />} />
</Routes>;
```

## 函数签名

```tsx
function Routes({
  children,
  location,
}: RoutesProps): React.ReactElement | null;
```

## Props

### children

嵌套的 [`Route`](../components/Route) 元素。

### location

要匹配的 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html)。默认为当前位置。
