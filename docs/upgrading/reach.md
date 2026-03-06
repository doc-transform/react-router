---
title: 从 @reach/router 迁移
---

# 从 Reach Router 迁移到 React Router v6

<docs-info>本页面仍在完善中。如果发现缺失的内容，请告诉我们，以便我们让迁移尽可能顺畅！</docs-info>

## 介绍

当我们着手构建 React Router v6 时，从 `@reach/router` 用户的角度，我们有以下目标：

- 保持小的 bundle 体积（结果我们做到了比 `@reach/router` 更小）
- 保留 `@reach/router` 的最佳部分（嵌套路由，以及通过排名路径匹配和 `navigate` 简化的 API）
- 更新 API 以符合现代 React 的习惯用法（即 hooks）
- 为并发模式和 Suspense 提供更好的支持
- 停止默认的不够好的焦点管理

如果我们要制作 `@reach/router` v2，它看起来会和 React Router v6 几乎完全一样。所以，`@reach/router` 的下一个版本*就是* React Router v6。换句话说，不会有 `@reach/router` v2，因为它与 React Router v6 是相同的。

`@reach/router` 1.3 和 React Router v6 之间实际上有很多相同的 API：

- 路由进行排名匹配
- 嵌套路由配置保持不变
- `navigate` 具有相同的签名
- `Link` 具有相同的签名
- 1.3 中的所有 hook 都是相同的（或几乎相同）

大多数更改只是一些重命名。如果你碰巧写了一个 codemod，请与我们分享，我们会将它添加到本指南中！

## 升级概述

在本指南中，我们将向你展示如何升级路由代码的每个部分。我们会渐进地进行，这样你可以做一些更改、发布，然后在方便的时候再回来继续迁移。我们还会稍微讨论一下更改的“原因”，看似简单的重命名背后实际上有更深层的原因。

### 第一步：非破坏性更新

我们强烈建议你在迁移到 React Router v6 之前先对代码进行以下更新。这些更改不必一次性在整个应用中完成，你可以简单地更新一行、提交、发布。这样做会大大减少你在处理 React Router v6 的破坏性更改时的工作量。

1. 升级到 React v16.8 或更高版本
2. 升级到 `@reach/router` v1.3
3. 更新路由组件以从 hook 访问数据
4. 在应用顶层添加 `<LocationProvider/>`

### 第二步：破坏性更新

以下更改需要在整个应用中一次性完成。

<!-- If it is a significant burden, we have copy/paste wrapper components and hooks in each section that you can import instead of updating all of your application code at once (TODO). -->

1. 升级到 React Router v6
2. 将所有 `<Router>` 元素更新为 `<Routes>`
3. 将 `<RouteElement default/>` 改为 `<RouteElement path="*" />`
4. 修复 `<Redirect />`
5. 使用 hook 实现 `<Link getProps />`
6. 更新 `useMatch`，参数在 `match.params` 上
7. 将 `ServerLocation` 改为 `StaticRouter`

## 非破坏性更新

### 升级到 React v16.8

React Router v6 大量使用 [React hooks](https://reactjs.org/docs/hooks-intro.html)，因此你需要先升级到 React 16.8 或更高版本，然后再尝试升级到 React Router v6。

升级到 React 16.8 后，你应该部署你的应用。然后你可以之后再回来继续。

### 升级到 `@reach/router` v1.3.3

你应该可以简单地安装 v1.3.3 然后部署你的应用。

```sh
npm install @reach/router@latest
```

### 更新路由组件以使用 hook

你可以一次一个路由组件地操作，提交并部署。不需要一次性更新整个应用。

在 `@reach/router` v1.3 中，我们添加了 hook 来访问路由数据，为 React Router v6 做准备。如果你先做这一步，你在升级到 React Router v6 时就会少很多工作。

```jsx
// @reach/router v1.2
<Router>
  <User path="users/:userId/grades/:assignmentId" />
</Router>;

function User(props) {
  let {
    // 路由参数从 props 中访问
    userId,
    assignmentId,

    // location 和 navigate 也是
    location,
    navigate,
  } = props;

  // ...
}

// @reach/router v1.3 和 React Router v6
import {
  useParams,
  useLocation,
  useNavigate,
} from "@reach/router";

function User() {
  // 现在一切都从特定的 hook 获取
  let { userId, assignmentId } = useParams();
  let location = useLocation();
  let navigate = useNavigate();
  // ...
}
```

#### 原因说明

所有这些数据已经存在于 context 中，但从那里访问它们对应用代码来说很不方便，所以我们将它们转储到了 props 中。Hook 使得从 context 访问数据变得简单，所以我们不再需要用路由信息污染你的 props。

不污染 props 也对 TypeScript 有一定帮助，还能避免你在查看组件时疑惑某个 prop 从哪里来。如果你使用来自路由器的数据，现在完全清晰。

另外，随着页面增长，你自然会将其拆分为多个组件，最终将数据“prop 钻传”到整个树下方。现在你可以在树的任何地方访问路由数据。这不仅更方便，还使得创建以路由为中心的可组合抽象成为可能。如果一个自定义 hook 需要 location，它现在可以简单地通过 `useLocation()` 等来请求。

### 在应用顶层添加 LocationProvider

虽然 `@reach/router` 不需要在应用树顶层放置 location provider，但 React Router v6 需要，所以不如现在就做好准备。

```jsx
// before
ReactDOM.render(<App />, el);

// after
import { LocationProvider } from "@reach/router";

ReactDOM.render(
  <LocationProvider>
    <App />
  </LocationProvider>,
  el,
);
```

#### 原因说明：

`@reach/router` 使用一个全局的、默认的 history 实例，它在模块中有副作用，这阫止了无论你是否使用全局实例都无法对模块进行 tree-shaking。此外，React Router 提供了 `@reach/router` 没有的其他 history 类型（如 hash history），因此它始终需要一个顶层 location provider（在 React Router 中是 `<BrowserRouter/>` 及其同类组件）。

另外，各种模块如 `Router`、`Link` 和 `useLocation` 在 `<LocationProvider/>` 外部渲染时会设置它们自己的 URL 监听器。这通常不是问题，但积少成多。在顶层放置一个 `<LocationProvider />` 允许应用拥有单一的 URL 监听器。

## 破坏性更新

接下来这组更新需要一次性完成。幸运的是，大部分只是简单的重命名。

不过你可以使用一个技巧，在迁移过程中同时使用两个路由器，但你绝对不应该在这种状态下发布你的应用，因为它们不可互操作。一个路由器的链接对另一个无效。然而，能够做一个更改然后刷新页面来确认你正确完成了那一步还是很不错的。

### 安装 React Router v6

```sh
npm install react-router@6 react-router-dom@6
```

### 将 `LocationProvider` 更新为 `BrowserRouter`

```jsx
// @reach/router
import { LocationProvider } from "@reach/router";

ReactDOM.render(
  <LocationProvider>
    <App />
  </LocationProvider>,
  el,
);

// React Router v6
import { BrowserRouter } from "react-router-dom";

ReactDOM.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
  el,
);
```

### 将 `Router` 更新为 `Routes`

你可能有多个，但通常在应用顶层附近只有一个。如果你有多个，请对每个都执行此操作。

```jsx
// @reach/router
import { Router } from "@reach/router";

<Router>
  <Home path="/" />
  {/* ... */}
</Router>;

// React Router v6
import { Routes, Route } from "react-router-dom";

<Routes>
  <Route path="/" element={<Home />} />
  {/* ... */}
</Routes>;
```

### 更新 `default` 路由属性

`default` 属性告诉 `@reach/router` 如果没有其他路由匹配则使用该路由。在 React Router v6 中，你可以用通配符路径来实现这个行为。

```jsx
// @reach/router
<Router>
  <Home path="/" />
  <NotFound default />
</Router>

// React Router v6
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="*" element={<NotFound />} />
</Routes>
```

### `<Redirect/>`、`redirectTo`、`isRedirect`

哥们... 系好安全带，这个比较复杂。请把你的番茄留着做自制珛格丽特披萨，而不是扔给我们。

我们已经从 React Router 中移除了重定向的能力。这意味着没有 `<Redirect/>`、`redirectTo` 或 `isRedirect`，也没有替代 API。请继续阅读 😅

不要将重定向与用户与应用交互时的导航混淆。响应用户交互的导航仍然被支持。当我们说重定向时，我们说的是在匹配时进行重定向：

```jsx
<Router>
  <Home path="/" />
  <Users path="/events" />
  <Redirect from="/dashboard" to="/events" />
</Router>
```

`@reach/router` 中重定向的工作方式有点实验性质。它“抛出”重定向并通过 `componentDidCatch` 捕获。这很酷，因为它会导致整个渲染树停止，然后用新的 location 重新开始。多年前我们首次发布这个项目时，与 React 团队的讨论让我们尝试了这种方式。

在碰到一些问题后（比如应用级别的 `componentDidCatch` 需要重新抛出重定向），我们决定在 React Router v6 中不再这样做了。

但我们更进一步，得出结论：重定向甚至不是 React Router 的职责。你的动态 Web 服务器或静态文件服务器应该处理这个问题，并发送适当的响应状态码，如 301 或 302。

在 React Router 中拥有匹配时重定向的能力，最好的情况是需要你在两个地方配置重定向（服务器和路由），最坏的情况是鼓励人们只在 React Router 中做——这根本不会发送状态码。

我们经常使用 Firebase 托管，所以举个例子，以下是我们如何更新其中一个应用：

```jsx
// @reach/router
<Router>
  <Home path="/" />
  <Users path="/events" />
  <Redirect from="/dashboard" to="/events" />
</Router>
```

```jsx
// React Router v6
// firebase.json config file
{
  // ...
  "hosting": {
    "redirects": [
      {
        "source": "/dashboard",
        "destination": "/events",
        "type": 301
      }
    ]
  }
}
```

无论我们是用 serverless 函数进行服务端渲染，还是仅作为静态文件服务器使用，这都可以工作。所有 Web 托管服务都提供了配置此功能的方法。

#### 那没有更新的链接点击时怎么办？

如果你的应用中仍然有一个 `<Link to="/events" />`，用户点击它时，服务器不会参与，因为你使用的是客户端路由器。你需要更加勤奢地更新你的链接 😬。

或者，如果你想允许过时的链接，_并且你意识到需要在客户端和服务器上都配置重定向_，那就复制粘贴我们即将发布但后来删除了的 `Redirect` 组件：

```jsx
import { useEffect } from "react";
import { useNavigate } from "react-router-dom";

function Redirect({ to }) {
  let navigate = useNavigate();
  useEffect(() => {
    navigate(to);
  });
  return null;
}

// 用法
<Routes>
  <Route path="/" element={<Home />} />
  <Route path="/events" element={<Users />} />
  <Route
    path="/dashboard"
    element={<Redirect to="/events" />}
  />
</Routes>;
```

#### 原因说明

我们认为通过不提供任何重定向 API，人们更有可能正确地配置它们。多年来我们一直在意外地鼓励不好的实践，现在想停下来 🙈。

### `<Link getProps />`

这个 prop getter 对于将链接样式化为“活动状态”很有用。判断链接是否活动有些主观。有时你希望它在 URL 完全匹配时活动，有时希望它在部分匹配时活动，还有更多涉及 search params 和 location state 的边缘情况。

```jsx
// @reach/router
function SomeCustomLink() {
  return (
    <Link
      to="/some/where/cool"
      getProps={(obj) => {
        let {
          isCurrent,
          isPartiallyCurrent,
          href,
          location,
        } = obj;
        // do what you will
      }}
    />
  );
}

// React Router
import { useLocation, useMatch } from "react-router-dom";

function SomeCustomLink() {
  let to = "/some/where/cool";
  let match = useMatch(to);
  let { isExact } = useMatch(to);
  let location = useLocation();
  return <Link to={to} />;
}
```

让我们看一些不那么通用的示例。

```jsx
// 一个当 URL 与链接的 href 完全匹配时活动的自定义导航链接

// @reach/router
function ExactNavLink(props) {
  const isActive = ({ isCurrent }) => {
    return isCurrent ? { className: "active" } : {};
  };
  return <Link getProps={isActive} {...props} />;
}

// React Router v6
function ExactNavLink(props) {
  return (
    <Link
      // 如果你只需要活动状态用于样式而不需要
      // 覆盖默认的 isActive 状态，我们将它作为
      // 命名参数提供在可以传递给
      // `className` 或 `style` props 的函数中
      className={({ isActive }) =>
        isActive ? "active" : ""
      }
      {...props}
    />
  );
}

// 一个当自身或更深层路由为当前路由时活动的链接

// @reach/router
function PartialNavLink(props) {
  const isPartiallyActive = ({ isPartiallyCurrent }) => {
    return isPartiallyCurrent
      ? { className: "active" }
      : {};
  };
  return <Link getProps={isPartiallyActive} {...props} />;
}

// React Router v6
function PartialNavLink(props) {
  // 添加通配符以匹配更深层的 URL
  let match = useMatch(props.to + "/*");
  return (
    <Link className={match ? "active" : ""} {...props} />
  );
}
```

#### 原因说明

“Prop getters”很笨拙，几乎总是可以用 hook 替代。这也允许你使用其他 hook，像 `useLocation`，来做更多自定义的事情，比如用 search string 让链接活动：

```jsx
function RecentPostsLink(props) {
  let match = useMatch("/posts");
  let location = useLocation();
  let isActive =
    match && location.search === "?view=recent";
  return (
    <Link className={isActive ? "active" : ""}>Recent</Link>
  );
}
```

### `useMatch`

`useMatch` 的签名在 React Router v6 中略有不同。

```jsx
// @reach/router
let {
  uri,
  path,

  // 参数从对象中合并了 uri 和 path
  eventId,
} = useMatch("/events/:eventId");

// React Router v6
let {
  url,
  path,

  // 参数有自己的键
  params: { eventId },
} = useMatch("/events/:eventId");
```

另外注意从 `uri` 到 `url` 的更改。

#### 原因说明

将参数与 URL 和 path 分开只是感觉更整洁。

另外，没人知道 URL 和 URI 的区别，所以我们不想引发一堆学院派的争论。React Router 一直叫它 URL，而且有更多的生产应用，所以我们用了 URL 而不是 URI。

### `<Match />`

React Router v6 中没有 `<Match/>` 组件。它使用 render props 来组合行为，但我们现在有 hooks 了。

如果你喜欢它，或者只是不想更新你的代码，很容易向后移植：

```jsx
function Match({ path, children }) {
  let match = useMatch(path);
  let location = useLocation();
  let navigate = useNavigate();
  return children({ match, location, navigate });
}
```

#### 原因说明

Render props 现在有点难看（噢！），因为我们有 hooks 了。

### `<ServerLocation />`

这里真的只是简单重命名：

```jsx
// @reach/router
import { ServerLocation } from "@reach/router";

createServer((req, res) => {
  let markup = ReactDOMServer.renderToString(
    <ServerLocation url={req.url}>
      <App />
    </ServerLocation>,
  );
  req.send(markup);
});

// React Router v6
// note the import path from react-router-dom/server!
import { StaticRouter } from "react-router-dom/server";

createServer((req, res) => {
  let markup = ReactDOMServer.renderToString(
    <StaticRouter location={req.url}>
      <App />
    </StaticRouter>,
  );
  req.send(markup);
});
```

## 反馈！

请告诉我们本指南是否对你有帮助：

_提交 Pull Request_：请添加任何我们遗漏的但你需要的迁移内容。

_常规反馈_：Twitter 上的 [@remix_run](https://twitter.com/remix_run)，或发送邮件到 [hello@remix.run](mailto:hello@remix.run)。

谢谢！
