---
title: 类型安全
---

# 类型安全

[MODES: framework]

<br/>
<br/>

如果你还没有这样做，请查看我们关于在新项目中[设置类型安全][route-module-type-safety]的指南。

React Router 为应用中的每个路由生成类型，以提供路由模块导出的类型安全。

例如，假设你配置了一个 `products/:id` 路由：

```ts filename=app/routes.ts
import {
  type RouteConfig,
  route,
} from "@react-router/dev/routes";

export default [
  route("products/:id", "./routes/product.tsx"),
] satisfies RouteConfig;
```

你可以像这样导入路由特定的类型：

```tsx filename=app/routes/product.tsx
import type { Route } from "./+types/product";
// 为此路由生成的类型 👆

export function loader({ params }: Route.LoaderArgs) {
  //                      👆 { id: string }
  return { planet: `world #${params.id}` };
}

export default function Component({
  loaderData, // 👈 { planet: string }
}: Route.ComponentProps) {
  return <h1>Hello, {loaderData.planet}!</h1>;
}
```

## 工作原理

React Router 的类型生成会执行你的路由配置（默认为 `app/routes.ts`）来确定应用的路由。然后它在特殊的 `.react-router/types/` 目录中为每个路由生成一个 `+types/<route file>.d.ts`。通过配置 [`rootDirs`][route-module-type-safety]，TypeScript 可以导入这些生成的文件，就像它们在对应路由模块的旁边一样。

要深入了解一些设计决策，请查看我们的[类型推断决策文档](https://github.com/remix-run/react-router/blob/dev/decisions/0012-type-inference.md)。

[route-module-type-safety]: ../how-to/route-module-type-safety

## `typegen` 命令

你可以使用 `typegen` 命令手动生成类型：

```sh
react-router typegen
```

为每个路由生成以下类型：

- `LoaderArgs`
- `ClientLoaderArgs`
- `ActionArgs`
- `ClientActionArgs`
- `HydrateFallbackProps`
- `ComponentProps`（用于 `default` 导出）
- `ErrorBoundaryProps`

### --watch

如果你运行 `react-router dev` —— 或者你的自定义服务器调用了 `vite.createServer` —— 那么 React Router 的 Vite 插件已经在为你生成最新的类型了。但如果你确实需要单独运行类型生成，你也可以使用 `--watch` 在文件变更时自动重新生成类型：

```sh
react-router typegen --watch
```
