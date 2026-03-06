---
title: Routes
---

# `<Routes>`

在应用中的任何位置渲染，`<Routes>` 会根据当前 [location][location] 匹配一组子路由。

```tsx
interface RoutesProps {
  children?: React.ReactNode;
  location?: Partial<Location> | string;
}

<Routes location>
  <Route />
</Routes>;
```

<docs-info>如果你使用的是像 [`createBrowserRouter`][createbrowserrouter] 这样的数据路由器，通常不会使用此组件，因为作为后代 `<Routes>` 树一部分定义的路由无法使用 [`RouterProvider`][router-provider] 应用中可用的[数据 API][data-apis]。你**可以且应该**在[迁移到 RouterProvider][migrating-to-router-provider] 期间在 `RouterProvider` 应用中使用此组件。</docs-info>

每当 location 发生变化时，`<Routes>` 会遍历其所有子路由以找到最佳匹配，并渲染该 UI 分支。`<Route>` 元素可以嵌套以表示嵌套 UI，这也对应于嵌套的 URL 路径。父路由通过渲染 [`<Outlet>`][outlet] 来渲染其子路由。

```tsx
<Routes>
  <Route path="/" element={<Dashboard />}>
    <Route
      path="messages"
      element={<DashboardMessages />}
    />
    <Route path="tasks" element={<DashboardTasks />} />
  </Route>
  <Route path="about" element={<AboutPage />} />
</Routes>
```

[location]: ../utils/location
[outlet]: ./outlet
[use-route]: ../hooks/use-routes
[createbrowserrouter]: ../routers/create-browser-router
[data-apis]: ../routers/picking-a-router#data-apis
[router-provider]: ../routers/router-provider
[migrating-to-router-provider]: ../upgrading/v6-data
