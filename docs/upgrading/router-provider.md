---
title: 从 RouterProvider 迁移到框架模式
order: 5
---

# 从 RouterProvider 迁移到框架模式

如果你没有使用 `<RouterProvider>`，请参阅[从组件路由迁移到框架模式][upgrade-component-routes]。

React Router Vite 插件为 React Router 增加了框架功能。本指南将帮助你在应用中采用该插件。如果你遇到任何问题，请通过 [Twitter](https://x.com/remix_run) 或 [Discord](https://rmx.as/discord) 寻求帮助。

## 功能

Vite 插件增加了以下功能：

- 路由 loader、action 和自动数据重新验证
- 类型安全的路由模块
- 自动路由代码拆分
- 跨导航的自动滚动恢复
- 可选的静态预渲染
- 可选的服务端渲染

初始设置需要的工作量最大。不过一旦完成，你可以逐步采用新功能。

## 前提条件

使用 Vite 插件要求你的项目满足：

- Node.js 20+（如果使用 Node 作为运行时）
- Vite 5+

## 1. 将路由定义移入路由模块

React Router Vite 插件会渲染自己的 `RouterProvider`，因此你不能在其中渲染现有的 `RouterProvider`。你需要将所有路由定义格式化为符合[路由模块 API][route-modules] 的格式。

这一步花费的时间最长，但不管是否采用 React Router Vite 插件，这样做都有几个好处：

- 路由模块会被懒加载，减小应用的初始包体积
- 路由定义将统一规范，简化应用架构
- 迁移到路由模块是渐进式的，你可以每次迁移一个路由

**👉 将路由定义移入路由模块**

将路由定义的每个部分作为单独的命名导出，遵循[路由模块 API][route-modules]。

```tsx filename=src/routes/about.tsx
export async function clientLoader() {
  return {
    title: "About",
  };
}

export default function About() {
  let data = useLoaderData();
  return <div>{data.title}</div>;
}

// clientAction、ErrorBoundary 等
```

**👉 创建转换函数**

创建一个辅助函数，将路由模块定义转换为数据路由所期望的格式：

```tsx filename=src/main.tsx
function convert(m: any) {
  let {
    clientLoader,
    clientAction,
    default: Component,
    ...rest
  } = m;
  return {
    ...rest,
    loader: clientLoader,
    action: clientAction,
    Component,
  };
}
```

**👉 懒加载并转换路由模块**

不再直接导入路由模块，而是懒加载并转换它们为数据路由所期望的格式。

这样不仅路由定义符合了路由模块 API，还获得了路由代码拆分的好处。

```diff filename=src/main.tsx
let router = createBrowserRouter([
  // ... 其他路由
  {
    path: "about",
-   loader: aboutLoader,
-   Component: About,
+   lazy: () => import("./routes/about").then(convert),
  },
  // ... 其他路由
]);
```

对应用中的每个路由重复此过程。

## 2. 安装 Vite 插件

当所有路由定义都转换为路由模块后，你就可以采用 React Router Vite 插件了。

**👉 安装 React Router Vite 插件**

```shellscript nonumber
npm install -D @react-router/dev
```

**👉 安装运行时适配器**

这里假设你使用 Node 作为运行时。

```shellscript nonumber
npm install @react-router/node
```

**👉 将 React 插件替换为 React Router**

```diff filename=vite.config.ts
-import react from '@vitejs/plugin-react'
+import { reactRouter } from "@react-router/dev/vite";
import { defineConfig } from "vite";


export default defineConfig({
  plugins: [
-    react()
+    reactRouter()
  ],
});
```

## 3. 添加 React Router 配置

**👉 创建 `react-router.config.ts` 文件**

在项目根目录添加以下文件。在这个配置中，你可以告诉 React Router 关于你项目的信息，比如应用目录的位置以及暂时不使用 SSR（服务端渲染）。

```shellscript nonumber
touch react-router.config.ts
```

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

export default {
  appDirectory: "src",
  ssr: false,
} satisfies Config;
```

## 4. 添加根入口点

在典型的 Vite 应用中，`index.html` 文件是打包的入口点。React Router Vite 插件将入口点移到 `root.tsx` 文件，这样你就可以使用 React 来渲染应用的外壳，而不是静态 HTML，并且以后可以升级到服务端渲染。

**👉 将现有的 `index.html` 移至 `root.tsx`**

例如，如果你当前的 `index.html` 如下所示：

```html filename=index.html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1.0"
    />
    <title>My App</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

你需要将这些标记移到 `src/root.tsx` 中，并删除 `index.html`：

```shellscript nonumber
touch src/root.tsx
```

```tsx filename=src/root.tsx
import {
  Links,
  Meta,
  Outlet,
  Scripts,
  ScrollRestoration,
} from "react-router";

export function Layout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <head>
        <meta charSet="UTF-8" />
        <meta
          name="viewport"
          content="width=device-width, initial-scale=1.0"
        />
        <title>My App</title>
        <Meta />
        <Links />
      </head>
      <body>
        {children}
        <ScrollRestoration />
        <Scripts />
      </body>
    </html>
  );
}

export default function Root() {
  return <Outlet />;
}
```

**👉 将 `RouterProvider` 上层的内容移到 `root.tsx`**

所有全局样式、context provider 等应移到 `root.tsx` 中，以便在所有路由间共享。

例如，如果你的 `App.tsx` 如下所示：

```tsx filename=src/App.tsx
import "./index.css";

export default function App() {
  return (
    <OtherProviders>
      <AppLayout>
        <RouterProvider router={router} />
      </AppLayout>
    </OtherProviders>
  );
}
```

你需要将 `RouterProvider` 上面的所有内容移到 `root.tsx` 中。

```diff filename=src/root.tsx
+import "./index.css";

// ... 其他导入和 Layout

export default function Root() {
  return (
+   <OtherProviders>
+     <AppLayout>
        <Outlet />
+     </AppLayout>
+   </OtherProviders>
  );
}
```

## 5. 添加客户端入口模块（可选）

在典型的 Vite 应用中，`index.html` 文件指向 `src/main.tsx` 作为客户端入口点。React Router 使用名为 `src/entry.client.tsx` 的文件。

如果不存在 `entry.client.tsx`，React Router Vite 插件会使用一个默认的隐藏入口。

**👉 将 `src/entry.client.tsx` 设为入口点**

如果你当前的 `src/main.tsx` 如下所示：

```tsx filename=src/main.tsx
import React from "react";
import ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router";
import App from "./App";

const router = createBrowserRouter([
  // ... 路由定义
]);

ReactDOM.createRoot(
  document.getElementById("root")!,
).render(
  <React.StrictMode>
    <RouterProvider router={router} />;
  </React.StrictMode>,
);
```

你需要将其重命名为 `entry.client.tsx` 并修改为：

```tsx filename=src/entry.client.tsx
import React from "react";
import ReactDOM from "react-dom/client";
import { HydratedRouter } from "react-router/dom";

ReactDOM.hydrateRoot(
  document,
  <React.StrictMode>
    <HydratedRouter />
  </React.StrictMode>,
);
```

- 使用 `hydrateRoot` 代替 `createRoot`
- 渲染 `<HydratedRouter>` 代替 `<App/>` 组件
- 注意：我们不再创建路由并手动传递给 `<RouterProvider />`。我们将在下一步中迁移路由定义。

## 6. 迁移路由

React Router Vite 插件使用 `routes.ts` 文件来配置路由。格式与数据路由的定义非常相似。

**👉 将路由定义移到 `routes.ts` 文件**

```shellscript nonumber
touch src/routes.ts src/catchall.tsx
```

将路由定义移到 `routes.ts` 中。注意格式不完全匹配，你会遇到类型错误；我们接下来会修复。

```diff filename=src/routes.ts
+import type { RouteConfig } from "@react-router/dev/routes";

-const router = createBrowserRouter([
+export default [
  {
    path: "/",
    lazy: () => import("./routes/layout").then(convert),
    children: [
      {
        index: true,
        lazy: () => import("./routes/home").then(convert),
      },
      {
        path: "about",
        lazy: () => import("./routes/about").then(convert),
      },
      {
        path: "todos",
        lazy: () => import("./routes/todos").then(convert),
        children: [
          {
            path: ":id",
            lazy: () =>
              import("./routes/todo").then(convert),
          },
        ],
      },
    ],
  },
-]);
+] satisfies RouteConfig;
```

**👉 将 `lazy` 加载替换为 `file` 加载**

```diff filename=src/routes.ts
export default [
  {
    path: "/",
-   lazy: () => import("./routes/layout").then(convert),
+   file: "./routes/layout.tsx",
    children: [
      {
        index: true,
-       lazy: () => import("./routes/home").then(convert),
+       file: "./routes/home.tsx",
      },
      {
        path: "about",
-       lazy: () => import("./routes/about").then(convert),
+       file: "./routes/about.tsx",
      },
      {
        path: "todos",
-       lazy: () => import("./routes/todos").then(convert),
+       file: "./routes/todos.tsx",
        children: [
          {
            path: ":id",
-           lazy: () => import("./routes/todo").then(convert),
+           file: "./routes/todo.tsx",
          },
        ],
      },
    ],
  },
] satisfies RouteConfig;
```

查看我们的[路由配置指南][configuring-routes]了解更多关于 `routes.ts` 文件以及辅助函数的信息，以进一步简化路由定义。

## 7. 启动应用

此时你应该已经完全迁移到 React Router Vite 插件了。请更新 `dev` 脚本并运行应用，确保一切正常。

**👉 添加 `dev` 脚本并运行应用**

```json filename=package.json
"scripts": {
  "dev": "react-router dev"
}
```

在继续下一步之前，确保应用能正常启动：

```shellscript
npm run dev
```

你可能还想将 `.react-router/` 添加到 `.gitignore` 文件中，以避免在仓库中跟踪不必要的文件。

```txt
.react-router/
```

你可以查看[类型安全][type-safety]了解如何完整设置和使用自动生成的类型安全，包括参数、loader 数据等。

## 启用 SSR 和/或预渲染

如果你想启用服务端渲染和静态预渲染，可以在打包器插件中使用 `ssr` 和 `prerender` 选项。对于 SSR，你还需要将服务端构建产物部署到服务器上。

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

export default {
  ssr: true,
  async prerender() {
    return ["/", "/about", "/contact"];
  },
} satisfies Config;
```

[upgrade-component-routes]: ./component-routes
[configuring-routes]: ../start/framework/routing
[route-modules]: ../start/framework/route-module
[type-safety]: ../how-to/route-module-type-safety
