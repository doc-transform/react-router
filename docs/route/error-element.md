---
title: errorElement
new: true
---

# `errorElement`

当 [loader][loader]、[action][action] 或组件渲染中抛出异常时，将渲染错误路径（`<Route errorElement>`）而不是正常的渲染路径（`<Route element>`），并且错误可以通过 [`useRouteError`][userouteerror] 获取。

<docs-info>如果你不想指定 React 元素（即 `errorElement={<MyErrorBoundary />}`），你可以改为指定一个 `ErrorBoundary` 组件（即 `ErrorBoundary={MyErrorBoundary}`），React Router 将在内部为你调用 `createElement`。</docs-info>

<docs-warning>此功能仅在使用数据路由器时有效，参见[选择路由器][pickingarouter]</docs-warning>

```tsx
<Route
  path="/invoices/:id"
  // 如果这里抛出异常
  loader={loadInvoice}
  // 或这里
  action={updateInvoice}
  // 或这里
  element={<Invoice />}
  // 这个将替代 `element` 渲染
  errorElement={<ErrorBoundary />}
/>;

function Invoice() {
  return <div>Happy {path}</div>;
}

function ErrorBoundary() {
  let error = useRouteError();
  console.error(error);
  // Uncaught ReferenceError: path is not defined
  return <div>Dang!</div>;
}
```

## 冒泡

当路由没有 `errorElement` 时，错误会沿着父路由冒泡。这让你可以根据需要进行粗粒度或细粒度的处理。

在路由树的顶部放置一个 `errorElement`，在一个地方处理应用中几乎所有的错误。或者，在所有路由上都放置它们，让没有错误的应用部分继续正常渲染。这给用户提供了更多从错误中恢复的选项，而不是强制刷新并祈祷 🤞。

### 默认错误元素

<docs-warning>我们建议在发布应用到生产环境之前，*始终*至少提供一个根级别的 `errorElement`，因为默认 `errorElement` 的 UI 很丑陋，不适合终端用户查看。</docs-warning>

如果你没有在路由树中提供 `errorElement` 来处理给定的错误，错误将冒泡并由默认的 `errorElement` 处理，它会打印错误信息和堆栈跟踪。一些人质疑为什么堆栈跟踪会在生产构建中显示。通常，出于安全原因，你不想在生产站点上暴露堆栈跟踪。然而，这更适用于服务端错误（Remix 确实会从服务端 loader/action 响应中去除堆栈跟踪）。对于客户端的 `react-router-dom` 应用，代码本来就已经在浏览器中可用，因此任何隐藏都只是表面上的安全。此外，我们仍然希望在控制台中暴露错误，所以从 UI 显示中移除它仍然不会隐藏任何关于堆栈跟踪的信息。不在 UI 中显示*也*不在控制台中记录，意味着应用开发者对生产环境的 bug *完全*没有任何信息，这会带来一系列问题。因此，我们再次建议你在部署站点到生产环境之前始终添加一个根级别的 `errorElement`！

## 手动抛出

虽然 `errorElement` 处理的是意外错误，但它也可以用于处理你预期的异常。

特别是在 loader 和 action 中，当你处理不受控制的外部数据时，你不能总是确定数据存在、服务可用或用户有访问权限。在这些情况下，你可以 `throw` 自己的异常。

以下是 [loader][loader] 中的"未找到"案例：

```tsx [4,7-9]
<Route
  path="/properties/:id"
  element={<PropertyForSale />}
  errorElement={<PropertyError />}
  loader={async ({ params }) => {
    const res = await fetch(`/api/properties/${params.id}`);
    if (res.status === 404) {
      throw new Response("Not Found", { status: 404 });
    }
    const home = await res.json();
    const descriptionHtml = parseMarkdown(
      data.descriptionMarkdown,
    );
    return { home, descriptionHtml };
  }}
/>
```

一旦你知道无法用正在加载的数据渲染路由，就可以抛出来跳出调用栈。你不必担心 loader 中剩余的工作（比如解析用户的 markdown 简介），因为数据不存在。直接抛出并退出即可。

这也意味着你不必在路由组件中担心大量的错误分支代码。如果你在 loader 或 action 中抛出，它根本不会尝试渲染，因为你的 `errorElement` 将替代渲染。

你可以从 loader 或 action 中抛出任何东西，就像你可以返回任何东西一样：Response（如前面的示例）、Error 或普通对象。

## 抛出 Response

虽然你可以抛出任何东西，它都会通过 [`useRouteError`][userouteerror] 返回给你，但如果你抛出一个 [Response][response]，React Router 会在返回给组件之前自动解析响应数据。

此外，[`isRouteErrorResponse`][isrouteerrorresponse] 允许你在边界中检查这种特定类型。配合 [`json`][json]，你可以轻松地抛出带有数据的 Response，并在边界中渲染不同的情况：

```tsx
import { json } from "react-router-dom";

function loader() {
  const stillWorksHere = await userStillWorksHere();
  if (!stillWorksHere) {
    throw json(
      {
        sorry: "You have been fired.",
        hrEmail: "hr@bigco.com",
      },
      { status: 401 },
    );
  }
}

function ErrorBoundary() {
  const error = useRouteError();

  if (isRouteErrorResponse(error) && error.status === 401) {
    // 响应的 json 会自动解析到 `error.data`，
    // 你也可以访问 status
    return (
      <div>
        <h1>{error.status}</h1>
        <h2>{error.data.sorry}</h2>
        <p>
          Go ahead and email {error.data.hrEmail} if you
          feel like this is a mistake.
        </p>
      </div>
    );
  }

  // 重新抛出，让父错误边界处理
  // 当它不是此路由的特殊情况时
  throw error;
}
```

这使得创建一个通用的错误边界成为可能，通常放在根路由上，处理多种情况：

```tsx
function RootBoundary() {
  const error = useRouteError();

  if (isRouteErrorResponse(error)) {
    if (error.status === 404) {
      return <div>此页面不存在！</div>;
    }

    if (error.status === 401) {
      return <div>你没有权限查看此内容</div>;
    }

    if (error.status === 503) {
      return <div>看起来我们的 API 挂了</div>;
    }

    if (error.status === 418) {
      return <div>🫖</div>;
    }
  }

  return <div>出了点问题</div>;
}
```

## 抽象化

当你知道无法继续沿着当前的数据加载路径走下去时抛出异常的模式，使得正确处理异常情况变得非常简单。

假设有一个获取用户 Web Token 用于授权请求的函数，大致如下：

```tsx
async function getUserToken() {
  const token = await getTokenFromWebWorker();
  if (!token) {
    throw new Response("", { status: 401 });
  }
  return token;
}
```

无论哪个 loader 或 action 使用该函数，它都会停止执行当前调用栈中的代码，将应用转到错误路径。

现在让我们添加一个获取项目的函数：

```tsx
function fetchProject(id) {
  const token = await getUserToken();
  const response = await fetch(`/projects/${id}`, {
    headers: { Authorization: `Bearer ${token}` },
  });

  if (response.status === 404) {
    throw new Response("Not Found", { status: 404 });
  }

  // fetch 失败了
  if (!response.ok) {
    throw new Error("Could not fetch project");
  }
}
```

多亏了 `getUserToken`，这段代码可以假设它能获得一个 token。如果没有 token，将渲染错误路径。然后如果项目不存在，无论哪个 loader 调用此函数，它都会抛出 404 到 `errorElement`。最后，如果 fetch 完全失败，它将发送一个错误。

任何时候你意识到"我没有我需要的东西"，你可以简单地 `throw`，知道你仍然在为终端用户渲染有用的内容。

让我们把它整合到一个路由中：

```tsx
<Route
  path="/"
  element={<Root />}
  errorElement={<RootBoundary />}
>
  <Route
    path="projects/:projectId"
    loader={({ params }) => fetchProject(params.projectId)}
    element={<Project />}
  />
</Route>
```

项目路由完全不需要考虑错误。在 loader 工具函数如 `fetchProject` 和 `getUserToken` 会在出错时抛出异常，以及 `RootBoundary` 处理所有情况之间，项目路由可以严格专注于正常路径。

[loader]: ./loader
[action]: ./action
[userouteerror]: ../hooks/use-route-error
[pickingarouter]: ../routers/picking-a-router
[response]: https://developer.mozilla.org/en-US/docs/Web/API/Response
[isrouteerrorresponse]: ../utils/is-route-error-response
[json]: ../fetch/json
[createbrowserrouter]: ../routers/create-browser-router
