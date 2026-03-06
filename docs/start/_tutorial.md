---
title: 教程
order: 3
hidden: true
---

# 教程

## 介绍

[在这里查看完成的应用版本][stackblitz-app]。

React Router 是一个功能完备的客户端和服务端路由库，用于 React，一个用于构建用户界面的 JavaScript 库。React Router 可以在 React 运行的任何地方运行；在 Web 上、使用 node.js 的服务器上以及 React Native 上。

如果你刚开始学习 React，我们建议你先阅读官方文档中优秀的[入门指南][reactjs-getting-started]。那里有大量信息可以帮助你开始并运行。React Router 兼容 React >= 16.8。

我们会保持本教程快速而简洁。学完后你就会知道 React Router 日常使用的 API。之后你可以深入其他文档以获得更深入的理解。

在构建一个小型记账应用的过程中，我们将涵盖：

- 配置路由
- 使用 Link 导航
- 创建带活动样式的链接
- 使用嵌套路由实现布局
- 编程式导航
- 使用 URL 参数加载数据
- 使用 URL 搜索参数
- 通过组合创建自定义行为
- 服务端渲染

## 安装

### 推荐：StackBlitz

要完成本教程，你需要一个可工作的 React 应用。我们建议跳过打包工具，使用 [StackBlitz 上的这个 Demo][stackblitz-template] 在浏览器中跟着编写代码：

[![Open in StackBlitz](https://developer.stackblitz.com/img/open_in_stackblitz.svg)][stackblitz-template]

当你编辑文件时，教程会实时更新。

### 使用打包工具

你也可以使用你喜欢的打包工具，如 [Create React App][cra] 或 [Vite][vite]。

```sh
# create react app
npx create-react-app router-tutorial

# vite
npm init vite@latest router-tutorial --template react
```

然后安装 React Router 依赖：

```sh
cd router-tutorial
npm install react-router-dom@6
```

然后编辑你的 App.js 让它变得很简单：

```tsx filename=src/App.js
export default function App() {
  return (
    <div>
      <h1>Bookkeeper!</h1>
    </div>
  );
}
```

实际上，那个“！”看起来一点都不无聊。这非常令人兴奋。在全球疑情之后，我们调整了业务方向，React Router v6 beta 已经虏了一年多了。这是我们一段时间以来做过的最令人兴奋的事情！

最后，确保 `index.js` 或 `main.jsx`（取决于你使用的打包工具）是简单的：

```tsx filename=src/main.jsx
import * as ReactDOM from "react-dom/client";
import App from "./App";

const root = ReactDOM.createRoot(
  document.getElementById("root"),
);
root.render(<App />);
```

最后，启动你的应用：

```sh
# probably this
npm start

# or this
npm run dev
```

## 连接 URL

首先，我们要将应用连接到浏览器的 URL：导入 `BrowserRouter` 并将它包裹在整个应用周围。

```tsx lines=[2,9-11] filename=src/main.jsx
import * as ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router-dom";
import App from "./App";

const root = ReactDOM.createRoot(
  document.getElementById("root"),
);
root.render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
);
```

你的应用没有任何变化，但现在我们已经准备好开始操作 URL 了。

## 添加一些链接

打开 `src/App.js`，导入 `Link` 并添加一些全局导航。提示：不要太在意本教程中的样式，我们只是为了方便使用了内联样式，你可以用你想要的方式来给应用添加样式。

```tsx lines=[1,7-15] filename=src/App.js
import { Link } from "react-router-dom";

export default function App() {
  return (
    <div>
      <h1>Bookkeeper</h1>
      <nav
        style={{
          borderBottom: "solid 1px",
          paddingBottom: "1rem",
        }}
      >
        <Link to="/invoices">Invoices</Link> |{" "}
        <Link to="/expenses">Expenses</Link>
      </nav>
    </div>
  );
}
```

继续点击链接和后退/前进按钮（如果你使用 StackBlitz，你需要点击内置浏览器工具栏中的“在新窗口中打开”按钮）。React Router 现在正在控制 URL！

当 URL 改变时我们还没有任何路由进行渲染，但 Link 已经在不引起全页面重新加载的情况下改变了 URL。

## 添加一些路由

添加几个新文件：

- `src/routes/invoices.jsx`
- `src/routes/expenses.jsx`

（文件的位置无所谓，但当你决定为这个应用添加自动后端 API、服务端渲染、代码分割打包等功能时，这样命名文件可以让你轻松将应用迁移到我们的另一个项目 [Remix][remix] 😉）

现在给它们填充一些代码：

```tsx filename=src/routes/expenses.jsx
export default function Expenses() {
  return (
    <main style={{ padding: "1rem 0" }}>
      <h2>Expenses</h2>
    </main>
  );
}
```

```tsx filename=src/routes/invoices.jsx
export default function Invoices() {
  return (
    <main style={{ padding: "1rem 0" }}>
      <h2>Invoices</h2>
    </main>
  );
}
```

最后，让我们通过在 `main.jsx` 或 `index.js` 中创建第一个“路由配置”来教会 React Router 如何在不同 URL 上渲染我们的应用。

```tsx lines=[2,4-5,8-9,15-21] filename=src/main.jsx
import * as ReactDOM from "react-dom/client";
import {
  BrowserRouter,
  Routes,
  Route,
} from "react-router-dom";
import App from "./App";
import Expenses from "./routes/expenses";
import Invoices from "./routes/invoices";

const root = ReactDOM.createRoot(
  document.getElementById("root"),
);
root.render(
  <BrowserRouter>
    <Routes>
      <Route path="/" element={<App />} />
      <Route path="expenses" element={<Expenses />} />
      <Route path="invoices" element={<Invoices />} />
    </Routes>
  </BrowserRouter>,
);
```

注意在 `"/"` 它渲染 `<App>`。在 `"/invoices"` 它渲染 `<Invoices>`。干得漂亮！

<docs-info>记住，如果你使用 StackBlitz，点击内置浏览器工具栏中的“在新窗口中打开”按钮来使用浏览器的后退/前进按钮。</docs-info>

## 嵌套路由

你可能注意到点击链接时 `App` 中的布局会消失。重复共享布局是一件很痛苦的事情。我们已经学到大多数 UI 是一系列嵌套布局，几乎总是映射到 URL 的段，所以这个想法已经内置到 React Router 中。

让我们通过两个操作来获得自动的、持久的布局处理：

1. 将路由嵌套在 App 路由内
2. 渲染一个 Outlet

首先让我们嵌套路由。现在 expenses 和 invoices 路由是 app 的兄弟，我们要把它们变成 app 路由的*子路由*：

```jsx lines=[17-20] filename=src/main.jsx
import * as ReactDOM from "react-dom/client";
import {
  BrowserRouter,
  Routes,
  Route,
} from "react-router-dom";
import App from "./App";
import Expenses from "./routes/expenses";
import Invoices from "./routes/invoices";

const root = ReactDOM.createRoot(
  document.getElementById("root"),
);
root.render(
  <BrowserRouter>
    <Routes>
      <Route path="/" element={<App />}>
        <Route path="expenses" element={<Expenses />} />
        <Route path="invoices" element={<Invoices />} />
      </Route>
    </Routes>
  </BrowserRouter>,
);
```

当路由有子路由时，它会做两件事：

1. 嵌套 URL（`"/" + "expenses"` 和 `"/" + "invoices"`）
2. 当子路由匹配时，它会嵌套 UI 组件以实现共享布局：

但是，在 (2) 生效之前，我们需要在 `App.jsx` “父”路由中渲染一个 `Outlet`。

```jsx lines=[1,16] filename=src/App.jsx
import { Outlet, Link } from "react-router-dom";

export default function App() {
  return (
    <div>
      <h1>Bookkeeper</h1>
      <nav
        style={{
          borderBottom: "solid 1px",
          paddingBottom: "1rem",
        }}
      >
        <Link to="/invoices">Invoices</Link> |{" "}
        <Link to="/expenses">Expenses</Link>
      </nav>
      <Outlet />
    </div>
  );
}
```

现在再次点击。父路由（`App.js`）保持不变，而 `<Outlet>` 在两个子路由（`<Invoices>` 和 `<Expenses>`）之间切换！

正如我们稍后将看到的，这在路由层级的*任何层级*都有效，并且非常强大。

## 列出发票

通常你会从某个服务器获取数据，但对于本教程，让我们硬编码一些假数据，这样我们可以专注于路由。

在 `src/data.js` 创建一个文件，复制粘贴以下内容：

```js filename=src/data.js
let invoices = [
  {
    name: "Santa Monica",
    number: 1995,
    amount: "$10,800",
    due: "12/05/1995",
  },
  {
    name: "Stankonia",
    number: 2000,
    amount: "$8,000",
    due: "10/31/2000",
  },
  {
    name: "Ocean Avenue",
    number: 2003,
    amount: "$9,500",
    due: "07/22/2003",
  },
  {
    name: "Tubthumper",
    number: 1997,
    amount: "$14,000",
    due: "09/01/1997",
  },
  {
    name: "Wide Open Spaces",
    number: 1998,
    amount: "$4,600",
    due: "01/27/1998",
  },
];

export function getInvoices() {
  return invoices;
}
```

现在我们可以在 invoices 路由中使用它。让我们也添加一些样式来实现侧边栏导航布局。可以随意复制粘贴这些，但请特别注意 `<Link>` 元素的 `to` prop：

```js lines=[17] filename=src/routes/invoices.jsx
import { Link } from "react-router-dom";
import { getInvoices } from "../data";

export default function Invoices() {
  let invoices = getInvoices();
  return (
    <div style={{ display: "flex" }}>
      <nav
        style={{
          borderRight: "solid 1px",
          padding: "1rem",
        }}
      >
        {invoices.map((invoice) => (
          <Link
            style={{ display: "block", margin: "1rem 0" }}
            to={`/invoices/${invoice.number}`}
            key={invoice.number}
          >
            {invoice.name}
          </Link>
        ))}
      </nav>
    </div>
  );
}
```

很好！现在点击一个发票链接看看会发生什么。

😨😨😨

## 添加“无匹配”路由

这可能不是你预期的结果。如果你点击这些链接，页面会变空白！那是因为我们定义的没有任何路由匹配我们链接到的 URL：`"/invoices/123"`。

在继续之前，始终处理这种“无匹配”情况是好的实践。回到你的路由配置并添加以下内容：

```js lines=[5-12] filename=src/main.jsx
<Routes>
  <Route path="/" element={<App />}>
    <Route path="expenses" element={<Expenses />} />
    <Route path="invoices" element={<Invoices />} />
    <Route
      path="*"
      element={
        <main style={{ padding: "1rem" }}>
          <p>There's nothing here!</p>
        </main>
      }
    />
  </Route>
</Routes>
```

这里的 `"*"` 有特殊含义。它只在没有其他路由匹配时才会匹配。

## 读取 URL 参数

好的，回到单个发票 URL。让我们为特定发票添加一个路由。我们刚刚访问了一些 URL，如 `"/invoices/1998"` 和 `"/invoices/2005"`，让我们在 `src/routes/invoice.jsx` 创建一个新组件来在这些 URL 上渲染：

```js filename=src/routes/invoice.jsx
export default function Invoice() {
  return <h2>Invoice #???</h2>;
}
```

我们想渲染发票号而不是 `"???"`。通常在 React 中你会将它作为 prop 传递：`<Invoice invoiceId="123" />`，但你无法控制这个信息，因为它来自 URL。

让我们定义一个可以匹配这类 URL 的路由，并能从中获取发票号。

在 "invoices" 路由*内部*创建一个新的 `<Route>`，像这样：

```js lines=[4-6] filename=src/main.jsx
<Routes>
  <Route path="/" element={<App />}>
    <Route path="expenses" element={<Expenses />} />
    <Route path="invoices" element={<Invoices />}>
      <Route path=":invoiceId" element={<Invoice />} />
    </Route>
    <Route
      path="*"
      element={
        <main style={{ padding: "1rem" }}>
          <p>There's nothing here!</p>
        </main>
      }
    />
  </Route>
</Routes>
```

注意两点：

- 我们刚刚创建了一个匹配像 "/invoices/2005" 和 "/invoices/1998" 这样 URL 的路由。路径中的 `:invoiceId` 部分是一个“URL 参数”，意味着只要模式相同它可以匹配任何值。
- `<Route>` 在匹配时添加了第二层路由嵌套：`<App><Invoices><Invoice /></Invoices></App>`。因为 `<Route>` 是嵌套的，UI 也会嵌套。

好的，现在点击一个发票链接，注意 URL 改变了但新的发票组件还没有显示。你知道为什么吗？

没错！我们需要在父布局路由中添加一个 outlet（我们真的为你感到骄傲）。

```tsx lines=[1,24] filename=src/routes/invoices.jsx
import { Link, Outlet } from "react-router-dom";
import { getInvoices } from "../data";

export default function Invoices() {
  let invoices = getInvoices();
  return (
    <div style={{ display: "flex" }}>
      <nav
        style={{
          borderRight: "solid 1px",
          padding: "1rem",
        }}
      >
        {invoices.map((invoice) => (
          <Link
            style={{ display: "block", margin: "1rem 0" }}
            to={`/invoices/${invoice.number}`}
            key={invoice.number}
          >
            {invoice.name}
          </Link>
        ))}
      </nav>
      <Outlet />
    </div>
  );
}
```

好的，让我们在这里闭合循环。再次打开 invoice 组件，让我们从 URL 获取 `:invoiceId` 参数：

```ts lines=[1,4] filename=src/routes/invoice.jsx
import { useParams } from "react-router-dom";

export default function Invoice() {
  let params = useParams();
  return <h2>Invoice: {params.invoiceId}</h2>;
}
```

注意 `params` 对象上参数的键名与路由路径中的动态段相同：

```
:invoiceId -> params.invoiceId
```

让我们用这个信息来构建一个更有趣的发票页面。打开 `src/data.js` 并添加一个新函数来按编号查找发票：

```js filename=src/data.js lines=[7-11]
// ...

export function getInvoices() {
  return invoices;
}

export function getInvoice(number) {
  return invoices.find(
    (invoice) => invoice.number === number,
  );
}
```

现在回到 `invoice.jsx`，我们使用参数来查找发票并显示更多信息：

```js filename=routes/invoice.jsx lines=[2,6]
import { useParams } from "react-router-dom";
import { getInvoice } from "../data";

export default function Invoice() {
  let params = useParams();
  let invoice = getInvoice(parseInt(params.invoiceId, 10));
  return (
    <main style={{ padding: "1rem" }}>
      <h2>Total Due: {invoice.amount}</h2>
      <p>
        {invoice.name}: {invoice.number}
      </p>
      <p>Due Date: {invoice.due}</p>
    </main>
  );
}
```

注意我们在参数上使用了 `parseInt`。你的数据查找使用 `number` 类型是很常见的，但 URL 参数始终是 `string`。

## 索引路由

索引路由可能是 React Router 中人们最难理解的概念。所以如果你以前有过困惑，我们希望这可以为你澄清。

现在你可能正在查看其中一个发票。点击应用全局导航中的 "Invoices" 链接。注意主内容区域变空白了！我们可以用一个“索引”路由来修复这个问题。

```jsx filename=src/main.jsx lines=[5-12]
<Routes>
  <Route path="/" element={<App />}>
    <Route path="expenses" element={<Expenses />} />
    <Route path="invoices" element={<Invoices />}>
      <Route
        index
        element={
          <main style={{ padding: "1rem" }}>
            <p>Select an invoice</p>
          </main>
        }
      />
      <Route path=":invoiceId" element={<Invoice />} />
    </Route>
    <Route
      path="*"
      element={
        <main style={{ padding: "1rem" }}>
          <p>There's nothing here!</p>
        </main>
      }
    />
  </Route>
</Routes>
```

太好了！现在索引路由填充了空白区域！

注意它有 `index` prop 而不是 `path`。那是因为索引路由与父路由共享路径。这就是它的全部意义——它没有路径。

也许你还在困惑。我们有几种方式来回答“什么是索引路由？”这个问题。希望其中一个对你有用：

- 索引路由在父路由路径的父路由 outlet 中渲染。
- 索引路由在父路由匹配但其他子路由都不匹配时匹配。
- 索引路由是父路由的默认子路由。
- 索引路由在用户还没有点击导航列表中的任何项目时渲染。

## 活动链接

非常常见的做法是，特别是在导航列表中，将链接显示为用户正在查看的活动链接。让我们通过将 `Link` 替换为 `NavLink` 来给发票列表添加这个处理。

```jsx lines=[1,15-27] filename=src/routes/invoices.jsx
import { NavLink, Outlet } from "react-router-dom";
import { getInvoices } from "../data";

export default function Invoices() {
  let invoices = getInvoices();
  return (
    <div style={{ display: "flex" }}>
      <nav
        style={{
          borderRight: "solid 1px",
          padding: "1rem",
        }}
      >
        {invoices.map((invoice) => (
          <NavLink
            style={({ isActive }) => {
              return {
                display: "block",
                margin: "1rem 0",
                color: isActive ? "red" : "",
              };
            }}
            to={`/invoices/${invoice.number}`}
            key={invoice.number}
          >
            {invoice.name}
          </NavLink>
        ))}
      </nav>
      <Outlet />
    </div>
  );
}
```

我们做了三件事：

1. 我们将 `Link` 替换为 `NavLink`。
2. 我们将 `style` 从一个简单对象改为一个返回对象的函数。
3. 我们通过查看 `NavLink` 传递给样式函数的 `isActive` 值来更改链接颜色。

你可以在 `NavLink` 上使用 `className` 做同样的事情：

```jsx
// 普通字符串
<NavLink className="red" />

// 函数
<NavLink className={({ isActive }) => isActive ? "red" : "blue"} />
```

## 搜索参数

搜索参数像 URL 参数，但它们在 URL 中的位置不同。它们不是在由 `/` 分隔的普通 URL 段中，而是在末尾的 `?` 之后。你在网上见过它们，比如 `"/login?success=1"` 或 `"/shoes?brand=nike&sort=asc&sortby=price"`。

React Router 通过 `useSearchParams` 让你可以轻松地读取和操作搜索参数。它的工作方式很像 `React.useState()`，但是将状态存储和设置在 URL 搜索参数中而不是内存中。

让我们通过在发票导航列表上添加一个小的过滤器来看看它的效果。

```jsx filename=routes/invoices.jsx lines=[4,10,20-30,32-37]
import {
  NavLink,
  Outlet,
  useSearchParams,
} from "react-router-dom";
import { getInvoices } from "../data";

export default function Invoices() {
  let invoices = getInvoices();
  let [searchParams, setSearchParams] = useSearchParams();

  return (
    <div style={{ display: "flex" }}>
      <nav
        style={{
          borderRight: "solid 1px",
          padding: "1rem",
        }}
      >
        <input
          value={searchParams.get("filter") || ""}
          onChange={(event) => {
            let filter = event.target.value;
            if (filter) {
              setSearchParams({ filter });
            } else {
              setSearchParams({});
            }
          }}
        />
        {invoices
          .filter((invoice) => {
            let filter = searchParams.get("filter");
            if (!filter) return true;
            let name = invoice.name.toLowerCase();
            return name.startsWith(filter.toLowerCase());
          })
          .map((invoice) => (
            <NavLink
              style={({ isActive }) => ({
                display: "block",
                margin: "1rem 0",
                color: isActive ? "red" : "",
              })}
              to={`/invoices/${invoice.number}`}
              key={invoice.number}
            >
              {invoice.name}
            </NavLink>
          ))}
      </nav>
      <Outlet />
    </div>
  );
}
```

看看这个，当用户输入时：

- `setSearchParams()` 将 `?filter=...` 搜索参数放入 URL 并重新渲染路由器。
- `useSearchParams` 现在返回一个 [`URLSearchParams`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)，其中 `"filter"` 是其值之一。
- 我们将 input 的值设置为 filter 搜索参数中的任何内容（就像 `useState` 但在 URLSearchParams 中！）
- 我们根据 filter 搜索参数过滤发票列表。

## 自定义行为

如果你过滤列表然后点击一个链接，你会注意到列表不再被过滤，搜索参数从 `<input>` 和 URL 中清除了。你可能想要这样，也可能不想！也许你想保持列表的过滤状态并将参数保留在 URL 中。

我们可以通过将查询字符串添加到链接的 href 来在点击链接时保持查询字符串。我们将通过组合 React Router 的 `NavLink` 和 `useLocation` 来创建我们自己的 `QueryNavLink`（也许有更好的名字，但今天我们就用这个）。

```js
import { useLocation, NavLink } from "react-router-dom";

function QueryNavLink({ to, ...props }) {
  let location = useLocation();
  return <NavLink to={to + location.search} {...props} />;
}
```

你可以把这段代码放在应用的任何你想要的地方，然后在 `src/routes/invoices.jsx` 中将 `NavLink` 替换为 `QueryNavLink` 就完成了。

像 `useSearchParams` 一样，`useLocation` 返回一个 location 对象，告诉我们关于 URL 的信息。一个 location 看起来像这样：

```js
{
  pathname: "/invoices",
  search: "?filter=sa",
  hash: "",
  state: null,
  key: "ae4cz2j"
}
```

有了这些信息，`QueryNavLink` 中的任务就很简单了：将 `location.search` 添加到 `to` prop 上。你可能会想，“天哪，这似乎应该是 React Router 的一个内置组件什么的？”。好吧，让我们看看另一个例子。

如果你在一个电商网站上有这样的链接。

```jsx
<Link to="/shoes?brand=nike">Nike</Link>
<Link to="/shoes?brand=vans">Vans</Link>
```

然后你想在 URL 搜索参数与品牌匹配时将它们样式化为“活动”？你可以用本教程中学到的知识很快地创建一个正好做到这一点的组件：

```jsx
function BrandLink({ brand, ...props }) {
  let [params] = useSearchParams();
  let isActive = params.getAll("brand").includes(brand);
  return (
    <Link
      style={{ color: isActive ? "red" : "" }}
      to={`/shoes?brand=${brand}`}
      {...props}
    />
  );
}
```

它将对 `"/shoes?brand=nike"` 以及 `"/shoes?brand=nike&brand=vans"` 都显示为活动。也许你想要只在选择了一个品牌时才显示为活动：

```js
let brands = params.getAll("brand");
let isActive =
  brands.includes(brand) && brands.length === 1;
// ...
```

或者你想让链接是*叠加的*（点击 Nike 然后点击 Vans 会将两个品牌都添加到搜索参数）而不是替换品牌：

```jsx [4-6,10]
function BrandLink({ brand, ...props }) {
  let [params] = useSearchParams();
  let isActive = params.getAll("brand").includes(brand);
  if (!isActive) {
    params.append("brand", brand);
  }
  return (
    <Link
      style={{ color: isActive ? "red" : "" }}
      to={`/shoes?${params.toString()}`}
      {...props}
    />
  );
}
```

或者你想要如果品牌还不在就添加，如果再次点击就移除！

```jsx [7-12]
function BrandLink({ brand, ...props }) {
  let [params] = useSearchParams();
  let isActive = params.getAll("brand").includes(brand);
  if (!isActive) {
    params.append("brand", brand);
  } else {
    params = new URLSearchParams(
      Array.from(params).filter(
        ([key, value]) =>
          key !== "brand" || value !== brand,
      ),
    );
  }
  return (
    <Link
      style={{ color: isActive ? "red" : "" }}
      to={`/shoes?${params.toString()}`}
      {...props}
    />
  );
}
```

如你所见，即使在这个相当简单的例子中，你可能想要的有效行为也有很多。React Router 不会试图直接解决我们听过的每一个用例。相反，我们给你提供组件和 hooks，让你组合出你需要的任何行为。

## 编程式导航

好的，回到我们的应用。坚持住，你快完成了！

大多数时候 URL 变化是用户点击链接的响应。但有时你——程序员——想要更改 URL。一个非常常见的用例是在数据更新后，比如创建或删除记录。

让我们添加一个按钮，将发票标记为已付款，然后导航到索引路由。

首先你可以复制粘贴这个从我们的假数据存储中删除发票的函数：

```js filename=src/data.js
export function deleteInvoice(number) {
  invoices = invoices.filter(
    (invoice) => invoice.number !== number,
  );
}
```

现在让我们添加删除按钮，调用新函数，并导航到索引路由：

```js lines=[1-6,9-10,21-30] filename=src/routes/invoice.jsx
import {
  useParams,
  useNavigate,
  useLocation,
} from "react-router-dom";
import { getInvoice, deleteInvoice } from "../data";

export default function Invoice() {
  let navigate = useNavigate();
  let location = useLocation();
  let params = useParams();
  let invoice = getInvoice(parseInt(params.invoiceId, 10));

  return (
    <main style={{ padding: "1rem" }}>
      <h2>Total Due: {invoice.amount}</h2>
      <p>
        {invoice.name}: {invoice.number}
      </p>
      <p>Due Date: {invoice.due}</p>
      <p>
        <button
          onClick={() => {
            deleteInvoice(invoice.number);
            navigate("/invoices" + location.search);
          }}
        >
          Delete
        </button>
      </p>
    </main>
  );
}
```

注意我们再次使用了 `useLocation` 来通过将 `location.search` 添加到导航链接来保持查询字符串。

## 获取帮助

恭喜！你已经完成了本教程。我们希望它能帮助你熟悉 React Router。

如果你遇到困难，请查看[资源](/resources)页面获取帮助。祝你好运！

[stackblitz-app]: https://stackblitz.com/edit/github-agqlf5?file=src/App.jsx
[stackblitz-template]: https://stackblitz.com/github/remix-run/react-router/tree/main/tutorial?file=src/App.jsx
[reactjs-getting-started]: https://reactjs.org/docs/getting-started.html
[cra]: https://create-react-app.dev/
[vite]: https://vitejs.dev/guide/#scaffolding-your-first-vite-project
[remix]: https://remix.run
