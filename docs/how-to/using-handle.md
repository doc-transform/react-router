---
title: 使用 handle
---

# 使用 `handle`

[MODES: framework]

<br/>
<br/>

你可以使用 [`useMatches`][use-matches] hook 和 [`handle`][handle] 路由导出，基于路由层级构建动态 UI 元素（如面包屑导航）。

## 理解基础

React Router 在组件树中提供了对所有路由匹配及其数据的访问。这允许路由通过 `handle` 导出贡献元数据，由祖先组件进行渲染。

`useMatches` hook 与 `handle` 导出结合使用，使路由能够参与其实际渲染位置之上的组件树渲染过程。虽然我们以面包屑为例，但此模式适用于任何需要路由向其祖先提供额外信息的场景。

## 定义路由 `handle`

我们将使用如下路由结构：

```ts filename=app/routes.ts
import { route } from "@react-router/dev/routes";

export default [
  route("parent", "./routes/parent.tsx", [
    route("child", "./routes/child.tsx"),
  ]),
] satisfies RouteConfig;
```

向"parent"路由的 `handle` 导出添加 `breadcrumb` 属性。你可以根据你的用例为此属性命名。

```tsx filename=app/routes/parent.tsx
import { Link } from "react-router";

export const handle = {
  breadcrumb: () => <Link to="/parent">某个路由</Link>,
};
```

你也可以为子路由定义面包屑：

```tsx filename=app/routes/child.tsx
import { Link } from "react-router";

export const handle = {
  breadcrumb: () => <Link to="/parent/child">子路由</Link>,
};
```

## 使用路由 `handle`

在根布局或任何祖先组件中使用 `useMatches` hook 来收集并渲染 `handle` 导出中定义的组件：

```tsx filename=app/root.tsx lines=[7,11,22-31]
import {
  Links,
  Meta,
  Outlet,
  Scripts,
  ScrollRestoration,
  useMatches,
} from "react-router";

export function Layout({ children }) {
  const matches = useMatches();

  return (
    <html lang="en">
      <head>
        <Meta />
        <Links />
      </head>
      <body>
        <header>
          <ol>
            {matches
              .filter(
                (match) =>
                  match.handle && match.handle.breadcrumb,
              )
              .map((match, index) => (
                <li key={index}>
                  {match.handle.breadcrumb(match)}
                </li>
              ))}
          </ol>
        </header>
        {children}
        <ScrollRestoration />
        <Scripts />
      </body>
    </html>
  );
}

export default function App() {
  return <Outlet />;
}
```

`match` 对象被传递给每个面包屑函数，让你可以访问 `match.data`（来自 loader）和其他路由信息，以便根据路由数据创建动态面包屑。

此模式提供了一种简洁的方式，让路由贡献元数据，供祖先组件消费和渲染。

## 其他资源

- [`useMatches`][use-matches]
- [`handle`][handle]

[use-matches]: ../api/hooks/useMatches
[handle]: ../start/framework/route-module#handle
