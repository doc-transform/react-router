---
title: 迁移到 RouterProvider
order: 1
---

# 迁移到 RouterProvider

当我们最初开始将 [Remix 数据 API 引入 React Router][remixing-react-router] 时，我们意识到它们带来了一种相当不同的路由结构方式。不再是在 React 渲染组件树时通过 [`<Routes>`][routes-component] 组件发现路由，而是需要将路由定义*提升*出来，以便我们可以[将数据获取与渲染解耦][when-to-fetch]。

这带来了一个有趣的难题。我们有大量的 v6 [`BrowserRouter`][browserrouter] 应用正在通过 `<Routes>` 组件愉快地定义路由——我们如何为它们提供一个平滑的升级体验，而不需要一次性大规模迁移到新方法？这排除了发布新的主版本，我们专注于以*完全向后兼容*的方式添加这些新功能，为用户提供从 [`BrowserRouter`][browserrouter] 到 [`RouterProvider`][routerprovider] 的*渐进式*升级路径。

## 差异

首先需要了解的是一些新的[数据 API][data-apis]，它们只在通过新的[数据路由器][picking-a-router]（即 [`createBrowserRouter`][createbrowserrouter]）*定义*的路由上工作。这些包括几类 API：

- 路由级别的数据 API，如 `loader`、`action`、`shouldRevalidate`、`handle` 和 `lazy`
- 组件内的数据 hook，如 `useLoaderData`、`useActionData`、`useFetcher`、`useMatches`、`useNavigation` 等
- 错误处理 API，如 `route.errorElement`、`route.ErrorBoundary` 和 `useRouteError`

v6.4.0 之前存在的其余 API 在 `BrowserRouter` 和 `RouterProvider` 应用中都可以使用。包括常用的 hook/组件，如 `useNavigate`、`useLocation`、`useParams`、`<Link>`、`<Outlet />` 等。

## 迁移

我们构建新的 `<RouterProvider>` 组件时，使其能够在根路由器中定义的路由上启用新的数据 API，同时不排除在 `BrowserRouter` 应用中常用的后代 `<Routes>` 树。这是为了明确支持从一个到另一个的渐进式迁移。让我们看看如何操作。

### 当前应用

假设我们有一个包含 2 个后代路由树的当前应用，并且假设这些路由都在组件内部获取数据，并渲染它们自己的加载和错误状态。

```tsx
import {
  BrowserRouter,
  Link,
  Route,
  Routes,
} from "react-router-dom";

export default function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/blog/*" element={<BlogApp />} />
        <Route path="/users/*" element={<UserApp />} />
      </Routes>
    </BrowserRouter>
  );
}

function Home() {
  return (
    <>
      <h1>Welcome!</h1>
      <p>
        Check out the <Link to="/blog">blog</Link> or the{" "}
        <Link to="users">users</Link> section
      </p>
    </>
  );
}

function BlogApp() {
  return (
    <Routes>
      <Route index element={<h1>Blog Index</h1>} />
      <Route path="posts" element={<h1>Blog Posts</h1>} />
    </Routes>
  );
}

function UserApp() {
  return (
    <Routes>
      <Route index element={<h1>Users Index</h1>} />
    </Routes>
  );
}
```

### 添加带有根 splat 路由的 RouterProvider

我们可以只做一些小改动就把这个应用渲染在 `RouterProvider` 中：

1. 将当前的 `App` 组件改名为 `Root`
2. 移除 `<BrowserRouter>` 组件
3. 创建一个带有 splat 路由的数据路由器单例，用于 `Root` 元素
4. 添加一个新的 `App` 组件渲染 `<RouterProvider>`

```tsx lines=[9-12,14-17,19-20,21-22]
import {
  createBrowserRouter,
  Link,
  Route,
  RouterProvider,
  Routes,
} from "react-router-dom";

// 3️⃣ Router singleton created
const router = createBrowserRouter([
  { path: "*", element: <Root /> },
]);

// 4️⃣ RouterProvider added
export default function App() {
  return <RouterProvider router={router} />;
}

// 1️⃣ 从 App 改名为 Root
function Root() {
  // 2️⃣ 移除了 `BrowserRouter` 组件，但下面的 <Routes>/<Route>
  // 组件保持不变
  return (
    <Routes>
      <Route path="/" element={<Home />} />
      <Route path="/blog/*" element={<BlogApp />} />
      <Route path="/users/*" element={<UserApp />} />
    </Routes>
  );
}

function Home() {
  /* Unchanged */
}
function BlogApp() {
  /* Unchanged */
}
function UserApp() {
  /* Unchanged */
}
```

🥳 恭喜——你现在正在渲染一个数据路由器应用！但等等——我们还不能使用任何新功能，因为我们的路由都没有在顶层通过 `createBrowserRouter` 定义 😢。要访问新的 API，我们需要开始一个一个地将路由提升到数据路由器中。

### 开始提升路由并利用数据 API

让我们从 `<Home>` 元素的 `/` 路由开始。我们只需要将 `<Route>` 定义提升到数据路由器：

```tsx lines=[2,13]
const router = createBrowserRouter([
  { path: "/", element: <Home /> }, // 🆕
  { path: "*", element: <Root /> },
]);

export default function App() {
  return <RouterProvider router={router} />;
}

function Root() {
  return (
    <Routes>
      {/* ⬆️ Home 路由已提升到数据路由器 */}
      <Route path="/blog/*" element={<BlogApp />} />
      <Route path="/users/*" element={<UserApp />} />
    </Routes>
  );
}
```

现在你可以给 home 路由添加数据 API（`loader`、`action`、`errorElement`），并开始在 Home 组件内使用数据 hook（`useLoaderData`、`useActionData`、`useFetcher`、`<Form>` 等）。

现在让我们看看如何将 Blog 应用向上提升，但仍然一次一个叶子路由地操作。为了将 `/blog` 索引路由提升上去，我们需要同时提升 `/blog/*` splat 路由，但我们仍然可以将 `/blog/posts` 路由保留在原处并单独处理。

```tsx lines=[3-12,23,32]
const router = createBrowserRouter([
  { path: "/", element: <Home /> },
  {
    // 提升的 blog splat 路由
    path: "/blog/*",
    children: [
      // 新的 blog 索引路由
      { index: true, element: <h1>Blog Index</h1> },
      // 为 /blog/posts 匹配添加的 Blog 子应用 splat 路由
      { path: "*", element: <BlogApp /> },
    ],
  },
  { path: "*", element: <Root /> },
]);

export default function App() {
  return <RouterProvider router={router} />;
}

function Root() {
  return (
    <Routes>
      {/* ⬆️ Blog splat 路由已提升 */}
      <Route path="/users/*" element={<UserApp />} />
    </Routes>
  );
}

function BlogApp() {
  return (
    <Routes>
      {/* ⬆️ Blog 索引路由已提升 */}
      <Route path="posts" element={<h1>Blog Posts</h1>} />
    </Routes>
  );
}
```

现在你的 blog 索引路由可以参与数据加载了。

你可以继续一次一个路由地操作，直到最终将所有路由转换为数据路由，不再使用任何嵌套的 `<Routes>` 来定义路由树。为了避免 bundle 体积膨胀，建议利用 [route.lazy][route-lazy] 属性来懒加载路由。

## 常见问题

### 但我在 `<BrowserRouter>` 和 `<Routes>` 之间有内容

许多人会通过类似以下方式在 `<Routes>` 周围渲染一个应用外壳：

```jsx
export default function App() {
  return (
    <BrowserRouter>
      <header>
        <h1>My Super Cool App</h1>
        <NavMenu />
      </header>
      <main>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/blog/*" element={<BlogApp />} />
          <Route path="/users/*" element={<UserApp />} />
        </Routes>
      </main>
      <footer>©️ me 2023</footer>
    </BrowserRouter>
  );
}
```

如果你发现自己处于这种情况，不用担心——有一个简单的解决方案，你可以在开始上述迁移之前执行。

这种情况很常见，但在上述迁移方式中会造成问题，因为我们需要逐个路由地提升到 `RouterProvider`，但这个“应用外壳”不是路由的一部分...但它可以是！那个“应用外壳”实际上只不过是一个带有 `<Outlet>` 的布局路由！所以在开始上述迁移之前，只需将这个“应用外壳”移到一个无路径的布局路由中，包裹在你的路由周围，如下所示：

```jsx lines=[6,25]
export default function App() {
  return (
    <BrowserRouter>
      <Routes>
        {/* 1️⃣ 将路由包裹在无路径的布局路由中 */}
        <Route element={<Layout />}>
          <Route path="/" element={<Home />} />
          <Route path="/blog/*" element={<BlogApp />} />
          <Route path="/users/*" element={<UserApp />} />
        </Route>
      </Routes>
    </BrowserRouter>
  );
}

function Layout() {
  return (
    <>
      <header>
        <h1>My Super Cool App</h1>
        <NavMenu />
      </header>
      <main>
        {/* 2️⃣ 通过 Layout 的 Outlet 渲染应用路由 */}
        <Outlet />
      </main>
      <footer>©️ me 2023</footer>
    </>
  );
}
```

完成后，你就可以按照上述迁移策略继续进行，并开始逐个将路由提升到 `RouterProvider` 中。你可能会想先提升布局路由，这样所有子路由都可以嵌套在其中。

[remixing-react-router]: https://remix.run/blog/remixing-react-router
[when-to-fetch]: https://www.youtube.com/watch?v=95B8mnhzoCM
[picking-a-router]: ../routers/picking-a-router
[data-apis]: ../routers/picking-a-router#data-apis
[createbrowserrouter]: ../routers/create-browser-router
[routerprovider]: ../routers/router-provider
[browserrouter]: ../router-components/browser-router
[routes-component]: ../components/routes
[route-lazy]: ../route/lazy
