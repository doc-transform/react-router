---
title: matchRoutes
---

# matchRoutes

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.matchRoutes.html)

将给定路由与 location 匹配并返回匹配数据。

```tsx
import { matchRoutes } from "react-router";

let routes = [
  {
    path: "/",
    Component: Root,
    children: [
      {
        path: "dashboard",
        Component: Dashboard,
      },
    ],
  },
];

matchRoutes(routes, "/dashboard"); // [rootMatch, dashboardMatch]
```

## 函数签名

```tsx
function matchRoutes<
  RouteObjectType extends
    AgnosticRouteObject = AgnosticRouteObject,
>(
  routes: RouteObjectType[],
  locationArg: Partial<Location> | string,
  basename = "/",
): AgnosticRouteMatch<string, RouteObjectType>[] | null;
```

## 参数

### routes

要匹配的路由对象数组。

### locationArg

要匹配的 location，可以是字符串路径或部分 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html) 对象。

### basename

在匹配前从 location 中去除的可选基础路径。默认为 `/`。

## 返回值

匹配路由的数组，如果没有找到匹配则返回 `null`。
