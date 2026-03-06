---
title: 核心概念
order: 5
---

# 核心概念

<docs-warning>本文档需要为 6.4 数据 API 更新</docs-warning>

<docs-warning>本文档是对 React Router 中实现的路由核心概念的深入探讨。它相当长，如果你在找更实用的指南，请查看我们的[快速入门教程][tutorial]。</docs-warning>

你可能想知道 React Router 究竟做什么。它如何帮助你构建应用？究竟什么是**路由器**？

如果你有过这些疑问，或者你只是想深入了解路由的基本原理，那你来对地方了。本文档包含了 React Router 中实现的所有路由核心概念的详细解释。

请不要被本文档压到！在日常使用中，React Router 相当简单。你不需要这么深入就能使用它。

React Router 不仅仅是将 URL 与函数或组件匹配：它是关于构建一个映射到 URL 的完整用户界面，所以它可能比你习惯的包含更多概念。我们将详细介绍 React Router 的三个主要职责：

1. 订阅和操作[历史栈](#history-stack)
2. 将 [URL](#url) 与你的[路由](#route-config)匹配
3. 从[路由匹配](#matches)中渲染嵌套 UI

## 定义

但首先，一些定义！关于后端和前端框架的路由有很多不同的想法。有时一个词在一个上下文中的含义可能与另一个不同。

以下是我们在谈论 React Router 时经常使用的一些词。本指南的其余部分将更详细地介绍每一个。

- <a id="url">**URL**</a> - 地址栏中的 URL。很多人交替使用“URL”和“路由”，但在 React Router 中这不是路由，它只是一个 URL。

- <a id="location">**Location**</a> - 这是一个 React Router 特定的对象，基于浏览器内置的 `window.location` 对象。它表示“用户在哪里”。它主要是 URL 的对象表示，但比 URL 有更多内容。

- <a id="location-state">**Location State**</a> - 一个与 [location](#location) 一起持久化的值，不会编码在 [URL](#url) 中。很像 hash 或 search params（编码在 URL 中的数据），但不可见地存储在浏览器内存中。

- <a id="history-stack">**历史栈**</a> - 当用户导航时，浏览器会在栈中跟踪每个 [location](#location)。如果你在浏览器中点击并按住后退按钮，你可以看到浏览器的历史栈。

- <a id="csr">**客户端路由 (CSR)**</a> - 纯 HTML 文档可以链接到其他文档，浏览器自己处理[历史栈](#history-stack)。客户端路由使开发者能够操作浏览器历史栈而无需向服务器发起文档请求。

- <a id="history">**History**</a> - 一个对象，允许 React Router 订阅 [URL](#url) 的变化，并提供以编程方式操作浏览器[历史栈](#history-stack)的 API。

- <a id="history-action">**History Action**</a> - `POP`、`PUSH` 或 `REPLACE` 之一。用户可能因为这三个原因之一到达一个 [URL](#url)。当一个新条目被添加到历史栈时是 push（通常是链接点击或程序员强制导航）。replace 类似，只是它替换栈上的当前条目而不是推入新的。最后，当用户点击浏览器的后退或前进按钮时会发生 pop。

- <a id="segment">**段**</a> - [URL](#url) 或[路径模式](#path-pattern)中 `/` 字符之间的部分。例如，"/users/123" 有两个段。

- <a id="path-pattern">**路径模式**</a> - 这些看起来像 URL，但可以有用于将 URL 与路由匹配的特殊字符，如**动态段**（`"/users/:userId"`）或**通配段**（`"/docs/*"`）。它们不是 URL，而是 React Router 会匹配的模式。

- <a id="dynamic-segment">**动态段**</a> - 路径模式中动态的段，意味着它可以匹配段中的任何值。例如模式 `/users/:userId` 将匹配像 `/users/123` 这样的 URL。

- <a id="url-params">**URL 参数**</a> - 从与[动态段](#dynamic-segment)匹配的 URL 中解析出的值。

- <a id="router">**路由器**</a> - 有状态的顶层组件，使所有其他组件和 hooks 工作。

- <a id="route-config">**路由配置**</a> - 一棵**路由对象**树，将与当前 location 进行排名和匹配（带嵌套），以创建**路由匹配**的分支。

- <a id="route">**路由**</a> - 一个对象或 Route 元素，通常的形状是 `{ path, element }` 或 `<Route path element>`。`path` 是一个路径模式。当路径模式与当前 URL 匹配时，element 将被渲染。

- <a id="route-element">**Route 元素**</a> - 或 `<Route>`。这个元素的 props 被 `<Routes>` 读取以创建[路由](#route)，但其他方面不做任何事情。

- <a id="nested-routes">**嵌套路由**</a> - 因为路由可以有子路由，而每个路由通过[段](#segment)定义 [URL](#url) 的一部分，单个 URL 可以匹配树中嵌套“分支”中的多个路由。这通过 [outlet](#outlet)、[相对链接](#relative-links)等实现自动布局嵌套。

- <a id="relative-links">**相对链接**</a> - 不以 `/` 开头的链接将继承它们渲染所在的最近路由。这使得链接到更深层 URL 变得容易，无需知道和构建完整路径。

- <a id="match">**匹配**</a> - 当路由与 URL 匹配时保存信息的对象，如匹配的 [URL 参数](#url-params)和路径名。

- <a id="matches">**匹配集**</a> - 与当前 [location](#location) 匹配的路由数组（或[路由配置](#route-config)的分支）。这个结构启用了[嵌套路由](#nested-routes)。

- <a id="parent-route">**父路由**</a> - 有子路由的路由。

- <a id="outlet">**Outlet**</a> - 一个组件，渲染[匹配集](#match)中的下一个匹配。

- <a id="index-route">**索引路由**</a> - 没有路径的子路由，在父路由的 [URL](#url) 处渲染在父路由的 [outlet](#outlet) 中。

- <a id="layout-route">**布局路由**</a> - 没有路径的**父路由**，专门用于在特定布局内分组子路由。

## 历史与位置

在 React Router 可以做任何事情之前，它必须能够订阅浏览器[历史栈](#history-stack)的变化。

浏览器在用户导航时维护自己的历史栈。这就是后退和前进按钮能工作的原因。在传统网站（没有 JavaScript 的 HTML 文档）中，每次用户点击链接、提交表单或点击后退和前进按钮时，浏览器都会向服务器发起请求。

例如，考虑用户：

1. 点击链接到 `/dashboard`
2. 点击链接到 `/accounts`
3. 点击链接到 `/customers/123`
4. 点击后退按钮
5. 点击链接到 `/dashboard`

历史栈将如下变化，其中**粗体**条目表示当前 [URL](#url)：

1. **`/dashboard`**
2. `/dashboard`, **`/accounts`**
3. `/dashboard`, `/accounts`, **`/customers/123`**
4. `/dashboard`, **`/accounts`**, `/customers/123`
5. `/dashboard`, `/accounts`, **`/dashboard`**

### History 对象

通过**客户端路由**，开发者可以以编程方式操作浏览器[历史栈](#history-stack)。例如，我们可以编写这样的代码来更改 [URL](#url)，而不会有浏览器向服务器发起请求的默认行为：

```jsx
<a
  href="/contact"
  onClick={(event) => {
    // 阻止浏览器更改 URL 和请求新文档
    event.preventDefault();
    // 将一个条目推入浏览器历史栈并更改 URL
    window.history.pushState({}, undefined, "/contact");
  }}
/>
```

<docs-warning>仅为说明用途，不要在 React Router 中直接使用 `window.history.pushState`</docs-warning>

这段代码更改了 [URL](#url) 但对 UI 没有任何作用。我们需要编写更多代码来更改某处的某些状态，以使 UI 变为联系人页面。问题是，浏览器没有给我们提供“监听 URL”并订阅这样变化的方法。

实际上，这并不完全对。我们可以通过 [pop](#history-actions) 事件监听 URL 的变化：

```jsx
window.addEventListener("popstate", () => {
  // URL changed!
});
```

但这只在用户点击后退或前进按钮时触发。当程序员调用 `window.history.pushState` 或 `window.history.replaceState` 时没有事件。

这就是 React Router 特定的 `history` 对象发挥作用的地方。它提供了一种“监听 [URL](#url)”变化的方法，无论 [history action](#history-actions) 是 **push**、**pop** 还是 **replace**。

```js
let history = createBrowserHistory();
history.listen(({ location, action }) => {
  // 每当有新 location 时调用
  // action 是 POP、PUSH 或 REPLACE
});
```

应用不需要设置自己的 history 对象——这是 `<Router>` 的工作。它设置其中一个对象，订阅[历史栈](#history-stack)的变化，并在 [URL](#url) 变化时更新其状态。这会导致应用重新渲染并显示正确的 UI。它唯一需要放在状态上的是一个 `location`，其他一切都从这个单一对象工作。

### Location

浏览器在 `window.location` 上有一个 location 对象。它告诉你关于 [URL](#url) 的信息，还有一些更改它的方法：

```js
window.location.pathname; // /getting-started/concepts/
window.location.hash; // #location
window.location.reload(); // force a refresh w/ the server
// and a lot more
```

<docs-warning>仅为说明。你通常不会在 React Router 应用中使用 `window.location`</docs-warning>

不是使用 `window.location`，React Router 有一个仿照 `window.location` 但更简单的 [location](#location) 概念。它看起来像这样：

```js
{
  pathname: "/bbq/pig-pickins",
  search: "?campaign=instagram",
  hash: "#menu",
  state: null,
  key: "aefz24ie"
}
```

前三个 `{ pathname, search, hash }` 与 `window.location` 完全相同。如果你将三个加起来，就会得到用户在浏览器中看到的 [URL](#url)：

```js
location.pathname + location.search + location.hash;
// /bbq/pig-pickins?campaign=instagram#menu
```

后两个 `{ state, key }` 是 React Router 特有的。

**Location Pathname**

这是 [URL](#url) 中原点之后的部分，所以对于 `https://example.com/teams/hotspurs`，pathname 是 `/teams/hotspurs`。这是 location 中路由唯一匹配的部分。

**Location Search**

人们对 [URL](#url) 的这部分使用很多不同的术语：

- location search
- search params
- URL search params
- 查询字符串

在 React Router 中我们称之为 "location search"。然而，location search 是 [`URLSearchParams`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams) 的序列化版本。所以有时我们也可能称它为 "URL search params"。

```js
// given a location like this:
let location = {
  pathname: "/bbq/pig-pickins",
  search: "?campaign=instagram&popular=true",
  hash: "",
  state: null,
  key: "aefz24ie",
};

// we can turn the location.search into URLSearchParams
let params = new URLSearchParams(location.search);
params.get("campaign"); // "instagram"
params.get("popular"); // "true"
params.toString(); // "campaign=instagram&popular=true",
```

简单一点，序列化的字符串版本称为 "search"，解析后的版本称为 "search params"，但在不需要精确时可以交替使用。

**Location Hash**

URL 中的 hash 表示*当前页面上的*滚动位置。在 `window.history.pushState` API 引入之前，Web 开发者专门使用 [URL](#url) 的 hash 部分进行客户端路由，它是我们唯一可以在不向服务器发起新请求的情况下操作的部分。然而，今天我们可以将它用于其设计目的。

**Location State**

你可能想知道为什么 `window.history.pushState()` API 被称为 "push state"。State？我们不是只是在更改 [URL](#url) 吗？不应该是 `history.push` 吗？好吧，我们不在 API 设计的现场，所以不确定为什么 "state" 是焦点，但它确实是浏览器的一个很酷的功能。

浏览器允许我们通过向 `pushState` 传递一个值来保存关于导航的信息。当用户点击后退时，`history.state` 上的值会变为之前 "pushed" 的任何内容。

```js
window.history.pushState("look ma!", undefined, "/contact");
window.history.state; // "look ma!"
// user clicks back
window.history.state; // undefined
// user clicks forward
window.history.state; // "look ma!"
```

<docs-warning>仅为说明。你不会在 React Router 应用中直接读取 `history.state`</docs-warning>

React Router 利用了这个浏览器功能，对其进行了一定的抽象，并将值暴露在 `location` 而不是 `history` 上。

你可以将 `location.state` 想象成像 `location.hash` 或 `location.search` 一样，只是它不是把值放在 [URL](#url) 中，而是隐藏的——像是 URL 中只有程序员知道的超级秘密部分。

location state 的几个很好的用例：

- 告诉下一页用户从哪里来并分支 UI。最流行的实现是，如果用户点击了网格视图中的项目，则在模态框中显示记录，但如果他们直接访问 URL，则在自己的布局中显示记录（pinterest、旧版 instagram）。
- 从列表发送部分记录到下一个屏幕，这样它可以立即渲染部分数据，然后再获取剩余数据。

你可以通过两种方式设置 location state：在 `<Link>` 或 `navigate` 上：

```jsx
<Link to="/pins/123" state={{ fromDashboard: true }} />;

let navigate = useNavigate();
navigate("/users/123", { state: partialUser });
```

在下一页你可以通过 `useLocation` 访问它：

```jsx
let location = useLocation();
location.state;
```

<docs-info>Location state 的值会被序列化，所以像 `new Date()` 这样的东西会被转换为字符串。</docs-info>

**Location Key**

每个 location 都会获得一个唯一的 key。这对于高级用例很有用，如基于 location 的滚动管理、客户端数据缓存等。因为每个新 location 都会获得一个唯一的 key，你可以构建将信息存储在普通对象、`new Map()` 甚至 `locationStorage` 中的抽象。

例如，一个非常基本的客户端数据缓存可以通过 location key（和 fetch [URL](#url)）存储值，并在用户点击后退时跳过数据获取：

```jsx
let cache = new Map();

function useFakeFetch(URL) {
  let location = useLocation();
  let cacheKey = location.key + URL;
  let cached = cache.get(cacheKey);

  let [data, setData] = useState(() => {
    // 从缓存初始化
    return cached || null;
  });

  let [state, setState] = useState(() => {
    // 如果已缓存则避免 fetch
    return cached ? "done" : "loading";
  });

  useEffect(() => {
    if (state === "loading") {
      let controller = new AbortController();
      fetch(URL, { signal: controller.signal })
        .then((res) => res.json())
        .then((data) => {
          if (controller.signal.aborted) return;
          // 设置缓存
          cache.set(cacheKey, data);
          setData(data);
        });
      return () => controller.abort();
    }
  }, [state, cacheKey]);

  useEffect(() => {
    setState("loading");
  }, [URL]);

  return data;
}
```

## 匹配

在初始渲染时，以及当[历史栈](#history-stack)变化时，React Router 会将 [location](#location) 与你的[路由配置](#route-config)匹配，以得出一组[匹配](#match)来渲染。

### 定义路由

路由配置是一棵[路由](#route)树，看起来像这样：

```js
<Routes>
  <Route path="/" element={<App />}>
    <Route index element={<Home />} />
    <Route path="teams" element={<Teams />}>
      <Route path=":teamId" element={<Team />} />
      <Route path=":teamId/edit" element={<EditTeam />} />
      <Route path="new" element={<NewTeamForm />} />
      <Route index element={<LeagueStandings />} />
    </Route>
  </Route>
  <Route element={<PageLayout />}>
    <Route path="/privacy" element={<Privacy />} />
    <Route path="/tos" element={<Tos />} />
  </Route>
  <Route path="contact-us" element={<Contact />} />
</Routes>
```

`<Routes>` 组件会递归其 `props.children`，剥离它们的 props，并生成这样的对象：

```js
let routes = [
  {
    element: <App />,
    path: "/",
    children: [
      {
        index: true,
        element: <Home />,
      },
      {
        path: "teams",
        element: <Teams />,
        children: [
          {
            index: true,
            element: <LeagueStandings />,
          },
          {
            path: ":teamId",
            element: <Team />,
          },
          {
            path: ":teamId/edit",
            element: <EditTeam />,
          },
          {
            path: "new",
            element: <NewTeamForm />,
          },
        ],
      },
    ],
  },
  {
    element: <PageLayout />,
    children: [
      {
        element: <Privacy />,
        path: "/privacy",
      },
      {
        element: <Tos />,
        path: "/tos",
      },
    ],
  },
  {
    element: <Contact />,
    path: "/contact-us",
  },
];
```

实际上，你可以使用 hook `useRoutes(routesGoHere)` 来代替 `<Routes>`。这就是 `<Routes>` 做的所有事情。

如你所见，路由可以定义多个[段](#segment)，如 `:teamId/edit`，或只有一个，如 `:teamId`。[路由配置](#route-config)分支下的所有段会加在一起创建路由的最终[路径模式](#path-pattern)。

### 匹配参数

注意 `:teamId` 段。这就是我们所说的[路径模式](#path-pattern)的[动态段](#dynamic-segment)，意味着它不是静态地匹配 URL（实际字符），而是动态匹配。任何值都可以填充 `:teamId`。`/teams/123` 或 `/teams/cupcakes` 都会匹配。我们称解析出的值为 [URL 参数](#url-params)。所以在这种情况下，我们的 `teamId` 参数将是 `"123"` 或 `"cupcakes"`。我们将在[渲染](#rendering)部分看到如何在应用中使用它们。

### 路由排名

如果我们将[路由配置](#route-config)所有分支的所有段加起来，我们会得到应用响应的以下路径模式：

```js
[
  "/",
  "/teams",
  "/teams/:teamId",
  "/teams/:teamId/edit",
  "/teams/new",
  "/privacy",
  "/tos",
  "/contact-us",
];
```

现在这里变得非常有趣。考虑 [URL](#url) `/teams/new`。列表中哪个模式与 URL 匹配？

没错，两个！

React Router 必须在这里做出决定，只能有一个。许多路由器，无论是客户端还是服务端，都会简单地按定义顺序处理模式。先匹配的获胜。在这种情况下，我们将匹配 `/` 并渲染 `<Home/>` 组件。这绝对不是我们想要的。这类路由器要求我们完美地排序路由才能获得预期结果。这是 React Router 在 v6 之前的工作方式，但现在它更智能了。

看着这些模式，你直觉地知道我们希望 `/teams/new` 匹配 URL `/teams/new`。这是完美匹配！React Router 也知道。匹配时，它会根据段数、静态段、动态段、通配符模式等对路由进行排名，并选择最具体的匹配。你永远不必考虑路由排序。

### 无路径路由

你可能注意到了之前的奇怪路由：

```jsx
<Route index element={<Home />} />
<Route index element={<LeagueStandings />} />
<Route element={<PageLayout />} />
```

它们甚至没有 path，它们怎么能是路由呢？这就是 React Router 中 "route" 这个词使用得相当宽松的地方。`<Home/>` 和 `<LeagueStandings/>` 是[索引路由](#index-route)，`<PageLayout/>` 是[布局路由](#layout-route)。我们将在[渲染](#rendering)部分讨论它们如何工作。两者与匹配关系不大。

### 路由匹配

当路由与 URL 匹配时，它由一个[匹配](#match)对象表示。`<Route path=":teamId" element={<Team/>}/>` 的匹配看起来像这样：

```js
{
  pathname: "/teams/firebirds",
  params: {
    teamId: "firebirds"
  },
  route: {
    element: <Team />,
    path: ":teamId"
  }
}
```

`pathname` 保存了与此路由匹配的 URL 部分（在我们的例子中是全部）。`params` 保存了从任何匹配的[动态段](#dynamic-segment)解析出的值。注意 param 对象的键直接映射到段的名称：`:teamId` 变成 `params.teamId`。

因为我们的路由是一棵树，单个 URL 可以匹配树的整个分支。考虑 URL `/teams/firebirds`，它将是以下路由分支：

```jsx [2,4,5]
<Routes>
  <Route path="/" element={<App />}>
    <Route index element={<Home />} />
    <Route path="teams" element={<Teams />}>
      <Route path=":teamId" element={<Team />} />
      <Route path=":teamId/edit" element={<EditTeam />} />
      <Route path="new" element={<NewTeamForm />} />
      <Route index element={<LeagueStandings />} />
    </Route>
  </Route>
  <Route element={<PageLayout />}>
    <Route path="/privacy" element={<Privacy />} />
    <Route path="/tos" element={<Tos />} />
  </Route>
  <Route path="contact-us" element={<Contact />} />
</Routes>
```

React Router 会从这些路由和 URL 创建一个[匹配](#match)数组，这样它就可以渲染与路由嵌套匹配的嵌套 UI。

```js
[
  {
    pathname: "/",
    params: null,
    route: {
      element: <App />,
      path: "/",
    },
  },
  {
    pathname: "/teams",
    params: null,
    route: {
      element: <Teams />,
      path: "teams",
    },
  },
  {
    pathname: "/teams/firebirds",
    params: {
      teamId: "firebirds",
    },
    route: {
      element: <Team />,
      path: ":teamId",
    },
  },
];
```

## 渲染

最后一个概念是渲染。考虑你的应用入口看起来像这样：

```jsx
const root = ReactDOM.createRoot(
  document.getElementById("root"),
);
root.render(
  <BrowserRouter>
    <Routes>
      <Route path="/" element={<App />}>
        <Route index element={<Home />} />
        <Route path="teams" element={<Teams />}>
          <Route path=":teamId" element={<Team />} />
          <Route path="new" element={<NewTeamForm />} />
          <Route index element={<LeagueStandings />} />
        </Route>
      </Route>
      <Route element={<PageLayout />}>
        <Route path="/privacy" element={<Privacy />} />
        <Route path="/tos" element={<Tos />} />
      </Route>
      <Route path="contact-us" element={<Contact />} />
    </Routes>
  </BrowserRouter>,
);
```

让我们再次使用 `/teams/firebirds` URL 作为例子。`<Routes>` 会将 [location](#location) 与你的[路由配置](#route-config)匹配，获得一组[匹配](#match)，然后渲染像这样的 React 元素树：

```jsx
<App>
  <Teams>
    <Team />
  </Teams>
</App>
```

每个在父路由元素内渲染的匹配是一个非常强大的抽象。大多数网站和应用都具有这个特征：盒子套盒子再套盒子，每个都有一个导航部分来更改页面的子部分。

### Outlet

这个嵌套元素树不会自动发生。`<Routes>` 会为你渲染第一个匹配的元素（在我们的例子中是 `<App/>`）。下一个匹配的元素是 `<Teams>`。为了渲染它，`App` 需要渲染一个 [outlet](#outlet)。

```jsx [5]
function App() {
  return (
    <div>
      <GlobalNav />
      <Outlet />
      <GlobalFooter />
    </div>
  );
}
```

`Outlet` 组件始终渲染下一个匹配。这意味着 `<Teams>` 也需要一个 outlet 来渲染 `<Team/>`。

如果 URL 是 `/contact-us`，元素树会变为：

```jsx
<Contact />
```

因为联系表单不在主 `<App>` 路由下。

如果 URL 是 `/teams/firebirds/edit`，元素树会变为：

```jsx
<App>
  <Teams>
    <EditTeam />
  </Teams>
</App>
```

outlet 会将子元素替换为匹配的新子元素，但父布局保持不变。这很微妙但非常有效地清理了你的组件。

### 索引路由

记得 `/teams` 的[路由配置](#route-config)：

```js
<Route path="teams" element={<Teams />}>
  <Route path=":teamId" element={<Team />} />
  <Route path="new" element={<NewTeamForm />} />
  <Route index element={<LeagueStandings />} />
</Route>
```

如果 URL 是 `/teams/firebirds`，元素树将是：

```jsx
<App>
  <Teams>
    <Team />
  </Teams>
</App>
```

But if the URL were `/teams`, the element tree would be:

```jsx
<App>
  <Teams>
    <LeagueStandings />
  </Teams>
</App>
```

League standings? How the heck did `<Route index element={<LeagueStandings>}/>` pop in there? It doesn't even have a path! The reason is that it's an [index route](#index-route). Index routes render in their parent route's [outlet](#outlet) at the parent route's path.

Think of it this way, if you're not at one of the child routes' paths, the `<Outlet>` will render nothing in the UI:

```jsx
<App>
  <Teams />
</App>
```

If all the teams are in a list on the left then an empty outlet means you've got a blank page on the right! Your UI needs something to fill the space: index routes to the rescue.

Another way to think of an index route is that it's the default child route when the parent matches but none of its children do.

Depending on the user interface, you might not need an index route, but if there is any sort of persistent navigation in the parent route you'll most likely want an index route to fill the space when the user hasn't clicked one of the items yet.

### Layout Routes

Here's a part of our route config we haven't matched yet: `/privacy`. Let's look at the route config again, highlighting the matched routes:

```jsx [2,11,12]
<Routes>
  <Route path="/" element={<App />}>
    <Route index element={<Home />} />
    <Route path="teams" element={<Teams />}>
      <Route path=":teamId" element={<Team />} />
      <Route path=":teamId/edit" element={<EditTeam />} />
      <Route path="new" element={<NewTeamForm />} />
      <Route index element={<LeagueStandings />} />
    </Route>
  </Route>
  <Route element={<PageLayout />}>
    <Route path="/privacy" element={<Privacy />} />
    <Route path="/tos" element={<Tos />} />
  </Route>
  <Route path="contact-us" element={<Contact />} />
</Routes>
```

And the resulting element tree rendered will be:

```jsx
<PageLayout>
  <Privacy />
</PageLayout>
```

<docs-warning>Don't forget to add an `<Outlet>` to your layout where you would like child route elements to be rendered. Using `{children}` will not work as expected.</docs-warning>

The `PageLayout` route is admittedly weird. We call it a [layout route](#layout-route) because it doesn't participate in the matching at all (though its children do). It only exists to make wrapping multiple child routes in the same layout simpler. If we didn't allow this then you'd have to handle layouts in two different ways: sometimes your routes do it for you, sometimes you do it manually with lots of layout component repetition throughout your app:

<docs-error>You can do it like this, but we recommend using a layout route</docs-error>

```jsx bad lines=[14-16,22-24]
<Routes>
  <Route path="/" element={<App />}>
    <Route index element={<Home />} />
    <Route path="teams" element={<Teams />}>
      <Route path=":teamId" element={<Team />} />
      <Route path=":teamId/edit" element={<EditTeam />} />
      <Route path="new" element={<NewTeamForm />} />
      <Route index element={<LeagueStandings />} />
    </Route>
  </Route>
  <Route
    path="/privacy"
    element={
      <PageLayout>
        <Privacy />
      </PageLayout>
    }
  />
  <Route
    path="/tos"
    element={
      <PageLayout>
        <Tos />
      </PageLayout>
    }
  />
  <Route path="contact-us" element={<Contact />} />
</Routes>
```

So, yeah, the semantics of a layout "route" is a bit silly since it has nothing to do with the URL matching, but it's just too convenient to disallow.

## Navigating

When the [URL](#url) changes we call that a "navigation". There are two ways to navigate in React Router:

- `<Link>`
- `navigate`

### Link

This is the primary means of navigation. Rendering a `<Link>` allows the user to change the URL when they click it. React Router will prevent the browser's default behavior and tell the [history](#history) to push a new entry into the [history stack](#history-stack). The [location](#location) changes and the new [matches](#match) will render.

However, links are accessible in that they:

- Still render a `<a href>` so all default accessibility concerns are met (like keyboard, focusability, SEO, etc.)
- Don't prevent the browser's default behavior if it's a right click or command/control click to "open in new tab"

[Nested routes](#nested-routes) aren't just about rendering layouts; they also enable "relative links". Consider our `teams` route from before:

```jsx
<Route path="teams" element={<Teams />}>
  <Route path=":teamId" element={<Team />} />
</Route>
```

The `<Teams>` component can render links like:

```jsx
<Link to="psg" />
<Link to="new" />
```

The full path it links to will be `/teams/psg` and `/teams/new`. They inherit the route within which they are rendered. This makes it so your route components don't have to really know anything about the rest of the routes in the app. A very large amount of links just go one more [segment](#segment) deeper. You can rearrange your whole [route config](#route-config) and these links will likely still work just fine. This is very valuable when building out a site in the beginning and the designs and layouts are shifting around.

### Navigate Function

This function is returned from the `useNavigate` hook and allows you, the programmer, to change the URL whenever you want. You could do it on a timeout:

```js
let navigate = useNavigate();
useEffect(() => {
  setTimeout(() => {
    navigate("/logout");
  }, 30000);
}, []);
```

Or after a form is submitted:

```js
<form onSubmit={event => {
  event.preventDefault();
  let data = new FormData(event.target)
  let urlEncoded = new URLSearchParams(data)
  navigate("/create", { state: urlEncoded })
}}>
```

Like `Link`, `navigate` works with nested "to" values as well.

```js
navigate("psg");
```

You should have a good reason to use `navigate` instead of `<Link>`. This makes us very sad:

```js bad nonumber
<li onClick={() => navigate("/somewhere")} />
```

Aside from links and forms, very few interactions should change the URL because it introduces complexity around accessibility and user expectations.

## Data Access

Finally, an application is going to want to ask React Router for a few pieces of information in order to build out the full UI. For this, React Router has a pile of hooks

```js
let location = useLocation();
let urlParams = useParams();
let [urlSearchParams] = useSearchParams();
```

## Review

Let's put it all together from the top!

1. You render your app:

   ```jsx
   const root = ReactDOM.createRoot(
     document.getElementById("root"),
   );
   root.render(
     <BrowserRouter>
       <Routes>
         <Route path="/" element={<App />}>
           <Route index element={<Home />} />
           <Route path="teams" element={<Teams />}>
             <Route path=":teamId" element={<Team />} />
             <Route path="new" element={<NewTeamForm />} />
             <Route index element={<LeagueStandings />} />
           </Route>
         </Route>
         <Route element={<PageLayout />}>
           <Route path="/privacy" element={<Privacy />} />
           <Route path="/tos" element={<Tos />} />
         </Route>
         <Route path="contact-us" element={<Contact />} />
       </Routes>
     </BrowserRouter>,
   );
   ```

2. `<BrowserRouter>` creates a [history](#history), puts the initial [location](#location) in to state, and subscribes to the [URL](#url).

3. `<Routes>` recurses its [child routes](#child-route) to build a [route config](#route-config), matches those routes against the [location](#location), creates some route [matches](#match), and renders the first match's route element.

4. You render an [`<Outlet/>`](#outlet) in each [parent route](#parent-route).

5. The outlets render the next match in the route [matches](#match).

6. The user clicks a link

7. The link calls `navigate()`

8. The [history](#history) changes the URL and notifies `<BrowserRouter>`.

9. `<BrowserRouter>` rerenders, start over at (2)!

That's it! We hope this guide has helped you gain a deeper understanding of the main concepts in React Router.

[tutorial]: ./tutorial
