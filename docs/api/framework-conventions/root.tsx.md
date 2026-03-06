---
title: root.tsx
order: 1
---

# root.tsx

[MODES: framework]

## 概述

<docs-info>
此文件是必需的
</docs-info>

"根"路由（`app/root.tsx`）是 React Router 应用中唯一*必需的*路由，因为它是所有路由的父级，负责渲染根 `<html>` 文档。

```tsx filename=app/root.tsx
import { Outlet, Scripts } from "react-router";

import "./global-styles.css";

export default function App() {
  return (
    <html lang="en">
      <head>
        <link rel="icon" href="/favicon.ico" />
      </head>
      <body>
        <Outlet />
        <Scripts />
      </body>
    </html>
  );
}
```

## 需要渲染的组件

由于根路由管理你的文档，它是渲染 React Router 提供的一些"文档级"组件的合适位置。这些组件在根路由中只使用一次，它们包含了 React Router 为正确渲染页面所计算或构建的所有内容。

```tsx filename=app/root.tsx
import {
  Outlet,
  Scripts,
  ScrollRestoration,
} from "react-router";

export default function App() {
  return (
    <html lang="en">
      <head>
        <meta charSet="utf-8" />
        <meta
          name="viewport"
          content="width=device-width, initial-scale=1"
        />
      </head>
      <body>
        {/* 子路由在这里渲染 */}
        <Outlet />

        {/* 管理客户端过渡的滚动位置 */}
        {/* 如果你使用基于 nonce 的内容安全策略来控制脚本，必须提供 `nonce` prop。否则，如此处所示省略 nonce prop。 */}
        <ScrollRestoration />

        {/* Script 标签放在这里 */}
        {/* 如果你使用基于 nonce 的内容安全策略来控制脚本，必须提供 `nonce` prop。否则，如此处所示省略 nonce prop。 */}
        <Scripts />
      </body>
    </html>
  );
}
```

如果你没有使用 React 19，或者选择不使用 React 的 [`<link>`][react-link]、[`<title>`][react-title] 和 [`<meta>`][react-meta] 组件，而是依赖 React Router 的 [`links`][react-router-links] 和 [`meta`][react-router-meta] 导出，则需要在根路由中添加以下内容：

```tsx filename=app/root.tsx
import { Links, Meta } from "react-router";

export default function App() {
  return (
    <html lang="en">
      <head>
        {/* 所有路由的 `meta` 导出将在这里渲染 */}
        <Meta />

        {/* 所有路由的 `link` 导出将在这里渲染 */}
        <Links />
      </head>
      <body>
        <Outlet />
        <ScrollRestoration />
        <Scripts />
      </body>
    </html>
  );
}
```

## Layout 导出

根路由支持所有[路由模块导出][route-module]。

根路由还支持一个额外的可选 `Layout` 导出。`Layout` 组件有两个用途：

1. 避免在根组件、`HydrateFallback` 和 `ErrorBoundary` 之间重复"应用外壳"
2. 防止 React 在根组件/`HydrateFallback`/`ErrorBoundary` 之间切换时重新挂载应用外壳元素，否则如果 React 移除并重新添加 `<Links>` 组件中的 `<link rel="stylesheet">` 标签，可能导致 FOUC（无样式内容闪烁）

`Layout` 接收单个 `children` prop，即 `default` 导出（如 `App`）、`HydrateFallback` 或 `ErrorBoundary`。

```tsx filename=app/root.tsx
export function Layout({ children }) {
  return (
    <html lang="en">
      <head>
        <meta charSet="utf-8" />
        <meta
          name="viewport"
          content="width=device-width, initial-scale=1"
        />
        <Meta />
        <Links />
      </head>
      <body>
        {/* children 将是根 Component、ErrorBoundary 或 HydrateFallback */}
        {children}
        <Scripts />
        <ScrollRestoration />
      </body>
    </html>
  );
}

export default function App() {
  return <Outlet />;
}

export function ErrorBoundary() {}
```

**关于在 `Layout` 组件中使用 `useLoaderData` 的说明**

`useLoaderData` 不允许在 `ErrorBoundary` 组件中使用，因为它是为正常路由渲染设计的，其类型内置了 `loader` 成功运行并返回数据的假设。在 `ErrorBoundary` 中这个假设不成立，因为可能正是 `loader` 抛出错误触发了边界！要在 `ErrorBoundary` 中访问 loader 数据，可以使用 `useRouteLoaderData`，它考虑了 loader 数据可能为 `undefined` 的情况。

由于你的 `Layout` 组件同时用于成功和错误流程，同样的限制也适用。如果你需要在 `Layout` 中根据请求是否成功来分叉逻辑，可以使用 `useRouteLoaderData("root")` 和 `useRouteError()`。

<docs-warn>由于你的 `<Layout>` 组件用于渲染 `ErrorBoundary`，你应该*非常谨慎*以确保能够渲染 `ErrorBoundary` 而不遇到任何渲染错误。如果你的 `Layout` 在尝试渲染边界时抛出另一个错误，则无法使用它，你的 UI 将回退到非常简陋的内置默认 `ErrorBoundary`。</docs-warn>

```tsx filename=app/root.tsx lines=[6-7,19-29,32-34]
export function Layout({
  children,
}: {
  children: React.ReactNode;
}) {
  const data = useRouteLoaderData("root");
  const error = useRouteError();

  return (
    <html lang="en">
      <head>
        <meta charSet="utf-8" />
        <meta
          name="viewport"
          content="width=device-width, initial-scale=1"
        />
        <Meta />
        <Links />
        <style
          dangerouslySetInnerHTML={{
            __html: `
              :root {
                --themeVar: ${
                  data?.themeVar || defaultThemeVar
                }
              }
            `,
          }}
        />
      </head>
      <body>
        {data ? (
          <Analytics token={data.analyticsToken} />
        ) : null}
        {children}
        <ScrollRestoration />
        <Scripts />
      </body>
    </html>
  );
}
```

[route-module]: ../../start/framework/route-module
[react-link]: https://react.dev/reference/react-dom/components/link
[react-meta]: https://react.dev/reference/react-dom/components/meta
[react-title]: https://react.dev/reference/react-dom/components/title
[react-router-links]: ../../start/framework/route-module#links
[react-router-meta]: ../../start/framework/route-module#meta
