---
title: 路由
order: 2
---

# 路由

[MODES: data]

## 配置路由

路由作为 `createBrowserRouter` 的第一个参数进行配置。至少需要一个路径和组件：

```tsx
import { createBrowserRouter } from "react-router";

function Root() {
  return <h1>Hello world</h1>;
}

const router = createBrowserRouter([
  { path: "/", Component: Root },
]);
```

以下是一个更完整的路由配置示例：

```ts filename=app/routes.ts
createBrowserRouter([
  {
    path: "/",
    Component: Root,
    children: [
      { index: true, Component: Home },
      { path: "about", Component: About },
      {
        path: "auth",
        Component: AuthLayout,
        children: [
          { path: "login", Component: Login },
          { path: "register", Component: Register },
        ],
      },
      {
        path: "concerts",
        children: [
          { index: true, Component: ConcertsHome },
          { path: ":city", Component: ConcertsCity },
          { path: "trending", Component: ConcertsTrending },
        ],
      },
    ],
  },
]);
```

## 路由对象

路由对象不仅定义了路径和组件，还定义了路由的其他行为，如数据加载和操作。我们将在[路由对象指南](./route-object)中详细介绍，这里先看一个 loader 的简单示例。

```tsx filename=app/team.tsx
import {
  createBrowserRouter,
  useLoaderData,
} from "react-router";

createBrowserRouter([
  {
    path: "/teams/:teamId",
    loader: async ({ params }) => {
      let team = await fetchTeam(params.teamId);
      return { name: team.name };
    },
    Component: Team,
  },
]);

function Team() {
  let data = useLoaderData();
  return <h1>{data.name}</h1>;
}
```

## 嵌套路由

路由可以通过 `children` 嵌套在父路由中。

```ts filename=app/routes.ts
createBrowserRouter([
  {
    path: "/dashboard",
    Component: Dashboard,
    children: [
      { index: true, Component: Home },
      { path: "settings", Component: Settings },
    ],
  },
]);
```

父路由的路径会自动包含在子路由中，因此上面的配置同时创建了 `"/dashboard"` 和 `"/dashboard/settings"` 两个 URL。

子路由通过父路由中的 `<Outlet/>` 进行渲染。

```tsx filename=app/dashboard.tsx
import { Outlet } from "react-router";

export default function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      {/* 这里渲染的是 <Home> 或 <Settings> */}
      <Outlet />
    </div>
  );
}
```

## 布局路由

省略路由的 `path` 会为其子路由创建新的[嵌套路由](#嵌套路由)层级，而不向 URL 添加任何路径段。

```tsx lines=[3,16]
createBrowserRouter([
  {
    // 这个父路由没有 path，只有组件
    Component: MarketingLayout,
    children: [
      { index: true, Component: Home },
      { path: "contact", Component: Contact },
    ],
  },

  {
    path: "projects",
    children: [
      { index: true, Component: ProjectsHome },
      {
        // 同样没有 path，只有布局组件
        Component: ProjectLayout,
        children: [
          { path: ":pid", Component: Project },
          { path: ":pid/edit", Component: EditProject },
        ],
      },
    ],
  },
]);
```

注意：

- `Home` 和 `Contact` 会渲染到 `MarketingLayout` 的 outlet 中
- `Project` 和 `EditProject` 会渲染到 `ProjectLayout` 的 outlet 中，而 `ProjectsHome` 不会。

## 索引路由

索引路由通过在没有 path 的路由对象上设置 `index: true` 来定义。

```ts
{ index: true, Component: Home }
```

索引路由在父路由的 URL 处渲染到父路由的 [Outlet][outlet] 中（类似于默认子路由）。

```ts lines=[4,5,10,11]
import { createBrowserRouter } from "react-router";

createBrowserRouter([
  // 在 "/" 处渲染
  { index: true, Component: Home },
  {
    Component: Dashboard,
    path: "/dashboard",
    children: [
      // 在 "/dashboard" 处渲染
      { index: true, Component: DashboardHome },
      { path: "settings", Component: DashboardSettings },
    ],
  },
]);
```

注意：索引路由不能有子路由。

## 前缀路由

只有 path 而没有组件的路由会创建一组带有路径前缀的路由。

```tsx lines=[3]
createBrowserRouter([
  {
    // 没有组件，只有路径
    path: "/projects",
    children: [
      { index: true, Component: ProjectsHome },
      { path: ":pid", Component: Project },
      { path: ":pid/edit", Component: EditProject },
    ],
  },
]);
```

这样会创建 `/projects`、`/projects/:pid` 和 `/projects/:pid/edit` 路由，而不引入布局组件。

## 动态路径段

如果路径段以 `:` 开头，则它会成为"动态段"。当路由匹配 URL 时，动态段会从 URL 中解析出来，并作为 `params` 提供给其他路由 API。

```ts lines=[2]
{
  path: "teams/:teamId",
  loader: async ({ params }) => {
    // params 在 loader/action 中可用
    let team = await fetchTeam(params.teamId);
    return { name: team.name };
  },
  Component: Team,
}
```

```tsx
import { useParams } from "react-router";

function Team() {
  // params 在组件中通过 useParams 获取
  let params = useParams();
  // ...
}
```

一个路由路径中可以有多个动态段：

```ts
{
  path: "c/:categoryId/p/:productId";
}
```

## 可选路径段

你可以在路径段末尾添加 `?` 使其成为可选的。

```ts
{
  path: ":lang?/categories";
}
```

静态路径段也可以是可选的：

```ts
{
  path: "users/:userId/edit?";
}
```

## 通配符

也称为"全匹配"和"星号"路径段。如果路由路径模式以 `/*` 结尾，它将匹配 `/` 之后的任何字符，包括其他 `/` 字符。

```ts
{
  path: "files/*";
  loader: async ({ params }) => {
    params["*"]; // 包含 files/ 之后的剩余 URL
  };
}
```

你可以解构 `*`，但需要给它一个新名称。常用的名称是 `splat`：

```tsx
const { "*": splat } = params;
```

---

下一节：[路由对象](./route-object)

[outlet]: https://api.reactrouter.com/v7/functions/react-router.Outlet.html
