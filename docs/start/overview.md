---
title: 功能概览
order: 1
---

# 功能概览

## 客户端路由

React Router 实现了“客户端路由”。

在传统网站中，浏览器从 Web 服务器请求文档，下载并评估 CSS 和 JavaScript 资源，并渲染从服务器发送的 HTML。当用户点击链接时，它会为新页面重新开始整个过程。

客户端路由允许你的应用从链接点击中更新 URL，而无需向服务器发起另一个文档请求。相反，你的应用可以立即渲染一些新 UI，并使用 `fetch` 发起数据请求来用新信息更新页面。

这可以实现更快的用户体验，因为浏览器不需要为下一个页面请求全新的文档或重新评估 CSS 和 JavaScript 资源。它还可以通过动画等方式实现更动态的用户体验。

通过创建 `Router` 并使用 `Link` 和 `<Form>` 链接/提交到页面来启用客户端路由：

```jsx [10,16,27]
import * as React from "react";
import { createRoot } from "react-dom/client";
import {
  createBrowserRouter,
  RouterProvider,
  Route,
  Link,
} from "react-router-dom";

const router = createBrowserRouter([
  {
    path: "/",
    element: (
      <div>
        <h1>Hello World</h1>
        <Link to="about">About Us</Link>
      </div>
    ),
  },
  {
    path: "about",
    element: <div>About</div>,
  },
]);

createRoot(document.getElementById("root")).render(
  <RouterProvider router={router} />,
);
```

## 嵌套路由

嵌套路由是将 URL 段与组件层级和数据耦合的总体思想。React Router 的嵌套路由灵感来源于 2014 年左右 Ember.js 中的路由系统。Ember 团队意识到，在几乎每种情况下，URL 的段决定了：

- 页面上要渲染的布局
- 这些布局的数据依赖

React Router 通过 API 采纳了这一约定，用于创建与 URL 段和数据耦合的嵌套布局。

```jsx
// Configure nested routes with JSX
createBrowserRouter(
  createRoutesFromElements(
    <Route path="/" element={<Root />}>
      <Route path="contact" element={<Contact />} />
      <Route
        path="dashboard"
        element={<Dashboard />}
        loader={({ request }) =>
          fetch("/api/dashboard.json", {
            signal: request.signal,
          })
        }
      />
      <Route element={<AuthLayout />}>
        <Route
          path="login"
          element={<Login />}
          loader={redirectIfUser}
        />
        <Route path="logout" action={logoutUser} />
      </Route>
    </Route>,
  ),
);

// Or use plain objects
createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    children: [
      {
        path: "contact",
        element: <Contact />,
      },
      {
        path: "dashboard",
        element: <Dashboard />,
        loader: ({ request }) =>
          fetch("/api/dashboard.json", {
            signal: request.signal,
          }),
      },
      {
        element: <AuthLayout />,
        children: [
          {
            path: "login",
            element: <Login />,
            loader: redirectIfUser,
          },
          {
            path: "logout",
            action: logoutUser,
          },
        ],
      },
    ],
  },
]);
```

这个[可视化工具](https://remix.run/_docs/routing)可能会有帮助。

## 动态段

URL 的段可以是动态占位符，它们会被解析并提供给各种 API。

```jsx
<Route path="projects/:projectId/tasks/:taskId" />
```

带 `:` 的两个段是动态的，提供给以下 API：

```jsx
// If the current location is /projects/abc/tasks/3
<Route
  // 发送给 loaders
  loader={({ params }) => {
    params.projectId; // abc
    params.taskId; // 3
  }}
  // 和 actions
  action={({ params }) => {
    params.projectId; // abc
    params.taskId; // 3
  }}
  element={<Task />}
/>;

function Task() {
  // 从 `useParams` 返回
  const params = useParams();
  params.projectId; // abc
  params.taskId; // 3
}

function Random() {
  const match = useMatch(
    "/projects/:projectId/tasks/:taskId",
  );
  match.params.projectId; // abc
  match.params.taskId; // 3
}
```

查看：

- [`<Route path>`][path]
- [`<Route loader>`][loader]
- [`<Route action>`][action]
- [`useParams`][useparams]
- [`useMatch`][usematch]

## 路由排名匹配

当将 URL 与路由匹配时，React Router 会根据段数、静态段、动态段、通配符等对路由进行排名，并选择*最具体的*匹配。

例如，考虑以下两个路由：

```jsx
<Route path="/teams/:teamId" />
<Route path="/teams/new" />
```

现在考虑 URL 是 http://example.com/teams/new。

即使两个路由在技术上都匹配该 URL（`new` 可以是 `:teamId`），你直觉上知道我们想要选择第二个路由（`/teams/new`）。React Router 的匹配算法也知道这一点。

有了排名路由，你不必担心路由顺序。

## 活动链接

大多数 Web 应用在 UI 顶部、侧边栏以及通常多个层级都有持久性的导航部分。使用 `<NavLink>` 可以轻松地为活动的导航项添加样式，让用户知道他们在应用中的位置（`isActive`）或将要去哪里（`isPending`）。

```jsx
<NavLink
  style={({ isActive, isPending }) => {
    return {
      color: isActive ? "red" : "inherit",
    };
  }}
  className={({ isActive, isPending }) => {
    return isActive ? "active" : isPending ? "pending" : "";
  }}
/>
```

你还可以使用 [`useMatch`][usematch] 在链接之外进行任何其他“活动”指示。

```jsx
function SomeComp() {
  const match = useMatch("/messages");
  return <li className={Boolean(match) ? "active" : ""} />;
}
```

查看：

- [`NavLink`][navlink]
- [`useMatch`][usematch]

## 相对链接

与 HTML 的 `<a href>` 类似，`<Link to>` 和 `<NavLink to>` 可以使用相对路径，在嵌套路由中有增强的行为。

给定以下路由配置：

```jsx
<Route path="home" element={<Home />}>
  <Route path="project/:projectId" element={<Project />}>
    <Route path=":taskId" element={<Task />} />
  </Route>
</Route>
```

考虑 url https://example.com/home/project/123，它渲染以下路由组件层级：

```jsx
<Home>
  <Project />
</Home>
```

如果 `<Project />` 渲染以下链接，链接的 href 将如下解析：

| In `<Project>` @ `/home/project/123` | Resolved `<a href>`     |
| ------------------------------------ | ----------------------- |
| `<Link to="abc">`                    | `/home/project/123/abc` |
| `<Link to=".">`                      | `/home/project/123`     |
| `<Link to="..">`                     | `/home`                 |
| `<Link to=".." relative="path">`     | `/home/project`         |

注意第一个 `..` 移除了 `project/:projectId` 路由的两个段。默认情况下，相对链接中的 `..` 遍历的是路由层级，而不是 URL 段。在下一个示例中添加 `relative="path"` 允许你遍历路径段。

相对链接始终相对于它们*渲染所在的*路由路径，而不是完整的 URL。这意味着如果用户通过 `<Link to="abc">` 深入导航到 `/home/project/123/abc` 的 `<Task />`，`<Project>` 中的 href 不会改变（与普通 `<a href>` 不同，这是客户端路由器的常见问题）。

## 数据加载

因为 URL 段通常映射到应用的持久化数据，React Router 提供了约定俗成的数据加载 hooks，在导航期间启动数据加载。结合嵌套路由，特定 URL 上多个布局的所有数据可以并行加载。

```jsx
<Route
  path="/"
  loader={async ({ request }) => {
    // loader 可以是异步函数
    const res = await fetch("/api/user.json", {
      signal: request.signal,
    });
    const user = await res.json();
    return user;
  }}
  element={<Root />}
>
  <Route
    path=":teamId"
    // loader 理解 Fetch Response 并会自动
    // 解包 res.json()，所以你可以直接返回一个 fetch
    loader={({ params }) => {
      return fetch(`/api/teams/${params.teamId}`);
    }}
    element={<Team />}
  >
    <Route
      path=":gameId"
      loader={({ params }) => {
        // 当然你可以使用任何数据存储
        return fakeSdk.getTeam(params.gameId);
      }}
      element={<Game />}
    />
  </Route>
</Route>
```

数据通过 `useLoaderData` 提供给你的组件。

```jsx
function Root() {
  const user = useLoaderData();
  // 来自 <Route path="/"> 的数据
}

function Team() {
  const team = useLoaderData();
  // 来自 <Route path=":teamId"> 的数据
}

function Game() {
  const game = useLoaderData();
  // 来自 <Route path=":gameId"> 的数据
}
```

当用户访问或点击链接到 https://example.com/real-salt-lake/45face3 时，所有三个路由 loader 都会被调用并并行加载，然后才渲染该 URL 的 UI。

## 重定向

在加载或更改数据时，将用户[重定向][redirect]到不同的路由是常见的。

```jsx
<Route
  path="dashboard"
  loader={async () => {
    const user = await fake.getUser();
    if (!user) {
      // 如果你知道不能渲染该路由，你可以
      // 抛出一个重定向来停止执行这里的代码，
      // 将用户发送到新路由
      throw redirect("/login");
    }

    // 否则继续
    const stats = await fake.getDashboardStats();
    return { user, stats };
  }}
/>
```

```jsx
<Route
  path="project/new"
  action={async ({ request }) => {
    const data = await request.formData();
    const newProject = await createProject(data);
    // action 完成后重定向是常见的，
    // 将用户发送到新记录
    return redirect(`/projects/${newProject.id}`);
  }}
/>
```

查看：

- [`redirect`][redirect]
- [Throwing in Loaders][throwing]
- [`useNavigate`][usenavigate]

## 待定导航 UI

当用户在应用中导航时，下一页的数据会在页面渲染之前加载。在此期间提供用户反馈很重要，这样应用不会感觉像没有响应。

```jsx lines=[2,5]
function Root() {
  const navigation = useNavigation();
  return (
    <div>
      {navigation.state === "loading" && <GlobalSpinner />}
      <FakeSidebar />
      <Outlet />
      <FakeFooter />
    </div>
  );
}
```

查看：

- [`useNavigation`][usenavigation]

## 使用 `<Suspense>` 的骨架屏 UI

不用等待下一页的数据，你可以 [`defer`][defer] 数据，这样 UI 会立即切换到下一个屏幕并显示占位 UI，同时数据在加载。

```jsx lines=[12,22-29,32-35]
<Route
  path="issue/:issueId"
  element={<Issue />}
  loader={async ({ params }) => {
    // 这些是 promise，但*没有*被 await
    const comments = fake.getIssueComments(params.issueId);
    const history = fake.getIssueHistory(params.issueId);
    // issue 则*被* await 了
    const issue = await fake.getIssue(params.issueId);

    // defer 为未 await 的 promise 启用 suspense
    return defer({ issue, comments, history });
  }}
/>;

function Issue() {
  const { issue, history, comments } = useLoaderData();
  return (
    <div>
      <IssueDescription issue={issue} />

      {/* Suspense 提供占位 fallback */}
      <Suspense fallback={<IssueHistorySkeleton />}>
        {/* Await 管理延迟数据（promise） */}
        <Await resolve={history}>
          {/* 数据解析时回调 */}
          {(resolvedHistory) => (
            <IssueHistory history={resolvedHistory} />
          )}
        </Await>
      </Suspense>

      <Suspense fallback={<IssueCommentsSkeleton />}>
        <Await resolve={comments}>
          {/* ...或者你可以使用 hooks 访问数据 */}
          <IssueComments />
        </Await>
      </Suspense>
    </div>
  );
}

function IssueComments() {
  const comments = useAsyncValue();
  return <div>{/* ... */}</div>;
}
```

See

- [Deferred Data Guide][deferreddata]
- [`defer`][defer]
- [`Await`][await]
- [`useAsyncValue`][useasyncvalue]

## 数据变更

HTML 表单是导航事件，就像链接一样。React Router 通过客户端路由支持 HTML 表单工作流。

当表单被提交时，普通的浏览器导航事件被阻止，并创建一个 [`Request`][request]，其 body 包含提交的 [`FormData`][formdata]。这个请求被发送到与表单的 `<Form action>` 匹配的 `<Route action>`。

表单元素的 `name` prop 会被提交到 action：

```jsx
<Form action="/project/new">
  <label>
    Project title
    <br />
    <input type="text" name="title" />
  </label>

  <label>
    Target Finish Date
    <br />
    <input type="date" name="due" />
  </label>
</Form>
```

普通的 HTML 文档请求被阻止并发送到匹配路由的 action（与 `<form action>` 匹配的 `<Route path>`），包括 `request.formData`。

```jsx
<Route
  path="project/new"
  action={async ({ request }) => {
    const formData = await request.formData();
    const newProject = await createProject({
      title: formData.get("title"),
      due: formData.get("due"),
    });
    return redirect(`/projects/${newProject.id}`);
  }}
/>
```

## 数据重新验证

数十年的 Web 约定表明，当表单提交到服务器时，数据正在更改并渲染新页面。这个约定在 React Router 基于 HTML 的数据变更 API 中得到了遵循。

路由 action 被调用后，页面上所有数据的 loader 会再次被调用，以确保 UI 自动与数据保持同步。无需过期缓存键，无需重新加载 context provider。

查看：

- [Tutorial "Creating Contacts"][creatingcontacts]

## 忙碌指示器

当表单正在提交到路由 action 时，你可以访问导航状态来显示忙碌指示器、禁用字段组等。

```jsx lines=[2,3,6,19-21]
function NewProjectForm() {
  const navigation = useNavigation();
  const busy = navigation.state === "submitting";
  return (
    <Form action="/project/new">
      <fieldset disabled={busy}>
        <label>
          Project title
          <br />
          <input type="text" name="title" />
        </label>

        <label>
          Target Finish Date
          <br />
          <input type="date" name="due" />
        </label>
      </fieldset>
      <button type="submit" disabled={busy}>
        {busy ? "Creating..." : "Create"}
      </button>
    </Form>
  );
}
```

查看：

- [`useNavigation`][usenavigation]

## 乐观 UI

知道发送到 [action][action] 的 [`formData`][formdata] 通常足以跳过忙碌指示器，立即将 UI 渲染到下一个状态，即使你的异步工作仍在进行中。这称为“乐观 UI”。

```jsx
function LikeButton({ tweet }) {
  const fetcher = useFetcher();

  // 如果有 `formData` 则正在提交到 action
  const liked = fetcher.formData
    ? // 检查 formData 来做乐观处理
      fetcher.formData.get("liked") === "yes"
    : // 如果没有提交到 action，使用记录的值
      tweet.liked;

  return (
    <fetcher.Form method="post" action="toggle-liked">
      <button
        type="submit"
        name="liked"
        value={liked ? "yes" : "no"}
      />
    </fetcher.Form>
  );
}
```

（是的，HTML 按钮可以有 `name` 和 `value`）。

虽然使用 [`fetcher`][fetcher] 做乐观 UI 更常见，但你也可以使用普通表单通过 [`navigation.formData`][navigationformdata] 实现相同效果。

## 数据 Fetcher

HTML 表单是变更的模型，但它们有一个主要限制：你一次只能有一个，因为表单提交是一次导航。

大多数 Web 应用需要允许同时发生多个变更，比如记录列表中每个都可以独立删除、标记完成、点赞等。

[Fetchers][fetcher] 允许你与路由 [actions][action] 和 [loaders][loader] 交互而不在浏览器中引起导航，但仍然获得所有常规优势，如错误处理、重新验证、中断处理和竞态条件处理。

想象一个任务列表：

```jsx
function Tasks() {
  const tasks = useLoaderData();
  return tasks.map((task) => (
    <div>
      <p>{task.name}</p>
      <ToggleCompleteButton task={task} />
    </div>
  ));
}
```

每个任务都可以独立于其余任务被标记为完成，拥有自己的待定状态，并不会通过 [fetcher][fetcher] 引起导航：

```jsx
function ToggleCompleteButton({ task }) {
  const fetcher = useFetcher();

  return (
    <fetcher.Form method="post" action="/toggle-complete">
      <fieldset disabled={fetcher.state !== "idle"}>
        <input type="hidden" name="id" value={task.id} />
        <input
          type="hidden"
          name="status"
          value={task.complete ? "incomplete" : "complete"}
        />
        <button type="submit">
          {task.status === "complete"
            ? "Mark Incomplete"
            : "Mark Complete"}
        </button>
      </fieldset>
    </fetcher.Form>
  );
}
```

查看：

- [`useFetcher`][fetcher]

## 竞态条件处理

React Router 会自动取消过时的操作，只提交最新的数据。

任何时候你有异步 UI 就有竞态条件的风险：当一个异步操作在较早的操作之后开始但在它之前完成。结果是用户界面显示了错误的状态。

考虑一个搜索字段，随用户输入更新列表：

```
?q=ry    |---------------|
                         ^ commit wrong state
?q=ryan     |--------|
                     ^ lose correct state
```

即使 `q?=ryan` 的查询发出得更晚，但它完成得更早。如果不正确处理，结果会短暂显示 `?q=ryan` 的正确值，然后切换到 `?q=ry` 的错误结果。节流和防抖是不够的（你仍然可以中断通过的请求）。你需要取消。

如果你使用 React Router 的数据约定，你可以完全自动地避免这个问题。

```
?q=ry    |-----------X
                     ^ cancel wrong state when
                       correct state completes earlier
?q=ryan     |--------|
                     ^ commit correct state
```

React Router 不仅处理像这样的导航竞态条件，还处理很多其他情况，比如为自动完成加载结果或使用 [`fetcher`][fetcher] 执行多个并发变更（以及其自动的并发重新验证）。

## 错误处理

你应用的绝大多数错误都由 React Router 自动处理。它会捕获以下情况中抛出的任何错误：

- 渲染
- 加载数据
- 更新数据

实际上，这几乎覆盖了你应用中的所有错误，除了事件处理程序（`<button onClick>`）或 `useEffect` 中抛出的错误。React Router 应用往往很少有这两者。

当抛出错误时，不会渲染路由的 [`element`][element]，而是渲染 [`errorElement`][errorelement]。

```jsx
<Route
  path="/"
  loader={() => {
    something.that.throws.an.error();
  }}
  // 这个不会被渲染
  element={<HappyPath />}
  // 但这个会
  errorElement={<ErrorBoundary />}
/>
```

如果一个路由没有 `errorElement`，错误会向上冒泡到最近的有 `errorElement` 的父路由：

```jsx
<Route
  path="/"
  element={<HappyPath />}
  errorElement={<ErrorBoundary />}
>
  {/* 这里的错误会向上冒泡到父路由 */}
  <Route path="login" element={<Login />} />
</Route>
```

查看：

- [`<Route errorElement>`][errorelement]
- [`useRouteError`][userouteerror]

## 滚动恢复

React Router 会在导航时模拟浏览器的滚动恢复，在滚动之前等待数据加载。这确保滚动位置恢复到正确的位置。

你还可以通过基于 location 以外的内容（如 url pathname）进行恢复，以及在某些链接（如页面中间的标签页）上阻止滚动来自定义行为。

查看：

- [`<ScrollRestoration>`][scrollrestoration]

## Web 标准 API

React Router 基于 Web 标准 API 构建。[Loaders][loader] 和 [actions][action] 接收标准的 Web Fetch API [`Request`][request] 对象，也可以返回 [`Response`][response] 对象。取消通过 [Abort Signals][signal] 实现，搜索参数通过 [`URLSearchParams`][urlsearchparams] 处理，数据变更通过 [HTML Forms][htmlform] 处理。

当你在 React Router 上变得更好时，你也在 Web 平台上变得更好。

## 搜索参数

<docs-info>TODO:</docs-info>

## Location State

<docs-info>TODO:</docs-info>

[path]: ../route/route#path
[loader]: ../route/loader
[action]: ../route/action
[useparams]: ../hooks/use-params
[usematch]: ../hooks/use-match
[navlink]: ../components/nav-link
[redirect]: ../fetch/redirect
[throwing]: ../route/loader#throwing-in-loaders
[usenavigate]: ../hooks/use-navigate
[useparams]: ../hooks/use-params
[usematch]: ../hooks/use-match
[defer]: ../utils/defer
[await]: ../components/await
[useasyncvalue]: ../hooks/use-async-value
[deferreddata]: ../guides/deferred
[usenavigation]: ../hooks/use-navigation
[request]: https://developer.mozilla.org/en-US/docs/Web/API/Request
[formdata]: https://developer.mozilla.org/en-US/docs/Web/API/FormData
[creatingcontacts]: ../start/tutorial#creating-contacts
[navigationformdata]: ../hooks/use-navigation#navigationformdata
[fetcher]: ../hooks/use-fetcher
[errorelement]: ../route/error-element
[userouteerror]: ../hooks/use-route-error
[element]: ../route/route#element
[scrollrestoration]: ../components/scroll-restoration
[request]: https://developer.mozilla.org/en-US/docs/Web/API/Request
[response]: https://developer.mozilla.org/en-US/docs/Web/API/Response
[signal]: https://developer.mozilla.org/en-US/docs/Web/API/AbortSignal
[urlsearchparams]: https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams
[htmlform]: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form
