# React Router 开发指南

## 命令

- **构建**：`pnpm build`（所有包）或 `pnpm run --filter <package> build`（单个包）
- **测试（Jest）**：`pnpm test`（所有包），`pnpm test packages/<package>/`（单个包），`pnpm test packages/react-router/__tests__/router/fetchers-test.ts`（单个文件），或 `pnpm test -- -t "action fetch"`（匹配名称的测试）
- **集成测试（Playwright）**：`pnpm test:integration --project chromium`（构建 + 测试全部），`pnpm test:integration:run --project chromium`（仅测试，全部），`pnpm test:integration:run --project chromium integration/middleware-test.ts`（单个文件），或 `pnpm test:integration:run --project chromium -g "middleware"`（匹配名称的测试）
- **类型检查**：`pnpm run typecheck`
- **代码检查**：`pnpm run lint`
- **文档生成**：`pnpm run docs`（从 JSDoc 重新生成 API 文档）
- **类型生成**：`pnpm run typegen`（仅 Framework 模式）
- **清理**：`pnpm run clean`（git clean -fdX）

## 模式

**五种不同模式**：声明式、数据、Framework、RSC 数据（不稳定）、RSC Framework（不稳定）。**始终明确功能适用于哪种模式。**

1. **声明式**：`<BrowserRouter>`、`<Routes>`、`<Route>`
2. **数据**：使用 `loader`/`action` 的 `createBrowserRouter()`，`<RouterProvider>`
3. **Framework**：Vite 插件 + `routes.ts` + 路由模块 API（路由导出如 `loader`、`action`、`default`）+ 类型生成 + SSR/SPA
4. **RSC 数据**（不稳定）：RSC 运行时 API，手动打包器设置，运行时路由配置
5. **RSC Framework**（不稳定）：Framework 模式 + `unstable_reactRouterRSC` Vite 插件

**RSC 模式差异：**

- **RSC Framework**：`unstable_reactRouterRSC` 插件，`@vitejs/plugin-rsc`，不同的入口点/格式
- **RSC 数据**：手动打包器，运行时路由配置通常在 `src/routes.ts` 中，`unstable_RSCRouteConfig`，不同的运行时 API，`integration/rsc/` 中的 `setupRscTest`

## 架构

- **Monorepo**：pnpm 工作空间，包在 `packages/` 中
- **核心包**：
  - `react-router`：核心（所有模式）- `lib/components.tsx`、`lib/hooks.tsx`、`lib/router/`、`lib/dom/`、`lib/rsc/`
  - `@react-router/dev`：Framework 工具 - `vite/plugin.ts`（Framework）、`vite/rsc/plugin.ts`（RSC Framework）、`typegen/`
  - `react-router-dom`：重导出 `react-router`（v6→v7 兼容）
  - `@react-router/node`、`@react-router/cloudflare`、`@react-router/express`：服务器适配器
  - `@react-router/serve`：Framework 模式的最小化服务器
  - `@react-router/fs-routes`：文件系统路由（`flatRoutes()`）

## 测试

### 单元测试（`packages/react-router/__tests__/`）

使用 Jest 测试纯路由逻辑、纯服务器运行时行为、路由器状态、React 组件行为。无需构建。

```bash
pnpm test                                                          # 所有包
pnpm test packages/react-router/                                   # 单个包
pnpm test packages/react-router/__tests__/router/fetchers-test.ts  # 单个文件
pnpm test -- -t "action fetch"                                     # 匹配名称的测试
```

### 集成测试（`integration/`）

使用 Playwright 测试 Vite 插件、构建管线、SSR/hydration、RSC、类型生成。

```bash
pnpm test:integration --project chromium                                     # 构建 + 测试全部
pnpm test:integration:run --project chromium                                 # 仅测试，全部
pnpm test:integration:run --project chromium integration/middleware-test.ts  # 单个文件
pnpm test:integration:run --project chromium -g "middleware"                 # 匹配名称的测试
```

**项目**：集成测试始终使用 `chromium`，除非另有明确说明。

**重新构建时机**：首次运行时，或更改 `packages/` 后（仅更改测试不需要重新构建）

**组织方式**：使用 `createFixture()` → `createAppFixture()` → `PlaywrightFixture`。可用模板：`vite-6-template/`、`rsc-vite-framework/` 等。测试所有适用的模式（当行为应跨模式工作时遍历模板数组）。引入 future 标志时测试两种状态（一个测试启用标志，一个测试禁用标志）。

**RSC 测试**：

- **RSC Framework**：使用 `createFixture` 配合 `rsc-vite-framework/` 模板
- **RSC 数据**：使用 `integration/rsc/` 中的 `setupRscTest`

跨多个模板测试共享行为（例如 `["vite-5-template", "rsc-vite-framework"]`）。针对 RSC 模板测试 RSC 特有的功能。

## routes.ts

Framework 模式使用 `app/` 中的 `routes.ts`。大多数测试使用 `flatRoutes()` 进行文件系统路由：

```ts
// app/routes.ts
import { type RouteConfig } from "@react-router/dev/routes";
import { flatRoutes } from "@react-router/fs-routes";

export default flatRoutes() satisfies RouteConfig;
```

**文件系统约定**（`app/routes/`）：

- `_index.tsx` → `/`（索引路由）
- `about.tsx` → `/about`
- `blog.$slug.tsx` → `/blog/:slug`（URL 参数）
- `settings.profile.tsx` → `/settings/profile`（`.` 创建嵌套）
- `_layout.tsx` → 无路径布局路由

**手动配置替代方案**：

```ts
import { index, route, layout } from "@react-router/dev/routes";
export default [
  index("./home.tsx"),
  route("about", "./about.tsx"),
  layout("./auth-layout.tsx", [route("login", "./login.tsx")]),
];
```

## 文档

**不要编辑生成的文件**：`docs/api/`（来自 JSDoc）、`.react-router/types/`（来自 typegen）

**模式标识**：每个文档都需要 `[MODES: framework, data, declarative]`

**API 文档**：编辑 `packages/react-router/lib/` 中的 JSDoc，运行 `pnpm docs`

**不稳定功能**：添加 `unstable_` 前缀，在 frontmatter 中添加 `unstable: true`，包含警告块

## Future 标志

- **Future 标志**（`vX_*`）：下一个主要版本的稳定破坏性变更
- **不稳定标志**（`unstable_*`）：实验性功能，可能更改

测试 future 标志的两种状态（启用/禁用）。没有标志不要破坏现有行为。

## Changesets

当进行影响用户的更改时，在 `.changeset/<unique-meaningful-name>.md` 创建一个 changeset。如果在尚未发布的更改上进行迭代，请更新现有的 changeset 文件而不是创建新的。

格式：

```markdown
---
"react-router": patch
"@react-router/dev": minor
---

变更的简要描述

- 如需要可添加额外详情
```

## 分支策略

- **`main`**：最新稳定版本
- **`dev`**：活跃开发（从此处创建代码更改的分支）
- **`v6`**：v6.x 维护
- 仅文档更改从 `main` 创建分支

## 关键文件

| 用途          | 位置                                                        |
| ------------- | ----------------------------------------------------------- |
| 路由器        | `packages/react-router/lib/router/router.ts`                |
| React API     | `packages/react-router/lib/components.tsx`、`lib/hooks.tsx` |
| Vite 插件     | `packages/react-router-dev/vite/plugin.ts`                  |
| RSC Vite 插件 | `packages/react-router-dev/vite/rsc/plugin.ts`              |
| 类型生成      | `packages/react-router-dev/typegen/`                        |
| 单元测试      | `packages/react-router/__tests__/`                          |
| 集成测试      | `integration/`                                              |
| 决策文档      | `decisions/`                                                |
