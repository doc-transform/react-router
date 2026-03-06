---
title: matchRoutes
---

# `matchRoutes`

<details>
  <summary>Type declaration</summary>

```tsx
declare function matchRoutes(
  routes: RouteObject[],
  location: Partial<Location> | string,
  basename?: string,
): RouteMatch[] | null;

interface RouteMatch<ParamKey extends string = string> {
  params: Params<ParamKey>;
  pathname: string;
  route: RouteObject;
}
```

</details>

`matchRoutes` 对一组路由运行路由匹配算法，以查看哪些路由（如果有）与给定的 [`location`][location] 匹配。如果找到匹配，则返回一个 `RouteMatch` 对象数组，每个匹配的路由对应一个。

这是 React Router 匹配算法的核心。它在 [`useRoutes`][useroutes] 和 [`<Routes>` 组件][routes] 内部使用，用于确定哪些路由与当前位置匹配。在某些你需要手动匹配一组路由的场景中，它也很有用。

[location]: ./location
[useroutes]: ../hooks/use-routes
[routes]: ../components/routes
