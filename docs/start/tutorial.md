---
title: 教程
order: 2
---

# 教程

欢迎来到教程！我们将构建一个小而功能丰富的应用，用来管理你的联系人。如果你跟着做的话，预计需要 30-60 分钟。

<img class="tutorial" src="/_docs/tutorial/15.webp" />

👉 **每次你看到这个标志，说明你需要在应用中做些操作！**

其余内容仅供参考和深入理解。让我们开始吧。

## 安装

<docs-info>如果你不打算在自己的应用中跟着做，可以跳过本节</docs-info>

本教程我们将使用 [Vite][vite] 作为打包工具和开发服务器。你需要安装 [Node.js][node] 以使用 `npm` 命令行工具。

👉️ **打开终端，使用 Vite 创建一个新的 React 应用：**

```sh
npm create vite@latest name-of-your-project -- --template react
# follow prompts
cd <your new project directory>
npm install react-router-dom # always need this!
npm install localforage match-sorter sort-by # only for this tutorial.
npm run dev
```

你应该能够访问终端中打印的 URL：

```
 VITE v3.0.7  ready in 175 ms

  ➜  Local:   http://127.0.0.1:5173/
  ➜  Network: use --host to expose
```

我们为本教程准备了一些预写的 CSS，这样我们可以专注于 React Router。随意评价或自己编写 😅（我们在 CSS 中做了一些通常不会做的事情，以使本教程的标记尽可能精简。）

👉 **将[这里找到的][tutorial-css]教程 CSS 复制粘贴到 `src/index.css`**

本教程将创建、读取、搜索、更新和删除数据。一个典型的 Web 应用可能会与 Web 服务器上的 API 通信，但我们将使用浏览器存储并模拟一些网络延迟以保持专注。这些代码都与 React Router 无关，所以直接复制粘贴就好。

👉 **将[这里找到的][tutorial-data]教程数据模块复制粘贴到 `src/contacts.js`**

src 文件夹中你只需要 `contacts.js`、`main.jsx` 和 `index.css`。你可以删除其他所有内容（比如 `App.js` 和 `assets` 等）。

👉 **删除 `src/` 中未使用的文件，只保留以下这些：**

```
src
├── contacts.js
├── index.css
└── main.jsx
```

如果你的应用正在运行，它可能会暂时崩溃，继续就好 😋。这样我们就准备好开始了！

## 添加路由器

首先要做的是创建一个[浏览器路由器][createbrowserrouter]并配置我们的第一个路由。这将为我们的 Web 应用启用客户端路由。

`main.jsx` 文件是入口点。打开它，我们来把 React Router 放到页面上。

👉 **在 `main.jsx` 中创建并渲染一个[浏览器路由器][createbrowserrouter]**

```jsx lines=[3-6,9-14,18] filename=src/main.jsx
import * as React from "react";
import * as ReactDOM from "react-dom/client";
import {
  createBrowserRouter,
  RouterProvider,
} from "react-router-dom";
import "./index.css";

const router = createBrowserRouter([
  {
    path: "/",
    element: <div>Hello world!</div>,
  },
]);

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>,
);
```

这第一个路由就是我们通常所说的

## 根路由

让我们来添加这个应用的全局布局。

👉 **创建 `src/routes` 和 `src/routes/root.jsx`**

```sh
mkdir src/routes
touch src/routes/root.jsx
```

<small>（如果你不想当命令行达人，可以用编辑器来代替这些命令 🤓）</small>

👉 **创建根布局组件**

```jsx filename=src/routes/root.jsx
export default function Root() {
  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <form id="search-form" role="search">
            <input
              id="q"
              aria-label="Search contacts"
              placeholder="Search"
              type="search"
              name="q"
            />
            <div
              id="search-spinner"
              aria-hidden
              hidden={true}
            />
            <div
              className="sr-only"
              aria-live="polite"
            ></div>
          </form>
          <form method="post">
            <button type="submit">New</button>
          </form>
        </div>
        <nav>
          <ul>
            <li>
              <a href={`/contacts/1`}>Your Name</a>
            </li>
            <li>
              <a href={`/contacts/2`}>Your Friend</a>
            </li>
          </ul>
        </nav>
      </div>
      <div id="detail"></div>
    </>
  );
}
```

到目前为止还没有 React Router 相关的内容，所以随意复制粘贴这些。

👉 **将 `<Root>` 设置为根路由的 [`element`][routeelement]**

```jsx filename=src/main.jsx lines=[2,7]
/* existing imports */
import Root from "./routes/root";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
  },
]);

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>,
);
```

应用现在应该看起来像这样。有一个能写 CSS 的设计师真好，不是吗？（感谢 [Jim][jim] 🙏）。

<img class="tutorial" loading="lazy" src="/_docs/tutorial/01.webp" />

## 处理未找到错误

在项目早期了解应用如何响应错误总是一个好主意，因为我们在构建新应用时写的 bug 远比功能多！这不仅能让用户在遇到错误时有好的体验，还能在开发过程中帮助你。

我们在应用中添加了一些链接，看看点击它们会发生什么？

👉 **点击侧边栏中的一个名字**

<img class="tutorial" loading="lazy" alt="screenshot of default React Router error element" src="/_docs/tutorial/02.webp" />

糟糕！这是 React Router 的默认错误页面，被我们应用中根元素的 flex box 样式搞得更难看了 😂。

每当你的应用在渲染、加载数据或执行数据变更时抛出错误，React Router 都会捕获它并渲染一个错误页面。让我们来创建自己的错误页面。

👉 **创建一个错误页面组件**

```sh
touch src/error-page.jsx
```

```jsx filename=src/error-page.jsx
import { useRouteError } from "react-router-dom";

export default function ErrorPage() {
  const error = useRouteError();
  console.error(error);

  return (
    <div id="error-page">
      <h1>Oops!</h1>
      <p>Sorry, an unexpected error has occurred.</p>
      <p>
        <i>{error.statusText || error.message}</i>
      </p>
    </div>
  );
}
```

👉 **将 `<ErrorPage>` 设置为根路由的 [`errorElement`][errorelement]**

```jsx filename=src/main.jsx lines=[2,8]
/* previous imports */
import ErrorPage from "./error-page";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
  },
]);

ReactDOM.createRoot(document.getElementById("root")).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>,
);
```

错误页面现在应该看起来像这样：

<img class="tutorial" loading="lazy" alt="new error page, but still ugly" src="/_docs/tutorial/03.webp" />

<small>（好吧，也没好多少。也许有人忘了让设计师做一个错误页面。也许每个人都忘了让设计师做错误页面，然后责怪设计师没想到 😆）</small>

注意 [`useRouteError`][userouteerror] 提供了抛出的错误。当用户导航到不存在的路由时，你会得到一个带有 "Not Found" `statusText` 的[错误响应][isrouteerrorresponse]。我们将在教程后面看到其他错误并进一步讨论。

现在，知道你的几乎所有错误现在都由这个页面处理就足够了，而不是无限转圈、无响应的页面或空白屏幕 🙌

## 联系人路由 UI

不是显示 404 "未找到"页面，我们希望在我们链接到的 URL 上实际渲染一些内容。为此，我们需要创建一个新路由。

👉 **创建联系人路由模块**

```sh
touch src/routes/contact.jsx
```

👉 **添加联系人组件 UI**

它只是一堆元素，随意复制粘贴。

```jsx filename=src/routes/contact.jsx
import { Form } from "react-router-dom";

export default function Contact() {
  const contact = {
    first: "Your",
    last: "Name",
    avatar: "https://robohash.org/you.png?size=200x200",
    twitter: "your_handle",
    notes: "Some notes",
    favorite: true,
  };

  return (
    <div id="contact">
      <div>
        <img
          key={contact.avatar}
          src={
            contact.avatar ||
            `https://robohash.org/${contact.id}.png?size=200x200`
          }
        />
      </div>

      <div>
        <h1>
          {contact.first || contact.last ? (
            <>
              {contact.first} {contact.last}
            </>
          ) : (
            <i>No Name</i>
          )}{" "}
          <Favorite contact={contact} />
        </h1>

        {contact.twitter && (
          <p>
            <a
              target="_blank"
              href={`https://twitter.com/${contact.twitter}`}
            >
              {contact.twitter}
            </a>
          </p>
        )}

        {contact.notes && <p>{contact.notes}</p>}

        <div>
          <Form action="edit">
            <button type="submit">Edit</button>
          </Form>
          <Form
            method="post"
            action="destroy"
            onSubmit={(event) => {
              if (
                !confirm(
                  "Please confirm you want to delete this record.",
                )
              ) {
                event.preventDefault();
              }
            }}
          >
            <button type="submit">Delete</button>
          </Form>
        </div>
      </div>
    </div>
  );
}

function Favorite({ contact }) {
  const favorite = contact.favorite;
  return (
    <Form method="post">
      <button
        name="favorite"
        value={favorite ? "false" : "true"}
        aria-label={
          favorite
            ? "Remove from favorites"
            : "Add to favorites"
        }
      >
        {favorite ? "★" : "☆"}
      </button>
    </Form>
  );
}
```

现在我们有了一个组件，让我们把它挂载到一个新路由上。

👉 **导入联系人组件并创建一个新路由**

```js filename=src/main.jsx lines=[2,10-13]
/* existing imports */
import Contact from "./routes/contact";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
  },
  {
    path: "contacts/:contactId",
    element: <Contact />,
  },
]);

/* existing code */
```

现在如果我们点击其中一个链接或访问 `/contacts/1`，就会看到我们的新组件！

<img class="tutorial" loading="lazy" alt="contact route rendering without the parent layout" src="/_docs/tutorial/04.webp" />

但是，它没有在我们的根布局内部 😠

## 嵌套路由

我们希望联系人组件在 `<Root>` 布局的*内部*渲染，像这样。

<img class="tutorial" loading="lazy" src="/_docs/tutorial/05.webp" />

我们通过将联系人路由设为根路由的*子路由*来实现。

👉 **将联系人路由移动为根路由的子路由**

```jsx filename=src/main.jsx lines=[6-11]
const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
      },
    ],
  },
]);
```

你现在又会看到根布局了，但右侧是一个空白页面。我们需要告诉根路由我们希望它在*哪里*渲染子路由。我们通过 [`<Outlet>`][outlet] 来实现。

找到 `<div id="detail">` 并在内部放置一个 outlet

👉 **渲染一个 [`<Outlet>`][outlet]**

```jsx filename=src/routes/root.jsx lines=[1,8]
import { Outlet } from "react-router-dom";

export default function Root() {
  return (
    <>
      {/* all the other elements */}
      <div id="detail">
        <Outlet />
      </div>
    </>
  );
}
```

## 客户端路由

你可能已经注意到了，也可能没有，当我们点击侧边栏中的链接时，浏览器会对下一个 URL 发起完整的文档请求，而不是使用 React Router。

客户端路由允许我们的应用更新 URL 而无需从服务器请求另一个文档。相反，应用可以立即渲染新的 UI。让我们用 [`<Link>`][link] 来实现。

👉 **将侧边栏的 `<a href>` 改为 `<Link to>`**

```jsx filename=src/routes/root.jsx lines=[1,12,15]
import { Outlet, Link } from "react-router-dom";

export default function Root() {
  return (
    <>
      <div id="sidebar">
        {/* other elements */}

        <nav>
          <ul>
            <li>
              <Link to={`contacts/1`}>Your Name</Link>
            </li>
            <li>
              <Link to={`contacts/2`}>Your Friend</Link>
            </li>
          </ul>
        </nav>

        {/* other elements */}
      </div>
    </>
  );
}
```

你可以打开浏览器开发工具的网络面板看到它不再请求文档了。

## 加载数据

URL 段、布局和数据往往是耦合在一起的。在这个应用中已经可以看到：

| URL 段       | 组件        | 数据       |
| ------------ | ----------- | ---------- |
| /            | `<Root>`    | 联系人列表 |
| contacts/:id | `<Contact>` | 单个联系人 |

由于这种自然的耦合，React Router 提供了数据约定来轻松地将数据获取到路由组件中。

我们将使用两个 API 来加载数据：[`loader`][loader] 和 [`useLoaderData`][useloaderdata]。首先我们在根模块中创建并导出一个 loader 函数，然后将它连接到路由。最后，我们将访问并渲染数据。

👉 **从 `root.jsx` 导出一个 loader**

```jsx filename=src/routes/root.jsx lines=[2,4-7]
import { Outlet, Link } from "react-router-dom";
import { getContacts } from "../contacts";

export async function loader() {
  const contacts = await getContacts();
  return { contacts };
}
```

👉 **在路由上配置 loader**

```jsx filename=src/main.jsx lines=[2,9]
/* other imports */
import Root, { loader as rootLoader } from "./routes/root";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
      },
    ],
  },
]);
```

👉 **访问并渲染数据**

```jsx filename=src/routes/root.jsx lines=[4,11,19-40]
import {
  Outlet,
  Link,
  useLoaderData,
} from "react-router-dom";
import { getContacts } from "../contacts";

/* other code */

export default function Root() {
  const { contacts } = useLoaderData();
  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        {/* other code */}

        <nav>
          {contacts.length ? (
            <ul>
              {contacts.map((contact) => (
                <li key={contact.id}>
                  <Link to={`contacts/${contact.id}`}>
                    {contact.first || contact.last ? (
                      <>
                        {contact.first} {contact.last}
                      </>
                    ) : (
                      <i>No Name</i>
                    )}{" "}
                    {contact.favorite && <span>★</span>}
                  </Link>
                </li>
              ))}
            </ul>
          ) : (
            <p>
              <i>No contacts</i>
            </p>
          )}
        </nav>

        {/* other code */}
      </div>
    </>
  );
}
```

就这样！React Router 现在会自动保持数据与 UI 同步。我们还没有任何数据，所以你可能会看到一个空列表像这样：

<img class="tutorial" loading="lazy" src="/_docs/tutorial/06.webp" />

## 数据写入 + HTML 表单

我们马上就会创建第一个联系人，但首先让我们谈谈 HTML。

React Router 模拟 HTML 表单导航作为数据变更原语，这是 JavaScript 寒武纪大爆发之前的 Web 开发方式。它为你提供了客户端渲染应用的 UX 能力，同时保持了“老派”Web 模型的简单性。

虽然对某些 Web 开发者来说可能不熟悉，但 HTML 表单实际上会在浏览器中引起导航，就像点击链接一样。唯一的区别在于请求：链接只能改变 URL，而表单还可以改变请求方法（GET vs POST）和请求体（POST 表单数据）。

没有客户端路由时，浏览器会自动序列化表单数据，并将其作为 POST 的请求体发送到服务器，对于 GET 则作为 URLSearchParams。React Router 做的是同样的事情，只不过它不是将请求发送到服务器，而是使用客户端路由将其发送到路由的 [`action`][action]。

我们可以通过点击应用中的 "New" 按钮来测试。应用应该会崩溃，因为 Vite 服务器没有配置来处理 POST 请求（它发送了 404，虽然应该是 405 🤷）。

<img class="tutorial" loading="lazy" src="/_docs/tutorial/07.webp" />

不要将那个 POST 发送到 Vite 服务器来创建新联系人，让我们使用客户端路由来代替。

## 创建联系人

我们将通过在根路由中导出一个 `action`、将其连接到路由配置，并将 `<form>` 改为 React Router 的 [`<Form>`][form] 来创建新联系人。

👉 **创建 action 并将 `<form>` 改为 `<Form>`**

```jsx filename=src/routes/root.jsx lines=[5,7,9-12,24-26]
import {
  Outlet,
  Link,
  useLoaderData,
  Form,
} from "react-router-dom";
import { getContacts, createContact } from "../contacts";

export async function action() {
  const contact = await createContact();
  return { contact };
}

/* other code */

export default function Root() {
  const { contacts } = useLoaderData();
  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          {/* other code */}
          <Form method="post">
            <button type="submit">New</button>
          </Form>
        </div>

        {/* other code */}
      </div>
    </>
  );
}
```

👉 **导入并在路由上设置 action**

```jsx filename=src/main.jsx lines=[5,14]
/* other imports */

import Root, {
  loader as rootLoader,
  action as rootAction,
} from "./routes/root";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
      },
    ],
  },
]);
```

就这样！继续点击 "New" 按钮，你应该会看到一条新记录出现在列表中 🥳

<img class="tutorial" loading="lazy" src="/_docs/tutorial/08.webp" />

`createContact` 方法只是创建了一个没有名称、没有数据、什么都没有的空联系人。但它确实创建了一条记录，没骗你！

> 🧐 等等... 侧边栏是如何更新的？我们在哪里调用了 `action`？重新获取数据的代码在哪里？`useState`、`onSubmit` 和 `useEffect` 在哪里？！

这就是“老派 Web”编程模型登场的地方。正如我们之前讨论的，[`<Form>`][form] 阻止了浏览器将请求发送到服务器，而是将其发送到你的路由 `action`。在 Web 语义中，POST 通常意味着某些数据正在发生变化。按惯例，React Router 将此作为提示，在 action 完成后自动重新验证页面上的数据。这意味着你所有的 `useLoaderData` hook 都会更新，UI 会自动与你的数据保持同步！相当酷。

## Loader 中的 URL 参数

👉 **点击“No Name”记录**

我们应该又会看到之前的静态联系人页面，但有一个区别：URL 现在有了记录的真实 ID。

<img class="tutorial" loading="lazy" src="/_docs/tutorial/09.webp" />

回顾路由配置，路由看起来是这样的：

```jsx
[
  {
    path: "contacts/:contactId",
    element: <Contact />,
  },
];
```

注意 `:contactId` URL 段。冒号（`:`）有特殊含义，它将其变成“动态段”。动态段会匹配 URL 中该位置的动态（变化的）值，比如联系人 ID。我们称 URL 中的这些值为“URL Params”，或简称“params”。

这些 [`params`][params] 会被传递给 loader，key 与动态段匹配。例如，我们的段名为 `:contactId`，所以值将作为 `params.contactId` 传递。

这些参数最常用于根据 ID 查找记录。让我们试试看。

👉 **为联系人页面添加 loader 并使用 `useLoaderData` 访问数据**

```jsx filename=src/routes/contact.jsx lines=[1,2,4-6,10]
import { Form, useLoaderData } from "react-router-dom";
import { getContact } from "../contacts";

export async function loader({ params }) {
  const contact = await getContact(params.contactId);
  return { contact };
}

export default function Contact() {
  const { contact } = useLoaderData();
  // existing code
}
```

👉 **在路由上配置 loader**

```jsx filename=src/main.jsx lines=[3,17]
/* existing code */
import Contact, {
  loader as contactLoader,
} from "./routes/contact";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
        loader: contactLoader,
      },
    ],
  },
]);

/* existing code */
```

<img class="tutorial" loading="lazy" src="/_docs/tutorial/10.webp" />

## 更新数据

就像创建数据一样，你用 [`<Form>`][form] 来更新数据。让我们在 `contacts/:contactId/edit` 创建一个新路由。同样，我们从组件开始，然后将其连接到路由配置。

👉 **创建编辑组件**

```
touch src/routes/edit.jsx
```

👉 **添加编辑页 UI**

之前都见过的内容，随意复制粘贴：

```jsx filename=src/routes/edit.jsx
import { Form, useLoaderData } from "react-router-dom";

export default function EditContact() {
  const { contact } = useLoaderData();

  return (
    <Form method="post" id="contact-form">
      <p>
        <span>Name</span>
        <input
          placeholder="First"
          aria-label="First name"
          type="text"
          name="first"
          defaultValue={contact?.first}
        />
        <input
          placeholder="Last"
          aria-label="Last name"
          type="text"
          name="last"
          defaultValue={contact?.last}
        />
      </p>
      <label>
        <span>Twitter</span>
        <input
          type="text"
          name="twitter"
          placeholder="@jack"
          defaultValue={contact?.twitter}
        />
      </label>
      <label>
        <span>Avatar URL</span>
        <input
          placeholder="https://example.com/avatar.jpg"
          aria-label="Avatar URL"
          type="text"
          name="avatar"
          defaultValue={contact?.avatar}
        />
      </label>
      <label>
        <span>Notes</span>
        <textarea
          name="notes"
          defaultValue={contact?.notes}
          rows={6}
        />
      </label>
      <p>
        <button type="submit">Save</button>
        <button type="button">Cancel</button>
      </p>
    </Form>
  );
}
```

👉 **添加新的编辑路由**

```jsx filename=src/main.jsx lines=[2,17-21]
/* existing code */
import EditContact from "./routes/edit";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
        loader: contactLoader,
      },
      {
        path: "contacts/:contactId/edit",
        element: <EditContact />,
        loader: contactLoader,
      },
    ],
  },
]);

/* existing code */
```

我们希望它在根路由的 outlet 中渲染，所以我们将它作为现有子路由的兄弟路由。

（你可能注意到我们为这个路由复用了 `contactLoader`。这只是因为我们在教程中偷懒。没有理由尝试在路由之间共享 loader，它们通常有自己的。）

好了，点击 "Edit" 按钮会给我们这个新 UI：

<img class="tutorial" loading="lazy" src="/_docs/tutorial/11.webp" />

## 使用 FormData 更新联系人

我们刚创建的编辑路由已经渲染了一个表单。我们需要做的就是为路由连接一个 action 来更新记录。表单将 post 到 action，数据将自动重新验证。

👉 **为编辑模块添加 action**

```jsx filename=src/routes/edit.jsx lines=[4,6,8-13]
import {
  Form,
  useLoaderData,
  redirect,
} from "react-router-dom";
import { updateContact } from "../contacts";

export async function action({ request, params }) {
  const formData = await request.formData();
  const updates = Object.fromEntries(formData);
  await updateContact(params.contactId, updates);
  return redirect(`/contacts/${params.contactId}`);
}

/* existing code */
```

👉 **将 action 连接到路由**

```jsx filename=src/main.jsx lines=[3,23]
/* existing code */
import EditContact, {
  action as editAction,
} from "./routes/edit";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      {
        path: "contacts/:contactId",
        element: <Contact />,
        loader: contactLoader,
      },
      {
        path: "contacts/:contactId/edit",
        element: <EditContact />,
        loader: contactLoader,
        action: editAction,
      },
    ],
  },
]);

/* existing code */
```

填写表单，点击保存，你应该会看到类似这样的内容！<small>（只不过更养眼，可能毛发少一些。）</small>

<img class="tutorial" loading="lazy" src="/_docs/tutorial/12.webp" />

## 变更讨论

> 😑 它起作用了，但我完全不知道这里发生了什么...

让我们深入了解一下...

打开 `src/routes/edit.jsx` 并查看表单元素。注意它们每个都有一个 name：

```jsx lines=[5] filename=src/routes/edit.jsx
<input
  placeholder="First"
  aria-label="First name"
  type="text"
  name="first"
  defaultValue={contact.first}
/>
```

没有 JavaScript 时，当表单被提交时，浏览器会创建 [`FormData`][formdata] 并将其作为请求体发送到服务器。如前所述，React Router 阻止了这个行为，将请求发送到你的 action，包括 [`FormData`][formdata]。

表单中的每个字段都可以通过 `formData.get(name)` 访问。例如，给定上面的输入字段，你可以像这样访问名和姓：

```jsx lines=[3,4]
export async function action({ request, params }) {
  const formData = await request.formData();
  const firstName = formData.get("first");
  const lastName = formData.get("last");
  // ...
}
```

由于我们有一些表单字段，我们使用 [`Object.fromEntries`][fromentries] 将它们收集到一个对象中，这正是我们的 `updateContact` 函数需要的。

```jsx lines=[2,3]
const updates = Object.fromEntries(formData);
updates.first; // "Some"
updates.last; // "Name"
```

除了 `action`，我们讨论的这些 API 都不是 React Router 提供的：[`request`][request]、[`request.formData`][requestformdata]、[`Object.fromEntries`][fromentries] 都是 Web 平台提供的。

在我们完成 action 后，注意最后的 [`redirect`][redirect]：

```jsx filename=src/routes/edit.jsx lines=[5]
export async function action({ request, params }) {
  const formData = await request.formData();
  const updates = Object.fromEntries(formData);
  await updateContact(params.contactId, updates);
  return redirect(`/contacts/${params.contactId}`);
}
```

Loader 和 action 都可以[返回 `Response`][returningresponses]（这很合理，因为它们接收的是 [`Request`][request]！）。[`redirect`][redirect] helper 只是让返回一个告诉应用改变位置的 [response][response] 变得更容易。

没有客户端路由时，如果服务器在 POST 请求后重定向，新页面会获取最新数据并渲染。正如我们之前学到的，React Router 模拟了这个模型，并在 action 后自动重新验证页面上的数据。这就是为什么当我们保存表单时侧边栏会自动更新。没有客户端路由时不存在的额外重新验证代码，有了客户端路由同样也不需要！

## 将新记录重定向到编辑页面

现在我们知道如何重定向了，让我们更新创建新联系人的 action，重定向到编辑页面：

👉 **重定向到新记录的编辑页面**

```jsx filename=src/routes/root.jsx lines=[6,12]
import {
  Outlet,
  Link,
  useLoaderData,
  Form,
  redirect,
} from "react-router-dom";
import { getContacts, createContact } from "../contacts";

export async function action() {
  const contact = await createContact();
  return redirect(`/contacts/${contact.id}/edit`);
}
```

现在当我们点击 "New" 时，应该会进入编辑页面：

<img class="tutorial" loading="lazy" src="/_docs/tutorial/13.webp" />

👉 **添加一些记录**

我要用第一届 Remix 大会的豪华演讲嘉宾阵容 😁

<img class="tutorial" loading="lazy" src="/_docs/tutorial/14.webp" />

## 活动链接样式

现在我们有了一堆记录，但不清楚侧边栏中我们正在查看哪一个。我们可以使用 [`NavLink`][navlink] 来解决这个问题。

👉 **在侧边栏中使用 `NavLink`**

```jsx filename=src/routes/root.jsx lines=[3,20-31]
import {
  Outlet,
  NavLink,
  useLoaderData,
  Form,
  redirect,
} from "react-router-dom";

export default function Root() {
  return (
    <>
      <div id="sidebar">
        {/* other code */}

        <nav>
          {contacts.length ? (
            <ul>
              {contacts.map((contact) => (
                <li key={contact.id}>
                  <NavLink
                    to={`contacts/${contact.id}`}
                    className={({ isActive, isPending }) =>
                      isActive
                        ? "active"
                        : isPending
                          ? "pending"
                          : ""
                    }
                  >
                    {/* other code */}
                  </NavLink>
                </li>
              ))}
            </ul>
          ) : (
            <p>{/* other code */}</p>
          )}
        </nav>
      </div>
    </>
  );
}
```

注意我们向 `className` 传递了一个函数。当用户在 `NavLink` 的 URL 上时，`isActive` 为 true。当它*即将*变为活动状态时（数据仍在加载），`isPending` 为 true。这允许我们轻松地指示用户在哪里，以及为已点击但仍在等待数据加载的链接提供即时反馈。

<img class="tutorial" loading="lazy" src="/_docs/tutorial/15.webp"/>

## 全局待定 UI

当用户在应用中导航时，React Router 会在为下一页加载数据时*保留旧页面*。你可能注意到在列表之间点击时应用感觉有点无响应。让我们为用户提供一些反馈，这样应用就不会感觉无响应。

React Router 在幕后管理所有状态，并揭示你构建动态 Web 应用所需的部分。在这种情况下，我们将使用 [`useNavigation`][usenavigation] hook。

👉 **使用 `useNavigation` 添加全局待定 UI**

```jsx filename=src/routes/root.jsx lines=[3,10,17-19]
import {
  // existing code
  useNavigation,
} from "react-router-dom";

// existing code

export default function Root() {
  const { contacts } = useLoaderData();
  const navigation = useNavigation();

  return (
    <>
      <div id="sidebar">{/* existing code */}</div>
      <div
        id="detail"
        className={
          navigation.state === "loading" ? "loading" : ""
        }
      >
        <Outlet />
      </div>
    </>
  );
}
```

[`useNavigation`][usenavigation] 返回当前的导航状态：可以是 `"idle" | "submitting" | "loading"` 之一。

在我们的例子中，如果我们不是空闲状态，就为应用的主要部分添加 `"loading"` 类。然后 CSS 会在短暂延迟后添加一个漂亮的淡入效果（以避免快速加载时 UI 闪烁）。你可以做任何你想做的，比如显示 spinner 或顶部加载条。

<img class="tutorial" loading="lazy" src="/_docs/tutorial/16.webp" />

注意我们的数据模型（`src/contacts.js`）有客户端缓存，所以第二次导航到相同的联系人很快。这个行为*不是* React Router 的功能，无论你之前是否访问过，它都会为变化的路由重新加载数据。但它确实会避免在导航期间为*未变化的*路由（如列表）调用 loader。

## 删除记录

如果我们查看联系人路由中的代码，可以看到删除按钮看起来是这样的：

```jsx filename=src/routes/contact.jsx lines=[3]
<Form
  method="post"
  action="destroy"
  onSubmit={(event) => {
    if (
      !confirm(
        "Please confirm you want to delete this record.",
      )
    ) {
      event.preventDefault();
    }
  }}
>
  <button type="submit">Delete</button>
</Form>
```

注意 `action` 指向 `"destroy"`。就像 `<Link to>` 一样，`<Form action>` 可以使用*相对*值。由于表单在 `contact/:contactId` 中渲染，所以带 `destroy` 的相对 action 将在点击时将表单提交到 `contact/:contactId/destroy`。

此时你应该知道使删除按钮工作所需的一切了。也许在继续之前试试看？你需要：

1. 一个新路由
2. 该路由上的 `action`
3. 来自 `src/contacts.js` 的 `deleteContact`

👉 **创建 "destroy" 路由模块**

```
touch src/routes/destroy.jsx
```

👉 **添加 destroy action**

```jsx filename=src/routes/destroy.jsx
import { redirect } from "react-router-dom";
import { deleteContact } from "../contacts";

export async function action({ params }) {
  await deleteContact(params.contactId);
  return redirect("/");
}
```

👉 **将 destroy 路由添加到路由配置中**

```jsx filename=src/main.jsx lines=[2,10-13]
/* existing code */
import { action as destroyAction } from "./routes/destroy";

const router = createBrowserRouter([
  {
    path: "/",
    /* existing root route props */
    children: [
      /* existing routes */
      {
        path: "contacts/:contactId/destroy",
        action: destroyAction,
      },
    ],
  },
]);

/* existing code */
```

好的，导航到一条记录并点击 "Delete" 按钮。它工作了！

> 😅 我还是不明白为什么这一切能工作

当用户点击提交按钮时：

1. `<Form>` 阻止了浏览器向服务器发送新 POST 请求的默认行为，而是通过客户端路由创建 POST 请求来模拟浏览器
2. `<Form action="destroy">` 匹配 `"contacts/:contactId/destroy"` 的新路由并将请求发送给它
3. action 重定向后，React Router 调用页面上所有数据的 loader 来获取最新值（这就是“重新验证”）。`useLoaderData` 返回新值并导致组件更新！

添加表单，添加 action，React Router 处理其余。

## 上下文错误

只是为了好玩，在 destroy action 中抛出一个错误：

```jsx filename=src/routes/destroy.jsx lines=[2]
export async function action({ params }) {
  throw new Error("oh dang!");
  await deleteContact(params.contactId);
  return redirect("/");
}
```

<img class="tutorial" loading="lazy" src="/_docs/tutorial/17.webp" />

认出这个页面了吗？它就是我们之前的 [`errorElement`][errorelement]。然而，用户除了刷新外没法从这个页面恢复。

让我们为 destroy 路由创建一个上下文错误消息：

```jsx filename=src/main.jsx lines=[6]
[
  /* other routes */
  {
    path: "contacts/:contactId/destroy",
    action: destroyAction,
    errorElement: <div>Oops! There was an error.</div>,
  },
];
```

现在再试试：

<img class="tutorial" loading="lazy" src="/_docs/tutorial/18.webp" />

我们的用户现在有了比狂按刷新更多的选择，他们可以继续与页面中没有问题的部分交互 🙌

因为 destroy 路由有自己的 `errorElement` 且是根路由的子路由，错误会在那里渲染而不是根路由。你可能已经注意到，这些错误会向上冒泡到最近的 `errorElement`。根据需要添加任意数量的 errorElement，只要确保根路由有一个就行。

## 索引路由

当我们加载应用时，你会注意到列表右侧有一大片空白。

<img class="tutorial" loading="lazy" src="/_docs/tutorial/19.webp" />

当路由有子路由且你在父路由的路径上时，`<Outlet>` 没有东西可渲染，因为没有子路由匹配。你可以把索引路由想象为填充该空间的默认子路由。

👉 **创建索引路由模块**

```
touch src/routes/index.jsx
```

👉 **填充索引组件的元素**

随意复制粘贴，这里没有什么特别的。

```jsx filename=src/routes/index.jsx
export default function Index() {
  return (
    <p id="zero-state">
      This is a demo for React Router.
      <br />
      Check out{" "}
      <a href="https://reactrouter.com">
        the docs at reactrouter.com
      </a>
      .
    </p>
  );
}
```

👉 **配置索引路由**

```jsx filename=src/main.jsx lines=[2,12]
// existing code
import Index from "./routes/index";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      { index: true, element: <Index /> },
      /* existing routes */
    ],
  },
]);
```

注意是 [`{ index:true }`][index] 而不是 [`{ path: "" }`][path]。这告诉路由器当用户在父路由的精确路径上时匹配并渲染此路由，这样就没有其他子路由需要在 `<Outlet>` 中渲染。

<img class="tutorial" loading="lazy" src="/_docs/tutorial/20.webp" />

太棒了！不再有空白了。在索引路由上放置仪表盘、统计信息、动态等是很常见的。它们也可以参与数据加载。

## 取消按钮

在编辑页面上我们有一个取消按钮，现在还没有任何功能。我们希望它像浏览器的后退按钮一样工作。

我们需要在按钮上添加点击处理程序以及 React Router 的 [`useNavigate`][usenavigate]。

👉 **使用 `useNavigate` 添加取消按钮点击处理程序**

```jsx filename=src/routes/edit.jsx lines=[5,10,20-22]
import {
  Form,
  useLoaderData,
  redirect,
  useNavigate,
} from "react-router-dom";

export default function EditContact() {
  const { contact } = useLoaderData();
  const navigate = useNavigate();

  return (
    <Form method="post" id="contact-form">
      {/* existing code */}

      <p>
        <button type="submit">Save</button>
        <button
          type="button"
          onClick={() => {
            navigate(-1);
          }}
        >
          Cancel
        </button>
      </p>
    </Form>
  );
}
```

现在当用户点击 "Cancel" 时，他们会被发送回浏览器历史中的上一个条目。

> 🧐 为什么按钮上没有 `event.preventDefault`？

`<button type="button">` 虽然看起来多余，但这是 HTML 中阻止按钮提交表单的方式。

还有两个功能要做。我们已经到最后冲刺阶段了！

## URL 搜索参数和 GET 提交

到目前为止，我们所有的交互 UI 要么是改变 URL 的链接，要么是向 action post 数据的表单。搜索字段很有趣，因为它是两者的混合：它是一个表单，但它只改变 URL，不改变数据。

现在它只是一个普通的 HTML `<form>`，不是 React Router 的 `<Form>`。让我们看看浏览器默认会对它做什么：

👉 **在搜索字段中输入一个名字并按回车键**

注意浏览器的 URL 现在包含你的查询作为 [URLSearchParams][urlsearchparams]：

```
http://127.0.0.1:5173/?q=ryan
```

如果我们查看搜索表单，它看起来是这样的：

```jsx filename=src/routes/root.jsx lines=[1,7]
<form id="search-form" role="search">
  <input
    id="q"
    aria-label="Search contacts"
    placeholder="Search"
    type="search"
    name="q"
  />
  <div id="search-spinner" aria-hidden hidden={true} />
  <div className="sr-only" aria-live="polite"></div>
</form>
```

正如我们之前看到的，浏览器可以通过输入元素的 `name` 属性序列化表单。这个输入框的名称是 `q`，这就是 URL 中有 `?q=` 的原因。如果我们把它命名为 `search`，URL 就会是 `?search=`。

注意这个表单与我们使用的其他表单不同，它没有 `<form method="post">`。默认的 `method` 是 `"get"`。这意味着当浏览器为下一个文档创建请求时，它不会将表单数据放入请求的 POST body 中，而是放入 GET 请求的 [`URLSearchParams`][urlsearchparams] 中。

## 使用客户端路由的 GET 提交

让我们使用客户端路由来提交这个表单并在现有的 loader 中过滤列表。

👉 **将 `<form>` 改为 `<Form>`**

```jsx filename=src/routes/root.jsx lines=[1,11]
<Form id="search-form" role="search">
  <input
    id="q"
    aria-label="Search contacts"
    placeholder="Search"
    type="search"
    name="q"
  />
  <div id="search-spinner" aria-hidden hidden={true} />
  <div className="sr-only" aria-live="polite"></div>
</Form>
```

👉 **如果有 URLSearchParams 则过滤列表**

```jsx filename=src/routes/root.jsx lines=[1,2-4]
export async function loader({ request }) {
  const url = new URL(request.url);
  const q = url.searchParams.get("q");
  const contacts = await getContacts(q);
  return { contacts };
}
```

<img class="tutorial" loading="lazy" src="/_docs/tutorial/21.webp" />

因为这是 GET，不是 POST，React Router *不会*调用 `action`。提交 GET 表单与点击链接相同：只有 URL 改变。这就是为什么我们添加的过滤代码在 `loader` 中，而不是在这个路由的 `action` 中。

这也意味着这是一个正常的页面导航。你可以点击后退按钮回到之前的位置。

## 同步 URL 与表单状态

这里有一些 UX 问题我们可以快速解决。

1. 如果你在搜索后点击后退，表单字段仍然有你输入的值，即使列表不再被过滤。
2. 如果你在搜索后刷新页面，表单字段不再有值，即使列表被过滤了。

换句话说，URL 和我们的表单状态不同步。

👉 **从 loader 返回 `q` 并将其设置为搜索字段的默认值**

```jsx filename=src/routes/root.jsx lines=[7,11,26]
// existing code

export async function loader({ request }) {
  const url = new URL(request.url);
  const q = url.searchParams.get("q");
  const contacts = await getContacts(q);
  return { contacts, q };
}

export default function Root() {
  const { contacts, q } = useLoaderData();
  const navigation = useNavigation();

  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              id="q"
              aria-label="Search contacts"
              placeholder="Search"
              type="search"
              name="q"
              defaultValue={q}
            />
            {/* existing code */}
          </Form>
          {/* existing code */}
        </div>
        {/* existing code */}
      </div>
      {/* existing code */}
    </>
  );
}
```

这解决了问题 (2)。如果你现在刷新页面，输入框会显示查询内容。

<img class="tutorial" loading="lazy" src="/_docs/tutorial/21.webp" />

现在解决问题 (1)，点击后退按钮并更新输入框。我们可以从 React 引入 `useEffect` 来直接操作 DOM 中的表单状态。

👉 **将输入值与 URL 搜索参数同步**

```jsx filename=src/routes/root.jsx lines=[1,9-11]
import { useEffect } from "react";

// existing code

export default function Root() {
  const { contacts, q } = useLoaderData();
  const navigation = useNavigation();

  useEffect(() => {
    document.getElementById("q").value = q;
  }, [q]);

  // existing code
}
```

> 🤔 你不应该用受控组件和 React State 来做这个吗？

你当然可以做成受控组件，但会为相同的行为带来更多复杂性。你不控制 URL，用户通过后退/前进按钮控制。使用受控组件会有更多的同步点。

<details>
<summary>如果你仍然担心，展开查看受控组件的写法</summary>

注意控制输入框现在需要三个同步点而不是一个。行为是相同的，但代码更复杂。

```jsx filename=src/routes/root.jsx lines=[1,6,15,18-20,34-37]
import { useEffect, useState } from "react";
// existing code

export async function loader({ request }) {
  const url = new URL(request.url);
  const q = url.searchParams.get("q") || "";
  const contacts = await getContacts(q);
  return { contacts, q };
}

// existing code

export default function Root() {
  const { contacts, q } = useLoaderData();
  const [query, setQuery] = useState(q);
  const navigation = useNavigation();

  useEffect(() => {
    setQuery(q);
  }, [q]);

  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              id="q"
              aria-label="Search contacts"
              placeholder="Search"
              type="search"
              name="q"
              value={query}
              onChange={(e) => {
                setQuery(e.target.value);
              }}
            />
            {/* existing code */}
          </Form>
          {/* existing code */}
        </div>
        {/* existing code */}
      </div>
    </>
  );
}
```

</details>

## `onChange` 时提交表单

我们需要做一个产品决策。对于这个 UI，我们可能更希望在每次按键时就进行过滤，而不是显式提交表单时。

我们已经见过 `useNavigate` 了，我们将使用它的近亲 [`useSubmit`][usesubmit] 来实现。

```jsx filename=src/routes/root.jsx lines=[4,10,25-27]
// existing code
import {
  // existing code
  useSubmit,
} from "react-router-dom";

export default function Root() {
  const { contacts, q } = useLoaderData();
  const navigation = useNavigation();
  const submit = useSubmit();

  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              id="q"
              aria-label="Search contacts"
              placeholder="Search"
              type="search"
              name="q"
              defaultValue={q}
              onChange={(event) => {
                submit(event.currentTarget.form);
              }}
            />
            {/* existing code */}
          </Form>
          {/* existing code */}
        </div>
        {/* existing code */}
      </div>
      {/* existing code */}
    </>
  );
}
```

现在当你输入时，表单会自动提交！

注意 [`submit`][usesubmit] 的参数。我们传入的是 `event.currentTarget.form`。`currentTarget` 是事件绑定的 DOM 节点，`currentTarget.form` 是输入框的父表单节点。`submit` 函数会序列化并提交你传给它的任何表单。

## 添加搜索 Spinner

在生产应用中，搜索很可能是在数据库中查找记录，数据库太大而无法一次性发送并在客户端过滤。这就是为什么这个演示有一些模拟的网络延迟。

没有加载指示器，搜索感觉有点迟缓。即使我们可以让数据库更快，我们也始终会有用户的网络延迟在路上且无法控制。为了更好的 UX，让我们为搜索添加一些即时的 UI 反馈。为此我们将再次使用 [`useNavigation`][usenavigation]。

👉 **添加搜索 spinner**

```jsx filename=src/routes/root.jsx lines=[8-12,26,32]
// existing code

export default function Root() {
  const { contacts, q } = useLoaderData();
  const navigation = useNavigation();
  const submit = useSubmit();

  const searching =
    navigation.location &&
    new URLSearchParams(navigation.location.search).has(
      "q",
    );

  useEffect(() => {
    document.getElementById("q").value = q;
  }, [q]);

  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              id="q"
              className={searching ? "loading" : ""}
              // existing code
            />
            <div
              id="search-spinner"
              aria-hidden
              hidden={!searching}
            />
            {/* existing code */}
          </Form>
          {/* existing code */}
        </div>
        {/* existing code */}
      </div>
      {/* existing code */}
    </>
  );
}
```

<img class="tutorial" loading="lazy" src="/_docs/tutorial/22.webp" />

当应用正在导航到新 URL 并加载其数据时，`navigation.location` 会出现。当没有待处理的导航时它就消失了。

## 管理历史栈

现在表单在每次按键时都会提交，如果我们输入字符 "seba" 然后用退格键删除它们，我们最终会在历史栈中有 7 个新条目 😂。我们绝对不想这样

<img class="tutorial" loading="lazy" src="/_docs/tutorial/23.webp" />

我们可以通过用下一页*替换*历史栈中的当前条目而不是推入新条目来避免这个问题。

👉 **在 `submit` 中使用 `replace`**

```jsx filename=src/routes/root.jsx lines=[16-19]
// existing code

export default function Root() {
  // existing code

  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              id="q"
              // existing code
              onChange={(event) => {
                const isFirstSearch = q == null;
                submit(event.currentTarget.form, {
                  replace: !isFirstSearch,
                });
              }}
            />
            {/* existing code */}
          </Form>
          {/* existing code */}
        </div>
        {/* existing code */}
      </div>
      {/* existing code */}
    </>
  );
}
```

我们只想替换搜索结果，而不是搜索前的页面，所以我们快速检查这是否是第一次搜索，然后决定是否替换。

现在每次按键不再创建新条目，所以用户可以点击后退退出搜索结果，而不必点击 7 次 😅。

## 无导航的变更

到目前为止，我们所有的变更（我们改变数据的时候）都使用了会导航的表单，在历史栈中创建新条目。虽然这些用户流程很常见，但同样常见的是想在*不引起导航*的情况下改变数据。

对于这些情况，我们有 [`useFetcher`][usefetcher] hook。它允许我们与 loader 和 action 通信而不引起导航。

联系人页面上的 ★ 按钮很适合这个场景。我们不是在创建或删除新记录，我们不想改变页面，我们只是想改变我们正在查看的页面上的数据。

👉 **将 `<Favorite>` 表单改为 fetcher 表单**

```jsx filename=src/routes/contact.jsx lines=[4,10,14,26]
import {
  useLoaderData,
  Form,
  useFetcher,
} from "react-router-dom";

// existing code

function Favorite({ contact }) {
  const fetcher = useFetcher();
  const favorite = contact.favorite;

  return (
    <fetcher.Form method="post">
      <button
        name="favorite"
        value={favorite ? "false" : "true"}
        aria-label={
          favorite
            ? "Remove from favorites"
            : "Add to favorites"
        }
      >
        {favorite ? "★" : "☆"}
      </button>
    </fetcher.Form>
  );
}
```

我们在这里可能想看看那个表单。一如既往，我们的表单有带 `name` prop 的字段。这个表单将发送带有 `favorite` key 的 [`formData`][formdata]，值为 `"true" | "false"`。由于它有 `method="post"`，它将调用 action。由于没有 `<fetcher.Form action="...">` prop，它将 post 到表单渲染所在的路由。

👉 **创建 action**

```jsx filename=src/routes/contact.jsx lines=[2,4-9]
// existing code
import { getContact, updateContact } from "../contacts";

export async function action({ request, params }) {
  const formData = await request.formData();
  return updateContact(params.contactId, {
    favorite: formData.get("favorite") === "true",
  });
}

export default function Contact() {
  // existing code
}
```

相当简单。从请求中提取表单数据并发送到数据模型。

👉 **配置路由的新 action**

```jsx filename=src/main.jsx lines=[4,20]
// existing code
import Contact, {
  loader as contactLoader,
  action as contactAction,
} from "./routes/contact";

const router = createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    errorElement: <ErrorPage />,
    loader: rootLoader,
    action: rootAction,
    children: [
      { index: true, element: <Index /> },
      {
        path: "contacts/:contactId",
        element: <Contact />,
        loader: contactLoader,
        action: contactAction,
      },
      /* existing code */
    ],
  },
]);
```

好了，我们准备好点击用户名旁边的星星了！

<img class="tutorial" loading="lazy" src="/_docs/tutorial/24.webp" />

看看，两个星星都自动更新了。我们新的 `<fetcher.Form method="post">` 的工作方式几乎与我们一直使用的 `<Form>` 完全相同：它调用 action，然后所有数据自动重新验证——甚至你的错误也会以相同的方式被捕获。

但有一个关键区别，它不是导航——URL 不会改变，历史栈不受影响。

## 乐观 UI

你可能注意到，当我们从上一节点击收藏按钮时，应用感觉有点无响应。再次声明，我们添加了一些网络延迟，因为在现实世界中你会有这些！

为了给用户一些反馈，我们可以用 [`fetcher.state`][fetcherstate]（很像之前的 `navigation.state`）将星星置于加载状态，但这次我们可以做得更好。我们可以使用一种称为“乐观 UI”的策略。

fetcher 知道正在提交到 action 的表单数据，所以你可以在 `fetcher.formData` 上获取。我们将用它来立即更新星星的状态，即使网络还没完成。如果更新最终失败，UI 会恢复到真实数据。

👉 **从 `fetcher.formData` 读取乐观值**

```jsx filename=src/routes/contact.jsx lines=[7-9]
// existing code

function Favorite({ contact }) {
  const fetcher = useFetcher();

  const favorite = fetcher.formData
    ? fetcher.formData.get("favorite") === "true"
    : contact.favorite;

  return (
    <fetcher.Form method="post">
      <button
        name="favorite"
        value={favorite ? "false" : "true"}
        aria-label={
          favorite
            ? "Remove from favorites"
            : "Add to favorites"
        }
      >
        {favorite ? "★" : "☆"}
      </button>
    </fetcher.Form>
  );
}
```

如果你现在点击按钮，你应该看到星星*立即*变为新状态。我们不是始终渲染实际数据，而是检查 fetcher 是否有正在提交的 `formData`，如果有，我们就用它来代替。当 action 完成后，`fetcher.formData` 将不再存在，我们回到使用实际数据。所以即使你在乐观 UI 代码中写了 bug，它最终都会回到正确状态 🥹

## 未找到数据

如果我们尝试加载的联系人不存在会怎样？

<img loading="lazy" class="tutorial" src="/_docs/tutorial/25.webp" />

我们的根 [`errorElement`][errorelement] 在我们尝试渲染 `null` 联系人时捕获了这个意外错误。很好，错误被正确处理了，但我们可以做得更好！

当你在 loader 或 action 中有预期的错误情况时——比如数据不存在——你可以 `throw`。调用栈会中断，React Router 会捕获它，并渲染错误路径。我们甚至不会尝试渲染 `null` 联系人。

👉 **在 loader 中抛出 404 响应**

```jsx filename=src/routes/contact.jsx lines=[2-9]
export async function loader({ params }) {
  const contact = await getContact(params.contactId);
  if (!contact) {
    throw new Response("", {
      status: 404,
      statusText: "Not Found",
    });
  }
  return { contact };
}
```

<img loading="lazy" class="tutorial" src="/_docs/tutorial/27.webp" />

不是遇到 `Cannot read properties of null` 的渲染错误，我们完全避免了组件渲染，转而渲染错误路径，告诉用户更具体的信息。

这让你的正常路径保持正常。你的路由元素不需要关心错误和加载状态。

## 无路径路由

最后一件事。我们看到的最后一个错误页面如果在根路由的 outlet 内渲染会更好，而不是整个页面。实际上，所有子路由中的每个错误都在 outlet 中渲染会更好，然后用户有比刷新更多的选择。

我们希望它看起来像这样：

<img loading="lazy" class="tutorial" src="/_docs/tutorial/26.webp" />

我们可以为每个子路由添加 error element，但由于都是相同的错误页面，这不推荐。

有一种更干净的方式。路由可以在*没有*路径的情况下使用，这让它们参与 UI 布局而无需在 URL 中添加新的路径段。看看这个：

👉 **用无路径路由包裹子路由**

```jsx filename=src/main.jsx lines=[9-21]
createBrowserRouter([
  {
    path: "/",
    element: <Root />,
    loader: rootLoader,
    action: rootAction,
    errorElement: <ErrorPage />,
    children: [
      {
        errorElement: <ErrorPage />,
        children: [
          { index: true, element: <Index /> },
          {
            path: "contacts/:contactId",
            element: <Contact />,
            loader: contactLoader,
            action: contactAction,
          },
          /* the rest of the routes */
        ],
      },
    ],
  },
]);
```

当子路由中抛出任何错误时，我们新的无路径路由会捕获它并渲染，保留根路由的 UI！

## JSX 路由

最后一个技巧，很多人喜欢用 JSX 配置路由。你可以用 `createRoutesFromElements` 来实现。使用 JSX 或对象配置路由没有功能上的区别，纯粹是风格偏好。

```jsx
import {
  createRoutesFromElements,
  createBrowserRouter,
  Route,
} from "react-router-dom";

const router = createBrowserRouter(
  createRoutesFromElements(
    <Route
      path="/"
      element={<Root />}
      loader={rootLoader}
      action={rootAction}
      errorElement={<ErrorPage />}
    >
      <Route errorElement={<ErrorPage />}>
        <Route index element={<Index />} />
        <Route
          path="contacts/:contactId"
          element={<Contact />}
          loader={contactLoader}
          action={contactAction}
        />
        <Route
          path="contacts/:contactId/edit"
          element={<EditContact />}
          loader={contactLoader}
          action={editAction}
        />
        <Route
          path="contacts/:contactId/destroy"
          action={destroyAction}
        />
      </Route>
    </Route>,
  ),
);
```

---

就这样！感谢你尝试 React Router。我们希望这个教程能给你一个坚实的开始来构建优秀的用户体验。React Router 还有很多可以做的，所以一定要查看所有的 API 😀

[vite]: https://vitejs.dev/guide/
[node]: https://nodejs.org
[createbrowserrouter]: ../routers/create-browser-router
[route]: ../route/route
[tutorial-css]: https://gist.githubusercontent.com/ryanflorence/ba20d473ef59e1965543fa013ae4163f/raw/499707f25a5690d490c7b3d54c65c65eb895930c/react-router-6.4-tutorial-css.css
[tutorial-data]: https://gist.githubusercontent.com/ryanflorence/1e7f5d3344c0db4a8394292c157cd305/raw/f7ff21e9ae7ffd55bfaaaf320e09c6a08a8a6611/contacts.js
[routeelement]: ../route/route#element
[jim]: https://blog.jim-nielsen.com/
[errorelement]: ../route/error-element
[userouteerror]: ../hooks/use-route-error
[isrouteerrorresponse]: ../utils/is-route-error-response
[outlet]: ../components/outlet
[link]: ../components/link
[setup]: #setup
[loader]: ../route/loader
[useloaderdata]: ../hooks/use-loader-data
[action]: ../route/action
[params]: ../route/loader#params
[form]: ../components/form
[request]: https://developer.mozilla.org/en-US/docs/Web/API/Request
[formdata]: https://developer.mozilla.org/en-US/docs/Web/API/FormData
[fromentries]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries
[requestformdata]: https://developer.mozilla.org/en-US/docs/Web/API/Request/formData
[response]: https://developer.mozilla.org/en-US/docs/Web/API/Response
[redirect]: ../fetch/redirect
[returningresponses]: ../route/loader#returning-responses
[usenavigation]: ../hooks/use-navigation
[index]: ../route/route#index
[path]: ../route/route#path
[usenavigate]: ../hooks/use-navigate
[uselocation]: ../hooks/use-location
[urlsearchparams]: https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams
[usesubmit]: ../hooks/use-submit
[navlink]: ../components/nav-link
[usefetcher]: ../hooks/use-fetcher
[fetcherstate]: ../hooks/use-fetcher#fetcherstate
