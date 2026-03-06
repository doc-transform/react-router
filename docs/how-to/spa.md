---
title: 单页应用 (SPA)
---

# 单页应用 (SPA)

[MODES: framework]

<br/>
<br/>

<docs-info>本指南重点介绍如何使用 React Router 框架模式构建单页应用。如果你使用的是声明式或数据模式的 React Router，可以设计自己的 SPA 架构。</docs-info>

使用 React Router 作为框架时，你可以通过在 `react-router.config.ts` 文件中设置 `ssr:false` 来启用"SPA 模式"。这将禁用运行时服务端渲染，并在构建时生成一个 `index.html`，你可以将其作为 SPA 来提供和注水。

典型的单页应用发送一个几乎空白的 `index.html` 模板，内容不多于一个空的 `<div id="root"></div>`。相比之下，`react-router build`（在 SPA 模式下）会在构建时预渲染你的根路由到一个 `index.html` 文件。这意味着你可以：

- 发送更多内容而非空的 `<div>`
- 使用根路由的 `loader` 为应用外壳加载数据
- 使用 React 组件生成用户看到的初始页面（根 `HydrateFallback`）
- 以后重新启用服务端渲染而无需更改 UI

<docs-info>SPA 模式是"预渲染"的一种特殊形式，允许你从同一个 HTML 文件提供应用中的所有路径。如果你想做更全面的预渲染，请参阅[预渲染](./pre-rendering)指南。</docs-info>

## 1. 禁用运行时服务端渲染

服务端渲染默认启用。在 `react-router.config.ts` 中将 `ssr` 标志设置为 `false` 来禁用它。

```ts filename=react-router.config.ts lines=[4]
import { type Config } from "@react-router/dev/config";

export default {
  ssr: false,
} satisfies Config;
```

设置为 false 后，将不再生成服务端构建。

<docs-info>需要注意的是，设置 `ssr:false` 只禁用 _运行时_ 服务端渲染。React Router 仍然会在 _构建时_ 服务端渲染你的根路由以生成 `index.html` 文件。这就是为什么你的项目仍然需要依赖 `@react-router/node`，并且你的路由需要兼容 SSR。这意味着即使禁用了服务端渲染，你也不能在初始渲染期间调用 `window` 或其他仅浏览器可用的 API。</docs-info>

## 2. 向根路由添加 `HydrateFallback` 和可选的 `loader`

SPA 模式将在构建时生成一个 `index.html` 文件，你可以将其作为 SPA 的入口点。它只会渲染根路由，以便能够在运行时为应用中的任何路径进行注水。

为了提供比空 `<div>` 更好的加载 UI，你可以向根路由添加 `HydrateFallback` 组件，在构建时将加载 UI 渲染到 `index.html` 中。这样在 SPA 加载/注水期间，用户会立即看到它。

```tsx filename=root.tsx lines=[7-9]
import LoadingScreen from "./components/loading-screen";

export function Layout() {
  return <html>{/*...*/}</html>;
}

export function HydrateFallback() {
  return <LoadingScreen />;
}

export default function App() {
  return <Outlet />;
}
```

因为根路由在构建时就进行了服务端渲染，你也可以选择在根路由中使用 `loader`。这个 `loader` 将在构建时调用，数据将通过可选的 `HydrateFallback` 的 `loaderData` prop 提供。

```tsx filename=root.tsx lines=[5,10,14]
import { Route } from "./+types/root";

export async function loader() {
  return {
    version: await getVersion(),
  };
}

export function HydrateFallback({
  loaderData,
}: Route.ComponentProps) {
  return (
    <div>
      <h1>正在加载版本 {loaderData.version}...</h1>
      <AwesomeSpinner />
    </div>
  );
}
```

使用 SPA 模式时，你不能在应用的其他路由中包含 `loader`，除非你正在[预渲染那些页面](./pre-rendering)。

## 3. 使用客户端 loader 和客户端 action

禁用服务端渲染后，你仍然可以使用 `clientLoader` 和 `clientAction` 来管理路由数据和变更。

```tsx filename=some-route.tsx
import { Route } from "./+types/some-route";

export async function clientLoader({
  params,
}: Route.ClientLoaderArgs) {
  let data = await fetch(`/some/api/stuff/${params.id}`);
  return data;
}

export async function clientAction({
  request,
}: Route.ClientActionArgs) {
  let formData = await request.formData();
  return await processPayment(formData);
}
```

## 4. 将所有 URL 指向 index.html

运行 `react-router build` 后，将 `build/client` 目录部署到你喜欢的任何静态主机。

与部署任何 SPA 一样，你需要配置主机将所有 URL 指向客户端构建的 `index.html`。有些主机默认就是这样做的，但其他主机不是。例如，主机可能支持 `_redirects` 文件来实现这一点：

```
/*    /index.html   200
```

如果你在应用的有效路由上收到 404，很可能需要配置你的主机。

```

```
