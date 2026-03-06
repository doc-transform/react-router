---
title: 常见问题
order: 4
---

# 常见问题

以下是人们通常对 React Router v6 提出的一些问题。你也可能在[示例][examples]中找到你需要的内容。

## withRouter 怎么了？我需要它！

这个问题通常源于你正在使用不支持 hooks 的 React 类组件。在 React Router v6 中，我们完全采用了 hooks 并使用它们来共享路由器的所有内部状态。但这并不意味着你不能使用路由器。假设你可以使用 hooks（React 16.8+），你只需要一个包装器。

```js
import {
  useLocation,
  useNavigate,
  useParams,
} from "react-router-dom";

function withRouter(Component) {
  function ComponentWithRouterProp(props) {
    let location = useLocation();
    let navigate = useNavigate();
    let params = useParams();
    return (
      <Component
        {...props}
        router={{ location, navigate, params }}
      />
    );
  }

  return ComponentWithRouterProp;
}
```

## 为什么 `<Route>` 有 `element` prop 而不是 `render` 或 `component`？

在 React Router v6 中，我们从 v5 的 `<Route component>` 和 `<Route render>` API 切换到了 `<Route element>`。为什么？

首先，我们看到 React 本身在这方面引领潮流，通过 `<Suspense fallback={<Spinner />}>` API。`fallback` prop 接受一个 React **元素**，而不是**组件**。这让你可以轻松地从渲染它的组件传递任意 props 到 `<Spinner>`。

使用元素而不是组件意味着我们不必提供 `passProps` 风格的 API，这样你就可以将所需的 props 传递给元素。例如，在基于组件的 API 中，没有好的方法将 props 传递给 `<Route path=":userId" component={Profile} />` 匹配时渲染的 `<Profile>` 元素。大多数采用这种方式的 React 库最终会有类似 `<Route component={Profile} passProps={{ animate: true }} />` 的 API，或者使用 render prop 或高阶组件。

另外，v5 中 `Route` 的渲染 API 变得相当庞大。当我们开发 v4/5 时，对话大致是这样的：

```js
// Ah, this is nice and simple!
<Route path=":userId" component={Profile} />

// But wait, how do I pass custom props to the <Profile> element??
// Hmm, maybe we can use a render prop in those situations?
<Route
  path=":userId"
  render={routeProps => (
    <Profile routeProps={routeProps} animate={true} />
  )}
/>

// Ok, now we have two ways to render something with a route. :/

// But wait, what if we want to render something when a route
// *doesn't* match the URL, like a Not Found page? Maybe we
// can use another render prop with slightly different semantics?
<Route
  path=":userId"
  children={({ match }) => (
    match ? (
      <Profile match={match} animate={true} />
    ) : (
      <NotFound />
    )
  )}
/>

// What if I want to get access to the route match, or I need
// to redirect deeper in the tree?
function DeepComponent(routeStuff) {
  // got routeStuff, phew!
}
export default withRouter(DeepComponent);

// Well hey, now at least we've covered all our use cases!
// ... *facepalm*
```

API 膨胀的至少部分原因是 React 没有提供任何方法让我们将 `<Route>` 的信息传递给你的路由元素，所以我们不得不发明巧妙的方法来同时传递路由数据**和**你自己的自定义 props 到你的元素：`component`、render props、`passProps` 高阶组件... 直到 **hooks** 出现！

现在，上面的对话变成了这样：

```js
// Ah, nice and simple API. And it's just like the <Suspense> API!
// Nothing more to learn here.
<Route path=":userId" element={<Profile />} />

// But wait, how do I pass custom props to the <Profile>
// element? Oh ya, it's just an element. Easy.
<Route path=":userId" element={<Profile animate={true} />} />

// Ok, but how do I access the router's data, like the URL params
// or the current location?
function Profile({ animate }) {
  let params = useParams();
  let location = useLocation();
}

// But what about components deep in the tree?
function DeepComponent() {
  // oh right, same as anywhere else
  let navigate = useNavigate();
}

// Aaaaaaaaand we're done here.
```

在 v6 中使用 `element` prop 的另一个重要原因是 `<Route children>` 被保留用于嵌套路由。你可以在[入门指南][nested-routes]中阅读更多相关内容。

## 如何在 React Router v6 中添加 No Match (404) 路由？

在 v4 中我们只需省略路由的 path prop。在 v5 中我们会将 404 元素包裹在 Route 中并使用 `path="*"`。在 v6 中使用 `path="*"` 并将 404 元素传入新的 `element` prop 而不是包裹它：

```js
<Route path="*" element={<NoMatch />} />
```

## `<Route>` 不渲染了？我该如何组合？

在 v5 中，`<Route>` 组件只是一个普通组件，就像一个 `if` 语句，当 URL 匹配其路径时渲染。在 v6 中，`<Route>` 元素实际上从不渲染，它只是用于配置。

在 v5 中，由于路由只是组件，当路径为 "/my-route" 时 `MyRoute` 会被渲染。

```tsx filename=v5.js
let App = () => (
  <div>
    <MyRoute />
  </div>
);

let MyRoute = ({ element, ...rest }) => {
  return (
    <Route path="/my-route" children={<p>Hello!</p>} />
  );
};
```

然而在 v6 中，`<Route>` 只用于其 props，所以以下代码永远不会渲染 `<p>Hello!</p>`，因为 `<MyRoute>` 没有 `<Routes>` 能看到的路径：

```tsx bad filename=v6-wrong.js
let App = () => (
  <Routes>
    <MyRoute />
  </Routes>
);

let MyRoute = () => {
  // 永远不会渲染因为 path 在这里下面
  return (
    <Route path="/my-route" children={<p>Hello!</p>} />
  );
};
```

你可以通过以下方式获得相同的行为：

- 只在 `<Routes>` 内渲染 `<Route>` 元素
- 将组合移到 `element` prop 中

```tsx filename=v6.js
let App = () => (
  <div>
    <Routes>
      <Route path="/my-route" element={<MyRoute />} />
    </Routes>
  </div>
);

let MyRoute = () => {
  return <p>Hello!</p>;
};
```

在 `<Routes>` 中静态地提供完整的嵌套路由配置将启用 `v6.x` 中的许多功能，所以我们鼓励你将路由放在一个顶层配置中。如果你真的喜欢独立于其他组件匹配 URL 的组件的想法，你可以创建一个类似 v5 `Route` 行为的组件：

```tsx
function MatchPath({ path, Comp }) {
  let match = useMatch(path);
  return match ? <Comp {...match} /> : null;
}

// 无需在 `<Routes>` 中即可在任何地方匹配
<MatchPath path="/accounts/:id" Comp={Account} />;
```

## 如何在树的深处嵌套路由？

在 v5 中你可以在任何地方渲染 `<Route>` 或 `<Switch>`。你可以继续做同样的事情，但需要使用 `<Routes>`（没有 's' 的 `<Route>` 不会工作）。我们称之为“后代 `<Routes>`”。

在 v5 中可能是这样的

```tsx filename=v5.js
// somewhere up the tree
<Switch>
  <Route path="/users" component={Users} />
</Switch>;

// and now deeper in the tree
function Users() {
  return (
    <div>
      <h1>Users</h1>
      <Switch>
        <Route path="/users/account" component={Account} />
      </Switch>
    </div>
  );
}
```

在 v6 中几乎相同：

- 注意祖先路由中的 `*`，让它即使没有直接子路由也能匹配更深的 URL
- 你不再需要知道完整的子路由路径，现在可以使用相对路由

```tsx filename=v6.js
// somewhere up the tree
<Routes>
  <Route path="/users/*" element={<Users />} />
</Routes>;

// and now deeper in the tree
function Users() {
  return (
    <div>
      <h1>Users</h1>
      <Routes>
        <Route path="account" element={<Account />} />
      </Routes>
    </div>
  );
}
```

如果你在 v5 中有“浮动路由”（未包裹在 `<Switch>` 中），只需将它包裹在 `<Routes>` 中即可。

```tsx
// v5
<Route path="/contact" component={Contact} />

// v6
<Routes>
  <Route path="contact" element={<Contact />} />
</Routes>
```

## 正则表达式路由路径怎么了？

正则表达式路由路径被移除了，原因有两个：

1. 路由中的正则表达式路径对 v6 的路由排名匹配提出了很多问题。如何对一个正则表达式进行排名？

2. 我们能够剥离一个完整的依赖（path-to-regexp），显著减少发送到用户浏览器的包体积。如果加回来，它将占 React Router 页面体积的 1/3！

在查看了大量用例后，我们发现即使没有直接的正则路径支持，我们仍然可以满足这些需求，所以我们做出了显著减小 bundle 大小并避免正则路由排名开放问题的权衡。

大多数正则路由只关注一次一个 URL 段，并做以下两件事之一：

1. 匹配多个静态值
2. 以某种方式验证参数（是数字、不是数字等）

**匹配常见静态值**

我们见过的一个非常常见的路由是匹配多个语言代码的正则表达式：

```tsx filename=v5-lang-route.js
function App() {
  return (
    <Switch>
      <Route path={/(en|es|fr)/} component={Lang} />
    </Switch>
  );
}

function Lang({ params }) {
  let lang = params[0];
  let translations = I81n[lang];
  // ...
}
```

这些实际上都只是静态路径，所以在 v6 中你可以创建三个路由并直接将代码传递给组件。如果你有很多，可以创建一个数组并将其映射为路由以避免重复。

```tsx filename=v6-lang-route.js
function App() {
  return (
    <Routes>
      <Route path="en" element={<Lang lang="en" />} />
      <Route path="es" element={<Lang lang="es" />} />
      <Route path="fr" element={<Lang lang="fr" />} />
    </Routes>
  );
}

function Lang({ lang }) {
  let translations = I81n[lang];
  // ...
}
```

**进行某种参数验证**

另一个常见情况是确保参数是整数。

```tsx filename=v5-userId-route.js
function App() {
  return (
    <Switch>
      <Route path={/users\/(\d+)/} component={User} />
    </Switch>
  );
}

function User({ params }) {
  let id = params[0];
  // ...
}
```

在这种情况下，你必须在匹配组件内部自己处理正则表达式：

```tsx filename=v6-userId-route.js
function App() {
  return (
    <Routes>
      <Route path="/users/:id" element={<ValidateUser />} />
      <Route path="/users/*" element={<NotFound />} />
    </Routes>
  );
}

function ValidateUser() {
  let params = useParams();
  let userId = params.id.match(/\d+/);
  if (!userId) {
    return <NotFound />;
  }
  return <User id={params.userId} />;
}

function User(props) {
  let id = props.id;
  // ...
}
```

在 v5 中如果正则不匹配，`<Switch>` 会继续尝试匹配下一个路由：

```tsx filename=v5-switch.js
function App() {
  return (
    <Switch>
      <Route path={/users\/(\d+)/} component={User} />
      <Route path="/users/new" exact component={NewUser} />
      <Route
        path="/users/inactive"
        exact
        component={InactiveUsers}
      />
      <Route path="/users/*" component={NotFound} />
    </Switch>
  );
}
```

看到这个例子你可能会担心，在 v6 版本中你的其他路由不会在其 URL 上渲染，因为 `:userId` 路由可能会先匹配。但得益于路由排名，事实并非如此。"new" 和 "inactive" 路由将获得更高排名，因此会在各自的 URL 上渲染：

```tsx filename=v6-ranked.js
function App() {
  return (
    <Routes>
      <Route path="/users/:id" element={<ValidateUser />} />
      <Route path="/users/new" element={<NewUser />} />
      <Route
        path="/users/inactive"
        element={<InactiveUsers />}
      />
    </Routes>
  );
}
```

实际上，v5 版本如果你的路由顺序不对，会有各种问题。V6 完全消除了这个问题。

**Remix 用户**

如果你使用 [Remix][remix]，你可以通过将这部分工作移到 loader 中来向浏览器发送正确的 40x 响应。这也减少了发送给用户的浏览器 bundle 大小，因为 loader 只在服务器上运行。

```tsx filename=remix-useLoaderData.js
import { useLoaderData } from "remix";

export async function loader({ params }) {
  if (!params.id.match(/\d+/)) {
    throw new Response("", { status: 400 });
  }

  let user = await fakeDb.user.find({
    where: { id: params.id },
  });
  if (!user) {
    throw new Response("", { status: 404 });
  }

  return user;
}

function User() {
  let user = useLoaderData();
  // ...
}
```

不是渲染你的组件，remix 会渲染最近的 [catch boundary][remix-catchboundary]。

[remix]: https://remix.run
[remix-catchboundary]: https://remix.run/docs/en/v1/api/conventions#catchboundary
[nested-routes]: ./overview#nested-routes
[examples]: https://github.com/remix-run/react-router/tree/dev/examples
