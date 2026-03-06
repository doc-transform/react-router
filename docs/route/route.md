---
title: Route
new: true
order: 1
---

# `Route`

路由可能是 React Router 应用中最重要的部分。它们将 URL 片段与组件、数据加载和数据变更耦合在一起。通过路由嵌套，复杂的应用布局和数据依赖变得简单且声明式。

路由是传递给路由器创建函数的对象：

```jsx
const router = createBrowserRouter([
  {
    // 它渲染此元素
    element: <Team />,

    // 当 URL 匹配此片段时
    path: "teams/:teamId",

    // 在渲染前加载此数据
    loader: async ({ request, params }) => {
      return fetch(
        `/fake/api/teams/${params.teamId}.json`,
        { signal: request.signal },
      );
    },

    // 当数据提交到此路由时执行此变更
    action: async ({ request }) => {
      return updateFakeTeam(await request.formData());
    },

    // 当出错时渲染此元素
    errorElement: <ErrorBoundary />,
  },
]);
```

你也可以使用 JSX 和 [`createRoutesFromElements`][createroutesfromelements] 来声明路由，元素的 props 与路由对象的属性完全相同：

```jsx
const router = createBrowserRouter(
  createRoutesFromElements(
    <Route
      element={<Team />}
      path="teams/:teamId"
      loader={async ({ params }) => {
        return fetch(
          `/fake/api/teams/${params.teamId}.json`,
        );
      }}
      action={async ({ request }) => {
        return updateFakeTeam(await request.formData());
      }}
      errorElement={<ErrorBoundary />}
    />,
  ),
);
```

两种风格都不会被反对，行为完全相同。本文档的大部分内容将使用 JSX 风格，因为在 React Router 的上下文中这是大多数人所习惯的。

<docs-info>当使用 `RouterProvider` 时，如果你不想指定 React 元素（即 `element={<MyComponent />}`），你可以改为指定一个 `Component`（即 `Component={MyComponent}`），React Router 将在内部为你调用 `createElement`。不过你应该只在 `RouterProvider` 应用中这样做，因为在 `<Routes>` 中使用 `Component` 会降低 React 跨渲染复用已创建元素的能力。</docs-info>

## 类型声明

```tsx
interface RouteObject {
  path?: string;
  index?: boolean;
  children?: RouteObject[];
  caseSensitive?: boolean;
  id?: string;
  loader?: LoaderFunction;
  action?: ActionFunction;
  element?: React.ReactNode | null;
  hydrateFallbackElement?: React.ReactNode | null;
  errorElement?: React.ReactNode | null;
  Component?: React.ComponentType | null;
  HydrateFallback?: React.ComponentType | null;
  ErrorBoundary?: React.ComponentType | null;
  handle?: RouteObject["handle"];
  shouldRevalidate?: ShouldRevalidateFunction;
  lazy?: LazyRouteFunction<RouteObject>;
}
```

## `path`

用于匹配 URL 的路径模式，以确定此路由是否匹配 URL、链接 href 或表单 action。

### 动态片段

如果路径片段以 `:` 开头，它将成为"动态片段"。当路由匹配 URL 时，动态片段将从 URL 中解析出来，并作为 `params` 提供给其他路由器 API。

```tsx
<Route
  // 此路径将匹配以下 URL
  // - /teams/hotspur
  // - /teams/real
  path="/teams/:teamId"
  // 匹配的参数将提供给 loader
  loader={({ params }) => {
    console.log(params.teamId); // "hotspur"
  }}
  // 以及 action
  action={({ params }) => {}}
  element={<Team />}
/>;

// 以及通过 `useParams` 提供给元素
function Team() {
  let params = useParams();
  console.log(params.teamId); // "hotspur"
}
```

一个路由路径中可以有多个动态片段：

```tsx
<Route path="/c/:categoryId/p/:productId" />;
// 两者都可以获取
params.categoryId;
params.productId;
```

动态片段不能是"部分的"：

- 🚫 `"/teams-:teamId"`
- ✅ `"/teams/:teamId"`
- 🚫 `"/:category--:productId"`
- ✅ `"/:productSlug"`

你仍然可以支持这样的 URL 模式，只需要自己做一些解析：

```tsx
function Product() {
  const { productSlug } = useParams();
  const [category, product] = productSlug.split("--");
  // ...
}
```

### 可选片段

你可以通过在片段末尾添加 `?` 来使路由片段变为可选的。

```tsx
<Route
  // 此路径将匹配以下 URL
  // - /categories
  // - /en/categories
  // - /fr/categories
  path="/:lang?/categories"
  // 匹配的参数可能在 loader 中可用
  loader={({ params }) => {
    console.log(params["lang"]); // "en"
  }}
  // 以及 action
  action={({ params }) => {}}
  element={<Categories />}
/>;

// 以及通过 `useParams` 提供给元素
function Categories() {
  let params = useParams();
  console.log(params.lang);
}
```

你也可以有可选的静态片段：

```jsx
<Route path="/project/task?/:taskId" />
```

### 通配符

也被称为 "catchall" 和 "star" 片段。如果路由路径模式以 `/*` 结尾，它将匹配 `/` 之后的任何字符，包括其他 `/` 字符。

```tsx
<Route
  // 此路径将匹配以下 URL
  // - /files
  // - /files/one
  // - /files/one/two
  // - /files/one/two/three
  path="/files/*"
  // 匹配的参数将提供给 loader
  loader={({ params }) => {
    console.log(params["*"]); // "one/two"
  }}
  // 以及 action
  action={({ params }) => {}}
  element={<Team />}
/>;

// 以及通过 `useParams` 提供给元素
function Team() {
  let params = useParams();
  console.log(params["*"]); // "one/two"
}
```

你可以解构 `*`，只需要给它分配一个新名称。常用名称是 `splat`：

```tsx
let { org, "*": splat } = params;
```

### 布局路由

省略 path 使此路由成为"布局路由"。它参与 UI 嵌套，但不向 URL 添加任何片段。

```tsx
<Route
  element={
    <div>
      <h1>Layout</h1>
      <Outlet />
    </div>
  }
>
  <Route path="/" element={<h2>Home</h2>} />
  <Route path="/about" element={<h2>About</h2>} />
</Route>
```

在此示例中，`<h1>Layout</h1>` 将与每个子路由的 `element` 属性一起渲染，通过布局路由的 [Outlet][outlet]。

## `index`

确定路由是否为索引路由。索引路由在其父级的 URL 处渲染到父级的 [Outlet][outlet] 中（类似于默认的子路由）。

```jsx [2]
<Route path="/teams" element={<Teams />}>
  <Route index element={<TeamsIndex />} />
  <Route path=":teamId" element={<Team />} />
</Route>
```

这些特殊路由起初可能会让人困惑，所以我们在这里有一份专门的指南：[索引路由][indexroute]。

## `children`

<docs-warning>（TODO: 需要讨论嵌套，可能需要单独的文档）</docs-warning>

## `caseSensitive`

指示路由匹配时是否区分大小写：

```jsx
<Route caseSensitive path="/wEll-aCtuA11y" />
```

- 将匹配 `"wEll-aCtuA11y"`
- 不会匹配 `"well-actua11y"`

## `loader`

路由 loader 在路由渲染之前被调用，并通过 [`useLoaderData`][useloaderdata] 为元素提供数据。

```tsx [3-5]
<Route
  path="/teams/:teamId"
  loader={({ params }) => {
    return fetchTeam(params.teamId);
  }}
/>;

function Team() {
  let team = useLoaderData();
  // ...
}
```

<docs-warning>如果你没有使用像 [`createBrowserRouter`][createbrowserrouter] 这样的数据路由器，这将不起作用</docs-warning>

请查看 [loader][loader] 文档了解更多细节。

## `action`

当从 [Form][form]、[fetcher][fetcher] 或 [submission][usesubmit] 向路由发送提交时，路由 action 将被调用。

```tsx [3-5]
<Route
  path="/teams/:teamId"
  action={({ request }) => {
    const formData = await request.formData();
    return updateTeam(formData);
  }}
/>
```

<docs-warning>如果你没有使用像 [`createBrowserRouter`][createbrowserrouter] 这样的数据路由器，这将不起作用</docs-warning>

请查看 [action][action] 文档了解更多细节。

## `element`/`Component`

当路由匹配 URL 时要渲染的 React Element/Component。

如果你想创建 React Element，使用 `element`：

```tsx
<Route path="/for-sale" element={<Properties />} />
```

否则使用 `Component`，React Router 将为你创建 React Element：

```tsx
<Route path="/for-sale" Component={Properties} />
```

<docs-warning>你应该只在通过 `RouterProvider` 使用数据路由时才选择 `Component` API。在 `<Routes>` 内的 `<Route>` 上使用此 API 会降低 React 跨渲染复用已创建元素的能力。</docs-warning>

## `errorElement`/`ErrorBoundary`

当路由在渲染、`loader` 或 `action` 中抛出异常时，将渲染此 React Element/Component 而不是正常的 `element`/`Component`。

如果你想自己创建 React Element，使用 `errorElement`：

```tsx
<Route
  path="/for-sale"
  // 如果渲染时抛出错误
  element={<Properties />}
  // 或加载属性时
  loader={() => loadProperties()}
  // 或创建属性时
  action={async ({ request }) =>
    createProperty(await request.formData())
  }
  // 则渲染此元素
  errorElement={<ErrorBoundary />}
/>
```

否则使用 `ErrorBoundary`，React Router 将为你创建 React Element：

```tsx
<Route
  path="/for-sale"
  Component={Properties}
  loader={() => loadProperties()}
  action={async ({ request }) =>
    createProperty(await request.formData())
  }
  ErrorBoundary={ErrorBoundary}
/>
```

<docs-warning>如果你没有使用像 [`createBrowserRouter`][createbrowserrouter] 这样的数据路由器，这将不起作用</docs-warning>

请查看 [errorElement][errorelement] 文档了解更多细节。

## `hydrateFallbackElement`/`HydrateFallback`

如果你使用[服务端渲染][ssr]并利用了[部分注水][partialhydration]，那么你可以为应用初始注水期间未注水的路由指定一个要渲染的 Element/Component。

<docs-warning>如果你没有使用像 [`createBrowserRouter`][createbrowserrouter] 这样的数据路由器，这将不起作用</docs-warning>

<docs-warning>这仅适用于更高级的用例，如 Remix 的 [`clientLoader`][clientloader] 功能。大多数 SSR 应用不需要使用这些路由属性。</docs-warning>

请查看 [hydrateFallbackElement][hydratefallbackelement] 文档了解更多细节。

## `handle`

任何应用特定的数据。请参阅 [useMatches][usematches] 文档了解详情和示例。

## `lazy`

为了保持应用包的小体积并支持路由的代码分割，每个路由可以提供一个异步函数来解析路由定义中非路由匹配的部分（`loader`、`action`、`Component`/`element`、`ErrorBoundary`/`errorElement` 等）。

每个 `lazy` 函数通常返回动态导入的结果。

```jsx
let routes = createRoutesFromElements(
  <Route path="/" element={<Layout />}>
    <Route path="a" lazy={() => import("./a")} />
    <Route path="b" lazy={() => import("./b")} />
  </Route>,
);
```

然后在你的懒加载路由模块中，导出你想要为路由定义的属性：

```jsx
export async function loader({ request }) {
  let data = await fetchData(request);
  return json(data);
}

export function Component() {
  let data = useLoaderData();

  return (
    <>
      <h1>You made it!</h1>
      <p>{data}</p>
    </>
  );
}
```

<docs-warning>如果你没有使用像 [`createBrowserRouter`][createbrowserrouter] 这样的数据路由器，这将不起作用</docs-warning>

请查看 [lazy][lazy] 文档了解更多细节。

[remix]: https://remix.run
[indexroute]: ../start/concepts#index-routes
[outlet]: ../components/outlet
[useloaderdata]: ../hooks/use-loader-data
[loader]: ./loader
[action]: ./action
[errorelement]: ./error-element
[hydratefallbackelement]: ./hydrate-fallback-element
[form]: ../components/form
[fetcher]: ../hooks/use-fetcher
[usesubmit]: ../hooks/use-submit
[createroutesfromelements]: ../utils/create-routes-from-elements
[createbrowserrouter]: ../routers/create-browser-router
[usematches]: ../hooks/use-matches
[lazy]: ./lazy
[ssr]: ../guides/ssr
[partialhydration]: ../routers/create-browser-router#partial-hydration-data
[clientloader]: https://remix.run/route/client-loader
