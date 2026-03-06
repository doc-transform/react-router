---
title: 路由
order: 2
---

# 路由

[MODES: declarative]

## 配置路由

路由通过渲染 `<Routes>` 和 `<Route>` 来配置，将 URL 路径段与 UI 元素关联起来。

```tsx
import React from "react";
import ReactDOM from "react-dom/client";
import { BrowserRouter, Routes, Route } from "react-router";
import App from "./app";

const root = document.getElementById("root");

ReactDOM.createRoot(root).render(
  <BrowserRouter>
    <Routes>
      <Route path="/" element={<App />} />
    </Routes>
  </BrowserRouter>,
);
```

以下是一个更完整的配置示例：

```tsx
<Routes>
  <Route index element={<Home />} />
  <Route path="about" element={<About />} />

  <Route element={<AuthLayout />}>
    <Route path="login" element={<Login />} />
    <Route path="register" element={<Register />} />
  </Route>

  <Route path="concerts">
    <Route index element={<ConcertsHome />} />
    <Route path=":city" element={<City />} />
    <Route path="trending" element={<Trending />} />
  </Route>
</Routes>
```

## 嵌套路由

路由可以嵌套在父路由中。

```tsx
<Routes>
  <Route path="dashboard" element={<Dashboard />}>
    <Route index element={<Home />} />
    <Route path="settings" element={<Settings />} />
  </Route>
</Routes>
```

父路由的路径会自动包含在子路由中，因此上面的配置同时创建了 `"/dashboard"` 和 `"/dashboard/settings"` 两个 URL。

子路由通过父路由中的 `<Outlet/>` 进行渲染。

```tsx filename=app/dashboard.tsx
import { Outlet } from "react-router";

export default function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      {/* 这里渲染的是 <Home/> 或 <Settings/> */}
      <Outlet />
    </div>
  );
}
```

## 布局路由

_没有_ `path` 的路由会为其子路由创建新的嵌套层级，但不会向 URL 添加任何路径段。

```tsx lines=[2,9]
<Routes>
  <Route element={<MarketingLayout />}>
    <Route index element={<MarketingHome />} />
    <Route path="contact" element={<Contact />} />
  </Route>

  <Route path="projects">
    <Route index element={<ProjectsHome />} />
    <Route element={<ProjectsLayout />}>
      <Route path=":pid" element={<Project />} />
      <Route path=":pid/edit" element={<EditProject />} />
    </Route>
  </Route>
</Routes>
```

## 索引路由

索引路由在父路由的 URL 处渲染到父路由的 `<Outlet/>` 中（类似于默认子路由）。通过 `index` 属性进行配置：

```tsx lines=[4,8]
<Routes>
  <Route path="/" element={<Root />}>
    {/* 在 "/" 处渲染到 <Root> 的 outlet 中 */}
    <Route index element={<Home />} />

    <Route path="dashboard" element={<Dashboard />}>
      {/* 在 "/dashboard" 处渲染到 <Dashboard> 的 outlet 中 */}
      <Route index element={<DashboardHome />} />
      <Route path="settings" element={<Settings />} />
    </Route>
  </Route>
</Routes>
```

注意：索引路由不能有子路由。如果你需要这种行为，可能应该使用[布局路由](#布局路由)。

## 路由前缀

_没有_ `element` 属性的 `<Route path>` 会为其子路由添加路径前缀，而不引入父布局。

```tsx filename=app/routes.ts lines=[1]
<Route path="projects">
  <Route index element={<ProjectsHome />} />
  <Route element={<ProjectsLayout />}>
    <Route path=":pid" element={<Project />} />
    <Route path=":pid/edit" element={<EditProject />} />
  </Route>
</Route>
```

## 动态路径段

如果路径段以 `:` 开头，则它会成为"动态段"。当路由匹配 URL 时，动态段会从 URL 中解析出来，并作为 `params` 提供给其他路由 API（如 `useParams`）。

```tsx
<Route path="teams/:teamId" element={<Team />} />
```

```tsx filename=app/team.tsx
import { useParams } from "react-router";

export default function Team() {
  let params = useParams();
  // params.teamId
}
```

一个路由路径中可以有多个动态段：

```tsx
<Route
  path="/c/:categoryId/p/:productId"
  element={<Product />}
/>
```

```tsx filename=app/category-product.tsx
import { useParams } from "react-router";

export default function CategoryProduct() {
  let { categoryId, productId } = useParams();
  // ...
}
```

你应该确保给定路径中所有动态段的名称是唯一的。否则，由于 `params` 对象是按顺序填充的，后面的动态段值会覆盖前面的值。

## 可选路径段

你可以在路径段末尾添加 `?` 使其成为可选的。

```tsx
<Route path=":lang?/categories" element={<Categories />} />
```

静态路径段也可以是可选的：

```tsx
<Route path="users/:userId/edit?" element={<User />} />
```

## 通配符

也称为"全匹配"和"星号"路径段。如果路由路径模式以 `/*` 结尾，它将匹配 `/` 之后的任何字符，包括其他 `/` 字符。

```tsx
<Route path="files/*" element={<File />} />
```

```tsx
let params = useParams();
// params["*"] 包含 files/ 之后的剩余 URL
let filePath = params["*"];
```

你可以解构 `*`，但需要给它一个新名称。常用的名称是 `splat`：

```tsx
let { "*": splat } = useParams();
```

## 链接

在 UI 中使用 `Link` 和 `NavLink` 链接到各个路由。

```tsx
import { NavLink, Link } from "react-router";

function Header() {
  return (
    <nav>
      {/* NavLink 可以方便地显示激活状态 */}
      <NavLink
        to="/"
        className={({ isActive }) =>
          isActive ? "active" : ""
        }
      >
        Home
      </NavLink>

      <Link to="/concerts/salt-lake-city">Concerts</Link>
    </nav>
  );
}
```

---

下一节：[导航](./navigating)
