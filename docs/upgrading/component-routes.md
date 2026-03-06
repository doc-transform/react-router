---
title: 从组件路由迁移到框架模式
order: 4
---

# 从组件路由迁移到框架模式

如果你使用的是 `<RouterProvider>`，请参阅[从 RouterProvider 迁移到框架模式][upgrade-router-provider]。

如果你使用的是 `<Routes>`，那你来对地方了。

React Router Vite 插件为 React Router 增加了框架功能。本指南将帮助你在应用中采用该插件。如果你遇到任何问题，请通过 [Twitter](https://x.com/remix_run) 或 [Discord](https://rmx.as/discord) 寻求帮助。

## 功能

Vite 插件增加了以下功能：

- 路由 loader、action 和自动数据重新验证
- 类型安全的路由模块
- 自动路由代码拆分
- 跨导航的自动滚动恢复
- 可选的静态预渲染
- 可选的服务端渲染

初始设置需要的工作量最大。不过一旦完成，你可以逐步采用新功能，每次迁移一个路由。

## 前提条件

使用 Vite 插件要求你的项目满足：

- Node.js 20+（如果使用 Node 作为运行时）
- Vite 5+

## 1. 安装 Vite 插件

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

## 2. 添加 React Router 配置

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

## 3. 添加根入口点

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

## 4. 添加客户端入口模块

在典型的 Vite 应用中，`index.html` 文件指向 `src/main.tsx` 作为客户端入口点。React Router 使用名为 `src/entry.client.tsx` 的文件。

**👉 将 `src/entry.client.tsx` 设为入口点**

如果你当前的 `src/main.tsx` 如下所示：

```tsx filename=src/main.tsx
import React from "react";
import ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router";
import "./index.css";
import App from "./App";

ReactDOM.createRoot(
  document.getElementById("root")!,
).render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>,
);
```

你需要将其重命名为 `entry.client.tsx` 并修改为：

```tsx filename=src/entry.client.tsx
import React from "react";
import ReactDOM from "react-dom/client";
import { HydratedRouter } from "react-router/dom";
import "./index.css";

ReactDOM.hydrateRoot(
  document,
  <React.StrictMode>
    <HydratedRouter />
  </React.StrictMode>,
);
```

- 使用 `hydrateRoot` 代替 `createRoot`
- 渲染 `<HydratedRouter>` 代替 `<App/>` 组件
- 注意：我们暂时不再渲染 `<App/>` 组件。我们会在后面的步骤中把它加回来，但首先要确保应用能用新的入口点启动。

## 5. 调整代码位置

在 `root.tsx` 和 `entry.client.tsx` 之间，你可能需要在它们之间移动一些代码。

一般来说：

- `root.tsx` 包含所有渲染相关的内容，如 context provider、布局、样式等。
- `entry.client.tsx` 应尽可能精简
- 记住暂时*不要*尝试渲染你现有的 `<App/>` 组件，我们会在后面的步骤中处理

注意：你的 `root.tsx` 文件会被静态生成并作为应用的入口点提供，因此仅该模块需要兼容服务端渲染。这是你可能遇到最多问题的地方。

## 6. 设置路由

React Router Vite 插件使用 `routes.ts` 文件来配置路由。现在我们先添加一个简单的全匹配路由来让应用运行起来。

**👉 设置一个 `catchall.tsx` 路由**

```shellscript nonumber
touch src/routes.ts src/catchall.tsx
```

```ts filename=src/routes.ts
import {
  type RouteConfig,
  route,
} from "@react-router/dev/routes";

export default [
  // * 匹配所有 URL，? 使其成为可选的，这样它也能匹配 /
  route("*?", "catchall.tsx"),
] satisfies RouteConfig;
```

**👉 渲染一个占位路由**

最终我们会用原来的 `App` 组件替换它，但现在先渲染一些简单的内容来确保应用能启动。

```tsx filename=src/catchall.tsx
export default function Component() {
  return <div>Hello, world!</div>;
}
```

查看我们的[路由配置指南][configuring-routes]了解更多关于 `routes.ts` 文件的信息。

## 7. 启动应用

此时你应该能够启动应用并看到根布局了。

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

## 8. 渲染你的应用

要恢复渲染你的应用，我们需要更新之前设置的匹配所有 URL 的"全匹配"路由，让你现有的 `<Routes>` 能够渲染。

**👉 更新全匹配路由以渲染你的应用**

```tsx filename=src/catchall.tsx
import App from "./App";

export default function Component() {
  return <App />;
}
```

你的应用应该重新出现在屏幕上并正常工作了！

## 9. 将路由迁移为路由模块

现在你可以逐步将路由迁移为路由模块了。

假设有一个现有路由如下：

```tsx filename=src/App.tsx
// ...
import About from "./containers/About";

export default function App() {
  return (
    <Routes>
      <Route path="/about" element={<About />} />
    </Routes>
  );
}
```

**👉 在 `routes.ts` 中添加路由定义**

```tsx filename=src/routes.ts
import {
  type RouteConfig,
  route,
} from "@react-router/dev/routes";

export default [
  route("/about", "./pages/about.tsx"),
  route("*?", "catchall.tsx"),
] satisfies RouteConfig;
```

**👉 添加路由模块**

编辑路由模块以使用[路由模块 API][route-modules]：

```tsx filename=src/pages/about.tsx
export async function clientLoader() {
  // 现在你可以在这里获取数据
  return {
    title: "About page",
  };
}

export default function Component({ loaderData }) {
  return <h1>{loaderData.title}</h1>;
}
```

查看[类型安全][type-safety]了解如何设置自动生成的参数、loader 数据等的类型安全。

迁移的前几个路由是最困难的，因为你经常需要以不同于之前的方式访问各种抽象（比如在 loader 中而不是从 hook 或 context 中获取）。但一旦最棘手的部分处理完毕，你就会进入一个渐进式的节奏。

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

[upgrade-router-provider]: ./router-provider
[configuring-routes]: ../start/framework/routing
[route-modules]: ../start/framework/route-module
[type-safety]: ../how-to/route-module-type-safety
