---
title: 选择模式
order: 1
---

# 选择模式

React Router 是一个面向 React 的多策略路由器。它有三种主要的使用方式，即"模式"。在文档中你会看到以下图标，表示相应内容适用于哪种模式：

[MODES: framework, data, declarative]

<p></p>

每种模式提供的功能是累加式的，从声明式模式到数据模式再到框架模式，功能逐步增多，但相应地你对架构的控制权会减少。因此，请根据你希望从 React Router 获得多少控制权或多少帮助来选择合适的模式。

使用哪种模式取决于你使用的"顶层"路由 API：

## 声明式模式

声明式模式提供基本的路由功能，如将 URL 匹配到组件、在应用中导航，以及通过 `<Link>`、`useNavigate` 和 `useLocation` 等 API 提供激活状态。

```tsx
import { BrowserRouter } from "react-router";

ReactDOM.createRoot(root).render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
);
```

## 数据模式

将路由配置移到 React 渲染之外后，数据模式增加了数据加载、操作（action）、待定状态等功能，提供了 `loader`、`action` 和 `useFetcher` 等 API。

```tsx
import {
  createBrowserRouter,
  RouterProvider,
} from "react-router";

let router = createBrowserRouter([
  {
    path: "/",
    Component: Root,
    loader: loadRootData,
  },
]);

ReactDOM.createRoot(root).render(
  <RouterProvider router={router} />,
);
```

## 框架模式

框架模式在数据模式的基础上封装了一个 Vite 插件，提供完整的 React Router 体验，包括：

- 类型安全的 `href`
- 类型安全的路由模块 API
- 智能代码拆分
- SPA、SSR 和静态渲染策略
- 以及更多功能

```ts filename=routes.ts
import { index, route } from "@react-router/dev/routes";

export default [
  index("./home.tsx"),
  route("products/:pid", "./product.tsx"),
];
```

之后你就可以使用路由模块 API，享受类型安全的参数、loaderData、代码拆分、SPA/SSR/SSG 策略等功能。

```ts filename=product.tsx
import { Route } from "./+types/product.tsx";

export async function loader({ params }: Route.LoaderArgs) {
  let product = await getProduct(params.pid);
  return { product };
}

export default function Product({
  loaderData,
}: Route.ComponentProps) {
  return <div>{loaderData.product.name}</div>;
}
```

## 选择建议

每种模式都支持任何架构和部署目标，所以问题的关键不在于你是否需要 SSR、SPA 等，而在于你想自己掌控多少。

**如果你符合以下情况，请使用框架模式：**

- 刚入门还没有形成自己的偏好
- 正在考虑 Next.js、Solid Start、SvelteKit、Astro、TanStack Start 等方案，想做对比
- 只想用 React 构建应用
- 可能需要服务端渲染，也可能不需要
- 从 Remix 迁移过来（React Router v7 是 Remix v2 之后的"下一个版本"）
- 从 Next.js 迁移过来

[→ 开始使用框架模式](./framework/installation)

**如果你符合以下情况，请使用数据模式：**

- 需要数据功能，但也想自己控制打包、数据和服务端抽象
- 在 v6.4 中已经开始使用数据路由并且很满意

[→ 开始使用数据模式](./data/custom)

**如果你符合以下情况，请使用声明式模式：**

- 想尽可能简单地使用 React Router
- 从 v6 迁移过来，对 `<BrowserRouter>` 感到满意
- 已有数据层，要么不需要待定状态（如本地优先、后台数据复制/同步），要么已有自己的抽象方案
- 从 Create React App 迁移过来（不过你可能需要考虑框架模式）

[→ 开始使用声明式模式](./declarative/installation)

## API 与模式可用性对照表

这张表主要是给 LLM 参考的，不过你也可以自行查阅：

| API                            | 框架模式 | 数据模式 | 声明式模式 |
| ------------------------------ | -------- | -------- | ---------- |
| Await                          | ✅       | ✅       |            |
| Form                           | ✅       | ✅       |            |
| Link                           | ✅       | ✅       | ✅         |
| `<Link discover>`              | ✅       |          |            |
| `<Link prefetch>`              | ✅       |          |            |
| `<Link preventScrollReset>`    | ✅       | ✅       |            |
| Links                          | ✅       |          |            |
| Meta                           | ✅       |          |            |
| NavLink                        | ✅       | ✅       | ✅         |
| `<NavLink discover>`           | ✅       |          |            |
| `<NavLink prefetch>`           | ✅       |          |            |
| `<NavLink preventScrollReset>` | ✅       | ✅       |            |
| NavLink `isPending`            | ✅       | ✅       |            |
| Navigate                       | ✅       | ✅       | ✅         |
| Outlet                         | ✅       | ✅       | ✅         |
| PrefetchPageLinks              | ✅       |          |            |
| Route                          | ✅       | ✅       | ✅         |
| Routes                         | ✅       | ✅       | ✅         |
| Scripts                        | ✅       |          |            |
| ScrollRestoration              | ✅       | ✅       |            |
| ServerRouter                   | ✅       |          |            |
| usePrompt                      | ✅       | ✅       |            |
| useActionData                  | ✅       | ✅       |            |
| useAsyncError                  | ✅       | ✅       |            |
| useAsyncValue                  | ✅       | ✅       |            |
| useBeforeUnload                | ✅       | ✅       | ✅         |
| useBlocker                     | ✅       | ✅       |            |
| useFetcher                     | ✅       | ✅       |            |
| useFetchers                    | ✅       | ✅       |            |
| useFormAction                  | ✅       | ✅       |            |
| useHref                        | ✅       | ✅       | ✅         |
| useInRouterContext             | ✅       | ✅       | ✅         |
| useLinkClickHandler            | ✅       | ✅       | ✅         |
| useLoaderData                  | ✅       | ✅       |            |
| useLocation                    | ✅       | ✅       | ✅         |
| useMatch                       | ✅       | ✅       | ✅         |
| useMatches                     | ✅       | ✅       |            |
| useNavigate                    | ✅       | ✅       | ✅         |
| useNavigation                  | ✅       | ✅       |            |
| useNavigationType              | ✅       | ✅       | ✅         |
| useOutlet                      | ✅       | ✅       | ✅         |
| useOutletContext               | ✅       | ✅       | ✅         |
| useParams                      | ✅       | ✅       | ✅         |
| useResolvedPath                | ✅       | ✅       | ✅         |
| useRevalidator                 | ✅       | ✅       |            |
| useRouteError                  | ✅       | ✅       |            |
| useRouteLoaderData             | ✅       | ✅       |            |
| useRoutes                      | ✅       | ✅       | ✅         |
| useSearchParams                | ✅       | ✅       | ✅         |
| useSubmit                      | ✅       | ✅       |            |
| useViewTransitionState         | ✅       | ✅       |            |
| isCookieFunction               | ✅       | ✅       |            |
| isSessionFunction              | ✅       | ✅       |            |
| createCookie                   | ✅       | ✅       |            |
| createCookieSessionStorage     | ✅       | ✅       |            |
| createMemorySessionStorage     | ✅       | ✅       |            |
| createPath                     | ✅       | ✅       | ✅         |
| createRoutesFromElements       |          | ✅       |            |
| createRoutesStub               | ✅       | ✅       |            |
| createSearchParams             | ✅       | ✅       | ✅         |
| data                           | ✅       | ✅       |            |
| generatePath                   | ✅       | ✅       | ✅         |
| href                           | ✅       |          |            |
| isCookie                       | ✅       | ✅       |            |
| isRouteErrorResponse           | ✅       | ✅       |            |
| isSession                      | ✅       | ✅       |            |
| matchPath                      | ✅       | ✅       | ✅         |
| matchRoutes                    | ✅       | ✅       | ✅         |
| parsePath                      | ✅       | ✅       | ✅         |
| redirect                       | ✅       | ✅       |            |
| redirectDocument               | ✅       | ✅       |            |
| renderMatches                  | ✅       | ✅       | ✅         |
| replace                        | ✅       | ✅       |            |
| resolvePath                    | ✅       | ✅       | ✅         |
