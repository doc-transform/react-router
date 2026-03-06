---
title: 路由
order: 2
---

# 路由

[MODES: framework]

## 配置路由

路由在 `app/routes.ts` 中配置。每个路由有两个必需部分：用于匹配 URL 的 URL 模式，以及定义路由行为的路由模块文件路径。

```ts filename=app/routes.ts
import {
  type RouteConfig,
  route,
} from "@react-router/dev/routes";

export default [
  route("some/path", "./some/file.tsx"),
  // 模式 ^           ^ 模块文件
] satisfies RouteConfig;
```

以下是一个更完整的路由配置示例：

```ts filename=app/routes.ts
import {
  type RouteConfig,
  route,
  index,
  layout,
  prefix,
} from "@react-router/dev/routes";

export default [
  index("./home.tsx"),
  route("about", "./about.tsx"),

  layout("./auth/layout.tsx", [
    route("login", "./auth/login.tsx"),
    route("register", "./auth/register.tsx"),
  ]),

  ...prefix("concerts", [
    index("./concerts/home.tsx"),
    route(":city", "./concerts/city.tsx"),
    route("trending", "./concerts/trending.tsx"),
  ]),
] satisfies RouteConfig;
```

如果你更喜欢通过文件命名约定而非配置来定义路由，`@react-router/fs-routes` 包提供了[文件系统路由约定][file-route-conventions]。你甚至可以混合使用不同的路由约定：

```ts filename=app/routes.ts
import {
  type RouteConfig,
  route,
} from "@react-router/dev/routes";
import { flatRoutes } from "@react-router/fs-routes";

export default [
  route("/", "./home.tsx"),

  ...(await flatRoutes()),
] satisfies RouteConfig;
```

## 路由模块

`routes.ts` 中引用的文件定义了每个路由的行为：

```tsx filename=app/routes.ts
route("teams/:teamId", "./team.tsx"),
//           路由模块 ^^^^^^^^
```

以下是一个路由模块示例：

```tsx filename=app/team.tsx
// 提供类型安全/推断
import type { Route } from "./+types/team";

// 为组件提供 `loaderData`
export async function loader({ params }: Route.LoaderArgs) {
  let team = await fetchTeam(params.teamId);
  return { name: team.name };
}

// 在 loader 完成后渲染
export default function Component({
  loaderData,
}: Route.ComponentProps) {
  return <h1>{loaderData.name}</h1>;
}
```

路由模块还有更多功能，如 action、headers 和错误边界，这些将在下一个指南中介绍：[路由模块](./route-module)。

## 嵌套路由

路由可以嵌套在父路由中。

```ts filename=app/routes.ts
import {
  type RouteConfig,
  route,
  index,
} from "@react-router/dev/routes";

export default [
  // 父路由
  route("dashboard", "./dashboard.tsx", [
    // 子路由
    index("./home.tsx"),
    route("settings", "./settings.tsx"),
  ]),
] satisfies RouteConfig;
```

父路由的路径会自动包含在子路由中，因此上面的配置同时创建了 `"/dashboard"` 和 `"/dashboard/settings"` 两个 URL。

子路由通过父路由中的 `<Outlet/>` 进行渲染。

```tsx filename=app/dashboard.tsx
import { Outlet } from "react-router";

export default function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      {/* 这里渲染的是 home.tsx 或 settings.tsx */}
      <Outlet />
    </div>
  );
}
```

## 根路由

`routes.ts` 中的每个路由都嵌套在特殊的 `app/root.tsx` 模块内。

## 布局路由

使用 `layout`，布局路由为其子路由创建新的嵌套层级，但不向 URL 添加任何路径段。它类似于根路由，但可以添加在任何层级。

```tsx filename=app/routes.ts lines=[10,16]
import {
  type RouteConfig,
  route,
  layout,
  index,
  prefix,
} from "@react-router/dev/routes";

export default [
  layout("./marketing/layout.tsx", [
    index("./marketing/home.tsx"),
    route("contact", "./marketing/contact.tsx"),
  ]),
  ...prefix("projects", [
    index("./projects/home.tsx"),
    layout("./projects/project-layout.tsx", [
      route(":pid", "./projects/project.tsx"),
      route(":pid/edit", "./projects/edit-project.tsx"),
    ]),
  ]),
] satisfies RouteConfig;
```

注意：

- `home.tsx` 和 `contact.tsx` 会渲染到 `marketing/layout.tsx` 的 outlet 中，且不会创建新的 URL 路径
- `project.tsx` 和 `edit-project.tsx` 会在 `/projects/:pid` 和 `/projects/:pid/edit` 处渲染到 `projects/project-layout.tsx` 的 outlet 中，而 `projects/home.tsx` 不会。

## 索引路由

```ts
index(componentFile),
```

索引路由在父路由的 URL 处渲染到父路由的 [Outlet][outlet] 中（类似于默认子路由）。

```ts filename=app/routes.ts
import {
  type RouteConfig,
  route,
  index,
} from "@react-router/dev/routes";

export default [
  // 在 / 处渲染到 root.tsx 的 Outlet 中
  index("./home.tsx"),
  route("dashboard", "./dashboard.tsx", [
    // 在 /dashboard 处渲染到 dashboard.tsx 的 Outlet 中
    index("./dashboard-home.tsx"),
    route("settings", "./dashboard-settings.tsx"),
  ]),
] satisfies RouteConfig;
```

注意：索引路由不能有子路由。

## 路由前缀

使用 `prefix`，你可以为一组路由添加路径前缀，而无需引入父路由。

```tsx filename=app/routes.ts lines=[14]
import {
  type RouteConfig,
  route,
  layout,
  index,
  prefix,
} from "@react-router/dev/routes";

export default [
  layout("./marketing/layout.tsx", [
    index("./marketing/home.tsx"),
    route("contact", "./marketing/contact.tsx"),
  ]),
  ...prefix("projects", [
    index("./projects/home.tsx"),
    layout("./projects/project-layout.tsx", [
      route(":pid", "./projects/project.tsx"),
      route(":pid/edit", "./projects/edit-project.tsx"),
    ]),
  ]),
] satisfies RouteConfig;
```

注意：这不会在路由树中引入新的路由，而只是修改其子路由的路径。

例如，以下两组路由是等价的：

```ts filename=app/routes.ts
// 使用 `prefix`...
prefix("parent", [
  route("child1", "./child1.tsx"),
  route("child2", "./child2.tsx"),
])

// ...等价于：
[
  route("parent/child1", "./child1.tsx"),
  route("parent/child2", "./child2.tsx"),
]
```

## 动态路径段

如果路径段以 `:` 开头，则它会成为"动态段"。当路由匹配 URL 时，动态段会从 URL 中解析出来，并作为 `params` 提供给其他路由 API。

```ts filename=app/routes.ts
route("teams/:teamId", "./team.tsx"),
```

```tsx filename=app/team.tsx
import type { Route } from "./+types/team";

export async function loader({ params }: Route.LoaderArgs) {
  //                           ^? { teamId: string }
}

export default function Component({
  params,
}: Route.ComponentProps) {
  params.teamId;
  //        ^ string
}
```

一个路由路径中可以有多个动态段：

```ts filename=app/routes.ts
route("c/:categoryId/p/:productId", "./product.tsx"),
```

```tsx filename=app/product.tsx
import type { Route } from "./+types/product";

async function loader({ params }: LoaderArgs) {
  //                    ^? { categoryId: string; productId: string }
}
```

## 可选路径段

你可以在路径段末尾添加 `?` 使其成为可选的。

```ts filename=app/routes.ts
route(":lang?/categories", "./categories.tsx"),
```

静态路径段也可以是可选的：

```ts filename=app/routes.ts
route("users/:userId/edit?", "./user.tsx");
```

## 通配符

也称为"全匹配"和"星号"路径段。如果路由路径模式以 `/*` 结尾，它将匹配 `/` 之后的任何字符，包括其他 `/` 字符。

```ts filename=app/routes.ts
route("files/*", "./files.tsx"),
```

```tsx filename=app/files.tsx
export async function loader({ params }: Route.LoaderArgs) {
  // params["*"] 包含 files/ 之后的剩余 URL
}
```

你可以解构 `*`，但需要给它一个新名称。常用的名称是 `splat`：

```tsx
const { "*": splat } = params;
```

你也可以使用通配符来捕获不匹配任何路由的请求：

```ts filename=app/routes.ts
route("*", "./catchall.tsx"); // 全匹配路由
```

```tsx filename=app/catchall.tsx
export function loader() {
  throw new Response("Page not found", { status: 404 });
}
```

## 组件路由

你也可以在组件树的任何位置使用将 URL 匹配到元素的组件：

```tsx
import { Routes, Route } from "react-router";

function Wizard() {
  return (
    <div>
      <h1>Some Wizard with Steps</h1>
      <Routes>
        <Route index element={<StepOne />} />
        <Route path="step-2" element={<StepTwo />} />
        <Route path="step-3" element={<StepThree />} />
      </Routes>
    </div>
  );
}
```

注意：这些路由不参与数据加载、action、代码拆分或任何其他路由模块功能，因此它们的使用场景比路由模块更有限。

---

下一节：[路由模块](./route-module)

[file-route-conventions]: ../../how-to/file-route-conventions
[outlet]: https://api.reactrouter.com/v7/functions/react-router.Outlet.html
