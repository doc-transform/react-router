---
title: 从 Remix 升级
order: 3
---

# 从 Remix 升级

<docs-info>

React Router v7 要求以下最低版本：

- `node@20`
- `react@18`
- `react-dom@18`

</docs-info>

React Router v7 是 Remix v2 之后的下一个主版本（更多信息请参阅我们的["React 19 的渐进式迁移路径"博客文章][incremental-path-to-react-19]）。

如果你已经启用了所有 [Remix v2 future flag][v2-future-flags]，从 Remix v2 升级到 React Router v7 主要是更新依赖。

<docs-info>

步骤 2-8 中的大部分操作可以通过社区成员 [James Restall][jrestall] 创建的 [codemod][codemod] 自动完成。

</docs-info>

## 1. 采用 Future Flag

**👉 采用 future flag**

在你的 Remix v2 应用中采用所有现有的 [future flag][v2-future-flags]。

## 2. 更新依赖

大多数之前通过运行时特定包（`@remix-run/node`、`@remix-run/cloudflare` 等）重新导出的"共享"API，在 v7 中都已合并到 `react-router` 中。因此，你需要直接从 `react-router` 导入，而不是从 `@react-router/node` 或 `@react-router/cloudflare` 导入。

```diff
-import { redirect } from "@remix-run/node";
+import { redirect } from "react-router";
```

在 v7 中你应该从运行时特定包中导入的 API 仅限于该运行时特有的 API，例如 Node 的 `createFileSessionStorage` 和 Cloudflare 的 `createWorkersKVSessionStorage`。

**👉 运行 codemod（自动方式）**

你可以使用以下 [codemod][codemod] 自动更新包和导入。请确保在运行 codemod 之前提交所有待处理的更改，以便需要时可以回退。

```shellscript nonumber
npx codemod remix/2/react-router/upgrade
```

**👉 安装新依赖**

在 codemod 更新了你的依赖后，你需要安装依赖以移除 Remix 包并添加新的 React Router 包。

```shellscript nonumber
npm install
```

**👉 手动更新依赖**

如果你不想使用 codemod，可以手动更新依赖。

<details>
<summary>展开查看按字母排序的包名称变更对照表</summary>

| Remix v2 包                        |     | React Router v7 包                          |
| ---------------------------------- | --- | ------------------------------------------- |
| `@remix-run/architect`             | ➡️  | `@react-router/architect`                   |
| `@remix-run/cloudflare`            | ➡️  | `@react-router/cloudflare`                  |
| `@remix-run/dev`                   | ➡️  | `@react-router/dev`                         |
| `@remix-run/express`               | ➡️  | `@react-router/express`                     |
| `@remix-run/fs-routes`             | ➡️  | `@react-router/fs-routes`                   |
| `@remix-run/node`                  | ➡️  | `@react-router/node`                        |
| `@remix-run/react`                 | ➡️  | `react-router`                              |
| `@remix-run/route-config`          | ➡️  | `@react-router/dev`                         |
| `@remix-run/routes-option-adapter` | ➡️  | `@react-router/remix-routes-option-adapter` |
| `@remix-run/serve`                 | ➡️  | `@react-router/serve`                       |
| `@remix-run/server-runtime`        | ➡️  | `react-router`                              |
| `@remix-run/testing`               | ➡️  | `react-router`                              |

</details>

## 3. 更新 `package.json` 中的 `scripts`

<docs-info>

如果你使用了 codemod，可以跳过此步骤，因为它已自动完成。

</docs-info>

**👉 更新 `package.json` 中的脚本**

| 脚本        | Remix v2                            |     | React Router v7                            |
| ----------- | ----------------------------------- | --- | ------------------------------------------ |
| `dev`       | `remix vite:dev`                    | ➡️  | `react-router dev`                         |
| `build`     | `remix vite:build`                  | ➡️  | `react-router build`                       |
| `start`     | `remix-serve build/server/index.js` | ➡️  | `react-router-serve build/server/index.js` |
| `typecheck` | `tsc`                               | ➡️  | `react-router typegen && tsc`              |

## 4. 添加 `routes.ts` 文件

<docs-info>

如果你使用了 codemod _且_ 使用了 Remix v2 的 `v3_routeConfig` flag，可以跳过此步骤，因为它已自动完成。

</docs-info>

在 React Router v7 中，你使用 `app/routes.ts` 文件来定义路由。查看[路由文档][routing]了解更多信息。

**👉 更新依赖（如果使用了 Remix v2 的 `v3_routeConfig` flag）**

```diff filename=app/routes.ts
-import { type RouteConfig } from "@remix-run/route-config";
-import { flatRoutes } from "@remix-run/fs-routes";
-import { remixRoutesOptionAdapter } from "@remix-run/routes-option-adapter";
+import { type RouteConfig } from "@react-router/dev/routes";
+import { flatRoutes } from "@react-router/fs-routes";
+import { remixRoutesOptionAdapter } from "@react-router/remix-routes-option-adapter";

export default [
  // 你的路由定义方式
] satisfies RouteConfig;
```

**👉 添加 `routes.ts` 文件（如果*没有*使用 Remix v2 的 `v3_routeConfig` flag）**

```shellscript nonumber
touch app/routes.ts
```

为了向后兼容，有几种方式可以使 `routes.ts` 与你在 Remix v2 中的路由配置保持一致：

1. 如果你之前使用的是"扁平路由"[文件系统约定][fs-routing]，可以通过新的 `@react-router/fs-routes` 包继续使用：

   ```ts filename=app/routes.ts
   import { type RouteConfig } from "@react-router/dev/routes";
   import { flatRoutes } from "@react-router/fs-routes";

   export default flatRoutes() satisfies RouteConfig;
   ```

2. 如果你使用的是 Remix v1 的"嵌套"约定（通过 `@remix-run/v1-route-convention` 包），也可以结合 `@react-router/remix-routes-option-adapter` 继续使用：

   ```ts filename=app/routes.ts
   import { type RouteConfig } from "@react-router/dev/routes";
   import { remixRoutesOptionAdapter } from "@react-router/remix-routes-option-adapter";
   import { createRoutesFromFolders } from "@remix-run/v1-route-convention";

   export default remixRoutesOptionAdapter(
     createRoutesFromFolders,
   ) satisfies RouteConfig;
   ```

3. 如果你使用的是 `routes` 选项来定义基于配置的路由，可以通过 `@react-router/remix-routes-option-adapter` 保留该配置：

   ```ts filename=app/routes.ts
   import { type RouteConfig } from "@react-router/dev/routes";
   import { remixRoutesOptionAdapter } from "@react-router/remix-routes-option-adapter";

   export default remixRoutesOptionAdapter(
     (defineRoutes) => {
       return defineRoutes((route) => {
         route("/", "home/route.tsx", { index: true });
         route("about", "about/route.tsx");
         route("", "concerts/layout.tsx", () => {
           route("trending", "concerts/trending.tsx");
           route(":city", "concerts/city.tsx");
         });
       });
     },
   ) satisfies RouteConfig;
   ```

   - 同时请确保移除 `vite.config.ts` 中的 `routes` 选项：

     ```diff filename=vite.config.ts
     export default defineConfig({
       plugins: [
         remix({
           ssr: true,
     -     ignoredRouteFiles: ['**/*'],
     -     routes(defineRoutes) {
     -       return defineRoutes((route) => {
     -         route("/somewhere/cool/*", "catchall.tsx");
     -       });
     -     },
         })
         tsconfigPaths(),
       ],
     });
     ```

## 5. 添加 React Router 配置

**👉 在项目中添加 `react-router.config.ts`**

之前传递给 `vite.config.ts` 中 `remix` 插件的配置现在从 `react-router.config.ts` 导出。

注意：此时你应该移除在步骤 1 中添加的 v3 future flag。

```shellscript nonumber
touch react-router.config.ts
```

```diff filename=vite.config.ts
export default defineConfig({
  plugins: [
-   remix({
-     ssr: true,
-     future: {/* 所有的 v3 flag */}
-   }),
+   reactRouter(),
    tsconfigPaths(),
  ],
});
```

```diff filename=react-router.config.ts
+import type { Config } from "@react-router/dev/config";
+export default {
+  ssr: true,
+} satisfies Config;
```

## 6. 在 `vite.config` 中添加 React Router 插件

<docs-info>

如果你使用了 codemod，可以跳过此步骤，因为它已自动完成。

</docs-info>

**👉 在 `vite.config` 中添加 `reactRouter` 插件**

修改 `vite.config.ts`，从 `@react-router/dev/vite` 导入并使用新的 `reactRouter` 插件：

```diff filename=vite.config.ts
-import { vitePlugin as remix } from "@remix-run/dev";
+import { reactRouter } from "@react-router/dev/vite";
import { defineConfig } from "vite";
import tsconfigPaths from "vite-tsconfig-paths";

export default defineConfig({
  plugins: [
-   remix(),
+   reactRouter(),
    tsconfigPaths(),
  ],
});
```

## 7. 启用类型安全

<docs-info>

如果你没有使用 TypeScript，可以跳过此步骤。

</docs-info>

React Router 会自动为你的路由模块在应用根目录的 `.react-router/` 目录中生成类型。该目录完全由 React Router 管理，应添加到 gitignore 中。了解更多关于[新的类型安全功能][type-safety]。

**👉 在 `.gitignore` 中添加 `.react-router/`**

```txt
.react-router/
```

**👉 更新 `tsconfig.json`**

更新 `tsconfig.json` 中的 `types` 字段：

- 在 `include` 字段中添加 `.react-router/types/**/*` 路径
- 在 `types` 字段中添加适当的 `@react-router/*` 包
- 添加 `rootDirs` 以简化相对导入

```diff filename=tsconfig.json
{
  "include": [
    /* ... */
+   ".react-router/types/**/*"
  ],
  "compilerOptions": {
-   "types": ["@remix-run/node", "vite/client"],
+   "types": ["@react-router/node", "vite/client"],
    /* ... */
+   "rootDirs": [".", "./.react-router/types"]
  }
}
```

## 8. 重命名入口文件中的组件

<docs-info>

如果你使用了 codemod，可以跳过此步骤，因为它已自动完成。

</docs-info>

如果你的应用中有 `entry.server.tsx` 和/或 `entry.client.tsx` 文件，你需要更新这些文件中的主要组件：

```diff filename=app/entry.server.tsx
-import { RemixServer } from "@remix-run/react";
+import { ServerRouter } from "react-router";

-<RemixServer context={remixContext} url={request.url} />,
+<ServerRouter context={remixContext} url={request.url} />,
```

```diff filename=app/entry.client.tsx
-import { RemixBrowser } from "@remix-run/react";
+import { HydratedRouter } from "react-router/dom";

hydrateRoot(
  document,
  <StrictMode>
-   <RemixBrowser />
+   <HydratedRouter />
  </StrictMode>,
);
```

## 9. 更新 `AppLoadContext` 的类型

<docs-info>

如果你之前使用的是 `remix-serve`，可以跳过此步骤。此步骤仅适用于在 Remix v2 中使用自定义服务器的情况。

</docs-info>

由于 React Router 既可以作为 React 框架使用，*也*可以作为独立的路由库使用，`LoaderFunctionArgs` 和 `ActionFunctionArgs` 的 `context` 参数现在是可选的，默认类型为 `any`。你可以为你的 load context 注册类型，以获得 loader 和 action 的类型安全。

👉 **为你的 load context 注册类型**

在迁移到新的 `Route.LoaderArgs` 和 `Route.ActionArgs` 类型之前，你可以临时扩展 `LoaderFunctionArgs` 和 `ActionFunctionArgs`，添加你的 load context 类型以便于迁移。

```ts filename=app/env.ts
declare module "react-router" {
  // 你在 v2 中使用的 AppLoadContext
  interface AppLoadContext {
    whatever: string;
  }

  // TODO: 当我们的 loader 迁移到 `Route.LoaderArgs` 后移除此项
  interface LoaderFunctionArgs {
    context: AppLoadContext;
  }

  // TODO: 当我们的 action 迁移到 `Route.ActionArgs` 后移除此项
  interface ActionFunctionArgs {
    context: AppLoadContext;
  }
}

export {}; // TypeScript 将此视为模块所必需的
```

<docs-info>

使用 `declare module` 注册类型是一种标准的 TypeScript 技术，称为[模块增强][ts-module-augmentation]。你可以在 `tsconfig.json` 的 `include` 字段覆盖的任何 TypeScript 文件中进行此操作，但我们建议在应用目录中使用专门的 `env.ts` 文件。

</docs-info>

👉 **使用新的类型**

一旦你采用了[新的类型生成][type-safety]，就可以移除 `LoaderFunctionArgs`/`ActionFunctionArgs` 的扩展，改用 [`Route.LoaderArgs`][server-loaders] 和 [`Route.ActionArgs`][server-actions] 中的 `context` 参数。

```ts filename=app/env.ts
declare module "react-router" {
  // 你在 v2 中使用的 AppLoadContext
  interface AppLoadContext {
    whatever: string;
  }
}

export {}; // TypeScript 将此视为模块所必需的
```

```ts filename=app/routes/my-route.tsx
import type { Route } from "./+types/my-route";

export function loader({ context }: Route.LoaderArgs) {}
// { whatever: string }  ^^^^^^^

export function action({ context }: Route.ActionArgs) {}
// { whatever: string }  ^^^^^^^
```

恭喜！你现在已经升级到 React Router v7 了。请运行你的应用确保一切正常工作。

[incremental-path-to-react-19]: https://remix.run/blog/incremental-path-to-react-19
[v2-future-flags]: https://remix.run/docs/start/future-flags
[routing]: ../start/framework/routing
[fs-routing]: ../how-to/file-route-conventions
[v7-changelog-types]: https://github.com/remix-run/react-router/blob/release-next/CHANGELOG.md#type-safety-improvements
[server-loaders]: ../start/framework/data-loading#server-data-loading
[server-actions]: ../start/framework/actions#server-actions
[ts-module-augmentation]: https://www.typescriptlang.org/docs/handbook/declaration-merging.html#module-augmentation
[type-safety]: ../explanation/type-safety
[codemod]: https://codemod.com/registry/remix-2-react-router-upgrade
[jrestall]: https://github.com/jrestall
