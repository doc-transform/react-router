---
title: 当前 Future Flags
order: 1
new: true
---

# Future Flags

以下 future flags 已稳定并可以采用。要了解更多关于 future flags 的信息，请参阅 [API 开发策略](../guides/api-development-strategy)

## 更新到最新的 v6.x

首先更新到 v6.x 的最新次要版本以获取最新的 future flags。

👉 **更新到最新的 v6**

```shellscript nonumber
npm install react-router-dom@6
```

## v7_relativeSplatPath

**背景**

更改了像 `dashboard/*`（相对于仅 `*`）这样的多段 splat 路径的相对路径匹配和链接行为。[查看 CHANGELOG](https://github.com/remix-run/react-router/blob/main/packages/react-router-dom/CHANGELOG.md#minor-changes-2) 了解更多信息。

👉 **启用该 flag**

启用 flag 取决于路由器类型：

```tsx
<BrowserRouter
  future={{
    v7_relativeSplatPath: true,
  }}
/>
```

```tsx
createBrowserRouter(routes, {
  future: {
    v7_relativeSplatPath: true,
  },
});
```

**更新你的代码**

如果你有任何包含 path + splat 的路由，像 `<Route path="dashboard/*">`，并且在其下方有相对链接像 `<Link to="relative">` 或 `<Link to="../relative">`，你需要更新你的代码。

👉 **将 `<Route>` 拆分为两个**

将任何多段 splat `<Route>` 拆分为一个带 path 的父路由和一个带 splat 的子路由：

```diff
<Routes>
  <Route path="/" element={<Home />} />
-  <Route path="dashboard/*" element={<Dashboard />} />
+  <Route path="dashboard">
+    <Route index element={<Dashboard />} />
+    <Route path="*" element={<Dashboard />} />
+  </Route>
</Routes>

// or
createBrowserRouter([
  { path: "/", element: <Home /> },
  {
-    path: "dashboard/*",
-    element: <Dashboard />,
+    path: "dashboard",
+    children: [{ path: "*", element: <Dashboard /> }],
  },
]);
```

👉 **更新相对链接**

更新该路由树内的任何 `<Link>` 元素，添加额外的 `..` 相对路径段，以继续链接到相同的位置：

```diff
function Dashboard() {
  return (
    <div>
      <h2>Dashboard</h2>
      <nav>
-        <Link to="/">Dashboard Home</Link>
-        <Link to="team">Team</Link>
-        <Link to="projects">Projects</Link>
+        <Link to="../">Dashboard Home</Link>
+        <Link to="../team">Team</Link>
+        <Link to="../projects">Projects</Link>
      </nav>

      <Routes>
        <Route path="/" element={<DashboardHome />} />
        <Route path="team" element={<DashboardTeam />} />
        <Route
          path="projects"
          element={<DashboardProjects />}
        />
      </Routes>
    </div>
  );
}
```

## v7_startTransition

**背景**

使用 `React.useTransition` 而不是 `React.useState` 来处理 Router 状态更新。查看 [CHANGELOG](https://github.com/remix-run/react-router/blob/main/CHANGELOG.md#v7_starttransition) 了解更多信息。

👉 **启用该 flag**

```tsx
<BrowserRouter
  future={{
    v7_startTransition: true,
  }}
/>

// or
<RouterProvider
  future={{
    v7_startTransition: true,
  }}
/>
```

👉 **更新你的代码**

你不需要更新任何内容，除非你在组件*内部*使用了 `React.lazy`。

在组件内部使用 `React.lazy` 与 `React.useTransition`（或其他在组件内部创建 promise 的代码）不兼容。将 `React.lazy` 移到模块作用域，不要在组件内部创建 promise。这不是 React Router 的限制，而是 React 的不正确用法。

## v7_fetcherPersist

<docs-warning>如果你没有使用 `createBrowserRouter`，可以跳过此部分</docs-warning>

**背景**

fetcher 的生命周期现在基于它返回空闲状态的时间，而不是其拥有者组件卸载的时间：[查看 CHANGELOG](https://github.com/remix-run/react-router/blob/main/CHANGELOG.md#persistence-future-flag-futurev7_fetcherpersist) 了解更多信息。

**启用该 Flag**

```tsx
createBrowserRouter(routes, {
  future: {
    v7_fetcherPersist: true,
  },
});
```

**更新你的代码**

不太可能影响你的应用。你可能需要检查 `useFetchers` 的所有用法，因为它们可能会比之前持续更长时间。根据你的具体用法，你可能会渲染某些内容比之前更长时间。

## v7_normalizeFormMethod

<docs-warning>如果你没有使用 `createBrowserRouter`，可以跳过此部分</docs-warning>

此 flag 将 `formMethod` 字段规范化为大写 HTTP 方法，以与 `fetch()` 行为保持一致。[查看 CHANGELOG](https://github.com/remix-run/react-router/blob/main/CHANGELOG.md#futurev7_normalizeformmethod) 了解更多信息。

👉 **启用该 Flag**

```tsx
createBrowserRouter(routes, {
  future: {
    v7_normalizeFormMethod: true,
  },
});
```

**更新你的代码**

如果你的代码中有检查小写 HTTP 方法的地方，你需要更新为检查大写 HTTP 方法（或对其调用 `toLowerCase()`）。

👉 **将 `formMethod` 与大写进行比较**

```diff
-useNavigation().formMethod === "post"
-useFetcher().formMethod === "get";
+useNavigation().formMethod === "POST"
+useFetcher().formMethod === "GET";
```

## v7_partialHydration

<docs-warning>如果你没有使用 `createBrowserRouter`，可以跳过此部分</docs-warning>

此 flag 允许 SSR 框架仅提供部分注水数据。你很可能不需要担心这个，只需打开该 flag 即可。[查看 CHANGELOG](https://github.com/remix-run/react-router/blob/main/CHANGELOG.md#partial-hydration) 了解更多信息。

👉 **启用该 Flag**

```tsx
createBrowserRouter(routes, {
  future: {
    v7_partialHydration: true,
  },
});
```

**更新你的代码**

使用部分注水时，你需要提供一个 `HydrateFallback` 组件在初始注水期间渲染。此外，如果你之前使用了 `fallbackElement`，需要移除它，因为它已被弃用。在大多数情况下，你会希望复用 `fallbackElement` 作为 `HydrateFallback`。

👉 **用 `HydrateFallback` 替换 `fallbackElement`**

```diff
const router = createBrowserRouter(
  [
    {
      path: "/",
      Component: Layout,
+      HydrateFallback: Fallback,
      // or
+      hydrateFallbackElement: <Fallback />,
      children: [],
    },
  ],
);


<RouterProvider
  router={router}
-  fallbackElement={<Fallback />}
/>
```

## v7_skipActionErrorRevalidation

<docs-warning>如果你没有使用 `createBrowserRouter`，可以跳过此部分</docs-warning>

当启用此 flag 时，loader 将不再在 action 抛出/返回 `4xx`/`5xx` 状态码的 `Response` 后默认重新验证。你可以通过 `shouldRevalidate` 和 `actionStatus` 参数在这些场景中选择性启用重新验证。

👉 **启用该 Flag**

```tsx
createBrowserRouter(routes, {
  future: {
    v7_skipActionErrorRevalidation: true,
  },
});
```

**更新你的代码**

在大多数情况下，你可能不需要修改应用代码。通常，如果 action 出错，数据不太可能已被变更并需要重新验证。如果你的代码确实在 action 错误场景中变更了数据，你有两个选择：

👉 **选择 1：修改 `action` 以避免在错误场景中进行变更**

```js
// Before
async function action() {
  await mutateSomeData();
  if (detectError()) {
    throw new Response(error, { status: 400 });
  }
  await mutateOtherData();
  // ...
}

// After
async function action() {
  if (detectError()) {
    throw new Response(error, { status: 400 });
  }
  // All data is now mutated after validations
  await mutateSomeData();
  await mutateOtherData();
  // ...
}
```

👉 **选择 2：通过 `shouldRevalidate` 和 `actionStatus` 选择性启用重新验证**

```js
async function action() {
  await mutateSomeData();
  if (detectError()) {
    throw new Response(error, { status: 400 });
  }
  await mutateOtherData();
}

async function loader() { ... }

function shouldRevalidate({ actionStatus, defaultShouldRevalidate }) {
  if (actionStatus != null && actionStatus >= 400) {
    // Revalidate this loader when actions return a 4xx/5xx status
    return true;
  }
  return defaultShouldRevalidate;
}
```
