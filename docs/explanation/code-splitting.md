---
title: 自动代码分割
---

# 自动代码分割

[MODES: framework]

<br/>
<br/>

使用 React Router 的框架功能时，你的应用会自动进行代码分割，以提高用户访问应用时的初始加载性能。

## 按路由代码分割

考虑以下简单的路由配置：

```tsx filename=app/routes.ts
import {
  type RouteConfig,
  route,
} from "@react-router/dev/routes";

export default [
  route("/contact", "./contact.tsx"),
  route("/about", "./about.tsx"),
] satisfies RouteConfig;
```

引用的模块（`contact.tsx` 和 `about.tsx`）不会被打包成一个巨大的构建产物，而是成为打包工具的入口点。

因为这些入口点与 URL 段耦合，React Router 仅通过 URL 就知道浏览器中需要哪些包，更重要的是，哪些是不需要的。

如果用户访问 `"/about"`，则 `about.tsx` 的包将被加载，但 `contact.tsx` 不会。这大大减少了初始页面加载的 JavaScript 体积，加速了你的应用。

## 移除服务端代码

任何仅限服务端的[路由模块 API][route-module] 将从包中移除。考虑以下路由模块：

```tsx
export async function loader() {
  return { message: "hello" };
}

export async function action() {
  console.log(Date.now());
  return { ok: true };
}

export async function headers() {
  return { "Cache-Control": "max-age=300" };
}

export default function Component({ loaderData }) {
  return <div>{loaderData.message}</div>;
}
```

为浏览器构建后，只有 `Component` 仍会存在于包中，因此你可以在其他模块导出中使用仅限服务端的代码。

[route-module]: ../../start/framework/route-module
