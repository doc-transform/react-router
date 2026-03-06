---
title: 通讯录
order: 2
---

# 通讯录

[MODES: framework]

<br />
<br />

我们将构建一个小型但功能丰富的通讯录应用，让你管理联系人。这里没有数据库或其他"生产就绪"的东西，这样我们可以专注于 React Router 提供的功能。如果你跟着一起做，预计需要 30-45 分钟，否则快速浏览即可。

<docs-info>

如果你更喜欢视频，也可以观看我们的 [React Router 教程讲解视频](https://www.youtube.com/watch?v=pw8FAg07kdo) 🎥

</docs-info>

<img class="tutorial" src="/_docs/v7_address_book_tutorial/01.webp" />

👉 **每次看到这个标记，就意味着你需要在应用中执行操作！**

其余内容只是为了帮助你了解信息和加深理解。让我们开始吧。

## 设置

👉 **生成基础模板**

```shellscript nonumber
npx create-react-router@latest --template remix-run/react-router/tutorials/address-book
```

这使用了一个非常基础的模板，但包含了 CSS 和数据模型，这样我们就可以专注于 React Router。

👉 **启动应用**

```shellscript nonumber
# 进入应用目录
cd {你存放应用的位置}

# 如果还没安装依赖的话先安装
npm install

# 启动服务器
npm run dev
```

现在你应该能够打开 [http://localhost:5173][http-localhost-5173] 看到应用在运行了，不过目前还没什么内容。

## 根路由

注意 `app/root.tsx` 文件。这就是我们所说的["根路由"][root-route]。它是 UI 中第一个渲染的组件，因此通常包含页面的全局布局以及默认的[错误边界][error-boundaries]。

<details>

<summary>展开查看根组件代码</summary>

```tsx filename=app/root.tsx
import {
  Form,
  Scripts,
  ScrollRestoration,
  isRouteErrorResponse,
} from "react-router";
import type { Route } from "./+types/root";

import appStylesHref from "./app.css?url";

export default function App() {
  return (
    <>
      <div id="sidebar">
        <h1>React Router Contacts</h1>
        <div>
          <Form id="search-form" role="search">
            <input
              aria-label="Search contacts"
              id="q"
              name="q"
              placeholder="Search"
              type="search"
            />
            <div
              aria-hidden
              hidden={true}
              id="search-spinner"
            />
          </Form>
          <Form method="post">
            <button type="submit">New</button>
          </Form>
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
    </>
  );
}

// Layout 组件是根路由的一个特殊导出。
// 它充当所有路由组件、HydrateFallback 和 ErrorBoundary 的文档"应用外壳"
// 更多信息请参阅 https://reactrouter.com/explanation/special-files#layout-export
export function Layout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <html lang="en">
      <head>
        <meta charSet="utf-8" />
        <meta
          name="viewport"
          content="width=device-width, initial-scale=1"
        />
        <link rel="stylesheet" href={appStylesHref} />
      </head>
      <body>
        {children}
        <ScrollRestoration />
        <Scripts />
      </body>
    </html>
  );
}

// 应用最顶层的错误边界，当应用抛出错误时渲染
// 更多信息请参阅 https://reactrouter.com/start/framework/route-module#errorboundary
export function ErrorBoundary({
  error,
}: Route.ErrorBoundaryProps) {
  let message = "Oops!";
  let details = "An unexpected error occurred.";
  let stack: string | undefined;

  if (isRouteErrorResponse(error)) {
    message = error.status === 404 ? "404" : "Error";
    details =
      error.status === 404
        ? "The requested page could not be found."
        : error.statusText || details;
  } else if (
    import.meta.env.DEV &&
    error &&
    error instanceof Error
  ) {
    details = error.message;
    stack = error.stack;
  }

  return (
    <main id="error-page">
      <h1>{message}</h1>
      <p>{details}</p>
      {stack && (
        <pre>
          <code>{stack}</code>
        </pre>
      )}
    </main>
  );
}
```

</details>

## 联系人路由 UI

如果你点击侧边栏中的某个项目，会看到默认的 404 页面。让我们创建一个匹配 `/contacts/1` URL 的路由。

👉 **创建联系人路由模块**

```shellscript nonumber
mkdir app/routes
touch app/routes/contact.tsx
```

我们可以把这个文件放在任何位置，但为了更好地组织，我们将所有路由放在 `app/routes` 目录内。

你也可以根据喜好使用[基于文件的路由][file-route-conventions]。

👉 **配置路由**

我们需要告诉 React Router 我们的新路由。`routes.ts` 是一个特殊文件，用于配置所有路由。

```tsx filename=routes.ts lines=[2,5]
import type { RouteConfig } from "@react-router/dev/routes";
import { route } from "@react-router/dev/routes";

export default [
  route("contacts/:contactId", "routes/contact.tsx"),
] satisfies RouteConfig;
```

在 React Router 中，`:` 使路径段变为动态的。我们刚刚让以下 URL 匹配 `routes/contact.tsx` 路由模块：

- `/contacts/123`
- `/contacts/abc`

👉 **添加联系人组件 UI**

就是一些元素，你可以直接复制粘贴。

```tsx filename=app/routes/contact.tsx
import { Form } from "react-router";

import type { ContactRecord } from "../data";

export default function Contact() {
  const contact = {
    first: "Your",
    last: "Name",
    avatar: "https://placecats.com/200/200",
    twitter: "your_handle",
    notes: "Some notes",
    favorite: true,
  };

  return (
    <div id="contact">
      <div>
        <img
          alt={`${contact.first} ${contact.last} avatar`}
          key={contact.avatar}
          src={contact.avatar}
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
          )}
          <Favorite contact={contact} />
        </h1>

        {contact.twitter ? (
          <p>
            <a
              href={`https://twitter.com/${contact.twitter}`}
            >
              {contact.twitter}
            </a>
          </p>
        ) : null}

        {contact.notes ? <p>{contact.notes}</p> : null}

        <div>
          <Form action="edit">
            <button type="submit">Edit</button>
          </Form>

          <Form
            action="destroy"
            method="post"
            onSubmit={(event) => {
              const response = confirm(
                "Please confirm you want to delete this record.",
              );
              if (!response) {
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

function Favorite({
  contact,
}: {
  contact: Pick<ContactRecord, "favorite">;
}) {
  const favorite = contact.favorite;

  return (
    <Form method="post">
      <button
        aria-label={
          favorite
            ? "Remove from favorites"
            : "Add to favorites"
        }
        name="favorite"
        value={favorite ? "false" : "true"}
      >
        {favorite ? "★" : "☆"}
      </button>
    </Form>
  );
}
```

现在如果我们点击其中一个链接或访问 [`/contacts/1`][contacts-1]，会发现……没有任何变化？

<img class="tutorial" src="/_docs/v7_address_book_tutorial/02.webp" />

## 嵌套路由和 Outlet

React Router 支持嵌套路由。为了让子路由在父布局内渲染，我们需要在父组件中渲染一个 [`Outlet`][outlet-component]。让我们来修复它，打开 `app/root.tsx` 并在其中渲染一个 Outlet。

👉 **渲染一个 [`<Outlet />`][outlet-component]**

```tsx filename=app/root.tsx lines=[3,15-17]
import {
  Form,
  Outlet,
  Scripts,
  ScrollRestoration,
  isRouteErrorResponse,
} from "react-router";

// 已有的导入和导出

export default function App() {
  return (
    <>
      <div id="sidebar">{/* other elements */}</div>
      <div id="detail">
        <Outlet />
      </div>
    </>
  );
}
```

现在子路由应该通过 Outlet 渲染了。

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/03.webp" />

## 客户端路由

你可能已经注意到了，当我们点击侧边栏中的链接时，浏览器会对下一个 URL 发起完整的文档请求，而不是进行客户端路由，这会导致整个应用完全重新挂载。

客户端路由允许应用更新 URL 而无需重新加载整个页面。相反，应用可以立即渲染新的 UI。让我们用 [`<Link>`][link-component] 来实现。

👉 **将侧边栏的 `<a href>` 改为 `<Link to>`**

```tsx filename=app/root.tsx lines=[3,20,23]
import {
  Form,
  Link,
  Outlet,
  Scripts,
  ScrollRestoration,
  isRouteErrorResponse,
} from "react-router";

// 已有的导入和导出

export default function App() {
  return (
    <>
      <div id="sidebar">
        {/* other elements */}
        <nav>
          <ul>
            <li>
              <Link to={`/contacts/1`}>Your Name</Link>
            </li>
            <li>
              <Link to={`/contacts/2`}>Your Friend</Link>
            </li>
          </ul>
        </nav>
      </div>
      {/* other elements */}
    </>
  );
}
```

你可以在浏览器开发者工具的网络面板中查看，它不再请求文档了。

## 加载数据

URL 路径段、布局和数据往往是紧密耦合的。我们已经可以在这个应用中看到：

| URL 路径段          | 组件        | 数据       |
| ------------------- | ----------- | ---------- |
| /                   | `<App>`     | 联系人列表 |
| contacts/:contactId | `<Contact>` | 单个联系人 |

由于这种天然的耦合关系，React Router 提供了数据约定，可以轻松地将数据传递到路由组件中。

首先，我们将在根路由中创建并导出一个 [`clientLoader`][client-loader] 函数，然后渲染数据。

👉 **从 `app/root.tsx` 导出 `clientLoader` 函数并渲染数据**

<docs-info>以下代码中有类型错误，我们将在下一节中修复</docs-info>

```tsx filename=app/root.tsx lines=[2,6-9,11-12,19-42]
// 已有的导入
import { getContacts } from "./data";

// existing exports

export async function clientLoader() {
  const contacts = await getContacts();
  return { contacts };
}

export default function App({ loaderData }) {
  const { contacts } = loaderData;

  return (
    <>
      <div id="sidebar">
        {/* other elements */}
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
                    )}
                    {contact.favorite ? (
                      <span>★</span>
                    ) : null}
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
      </div>
      {/* other elements */}
    </>
  );
}
```

就是这样！React Router 现在会自动保持数据与 UI 同步。侧边栏现在应该看起来像这样：

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/04.webp" />

你可能会好奇为什么我们在

## 类型安全

你可能注意到我们没有给 `loaderData` prop 指定类型。让我们来修复它。

👉 **给 `App` 组件添加 `ComponentProps` 类型**

```tsx filename=app/root.tsx lines=[5-7]
// 已有的导入
import type { Route } from "./+types/root";
// 已有的导入和导出

export default function App({
  loaderData,
}: Route.ComponentProps) {
  const { contacts } = loaderData;

  // existing code
}
```

等等，什么？这些类型从哪里来的？！

我们没有定义它们，但它们已经知道我们从 `clientLoader` 返回的 `contacts` 属性了。

这是因为 React Router 会[为应用中的每个路由生成类型][type-safety]来提供自动类型安全。

## 添加 `HydrateFallback`

我们之前提到过，我们正在构建一个没有服务端渲染的[单页应用][spa]。如果你查看 [`react-router.config.ts`][react-router-config]，你会看到这是通过一个简单的布尔值来配置的：

```tsx filename=react-router.config.ts lines=[4]
import { type Config } from "@react-router/dev/config";

export default {
  ssr: false,
} satisfies Config;
```

你可能已经开始注意到，每次刷新页面时，在应用加载之前会闪现一下白屏。由于我们只在客户端渲染，在应用加载期间没有任何内容可以展示给用户。

👉 **添加 `HydrateFallback` 导出**

我们可以提供一个在应用注水之前（客户端首次渲染）显示的回退内容，通过 [`HydrateFallback`][hydrate-fallback] 导出。

```tsx filename=app/root.tsx lines=[3-10]
// 已有的导入和导出

export function HydrateFallback() {
  return (
    <div id="loading-splash">
      <div id="loading-splash-spinner" />
      <p>Loading, please wait...</p>
    </div>
  );
}
```

现在刷新页面，你会短暂看到加载闪屏，然后应用就注水完成了。

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/05.webp" />

## 索引路由

当你加载应用并且还没有进入联系人页面时，你会注意到列表右侧有一大片空白。

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/06.webp" />

当一个路由有子路由，而你当前在父路由的路径上时，`<Outlet>` 没有任何内容可以渲染，因为没有子路由匹配。你可以把[索引路由][index-route]理解为填充这个空白的默认子路由。

👉 **为根路由创建索引路由**

```shellscript nonumber
touch app/routes/home.tsx
```

```ts filename=app/routes.ts lines=[2,5]
import type { RouteConfig } from "@react-router/dev/routes";
import { index, route } from "@react-router/dev/routes";

export default [
  index("routes/home.tsx"),
  route("contacts/:contactId", "routes/contact.tsx"),
] satisfies RouteConfig;
```

👉 **填充索引组件的元素**

可以直接复制粘贴，没有什么特别的。

```tsx filename=app/routes/home.tsx
export default function Home() {
  return (
    <p id="index-page">
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

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/07.webp" />

好了！不再有空白了。通常会在索引路由中放置仪表盘、统计数据、信息流等内容。它们也可以参与数据加载。

## 添加 About 路由

在我们开始处理用户可以交互的动态数据之前，让我们添加一个包含静态内容的页面，这些内容我们预计很少变化。一个关于页面正好合适。

👉 **创建 about 路由**

```shellscript nonumber
touch app/routes/about.tsx
```

Don't forget to add the route to `app/routes.ts`:

```tsx filename=app/routes.ts lines=[4]
export default [
  index("routes/home.tsx"),
  route("contacts/:contactId", "routes/contact.tsx"),
  route("about", "routes/about.tsx"),
] satisfies RouteConfig;
```

👉 **Add the about page UI**

Nothing too special here, just copy and paste:

```tsx filename=app/routes/about.tsx
import { Link } from "react-router";

export default function About() {
  return (
    <div id="about">
      <Link to="/">← Go to demo</Link>
      <h1>About React Router Contacts</h1>

      <div>
        <p>
          This is a demo application showing off some of the
          powerful features of React Router, including
          dynamic routing, nested routes, loaders, actions,
          and more.
        </p>

        <h2>Features</h2>
        <p>
          Explore the demo to see how React Router handles:
        </p>
        <ul>
          <li>
            Data loading and mutations with loaders and
            actions
          </li>
          <li>
            Nested routing with parent/child relationships
          </li>
          <li>URL-based routing with dynamic segments</li>
          <li>Pending and optimistic UI</li>
        </ul>

        <h2>Learn More</h2>
        <p>
          Check out the official documentation at{" "}
          <a href="https://reactrouter.com">
            reactrouter.com
          </a>{" "}
          to learn more about building great web
          applications with React Router.
        </p>
      </div>
    </div>
  );
}
```

👉 **Add a link to the about page in the sidebar**

```tsx filename=app/root.tsx lines=[5-7]
export default function App() {
  return (
    <>
      <div id="sidebar">
        <h1>
          <Link to="about">React Router Contacts</Link>
        </h1>
        {/* other elements */}
      </div>
      {/* other elements */}
    </>
  );
}
```

Now navigate to the [about page][about-page] and it should look like this:

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/08.webp" />

## 布局路由

实际上我们不希望 about 页面嵌套在侧边栏布局中。让我们把侧边栏移到一个布局中，这样就可以避免在 about 页面上渲染它。此外，我们还希望避免在 about 页面上加载所有联系人数据。

👉 **创建侧边栏的布局路由**

你可以随意命名和放置这个布局路由，但对于我们这个简单的应用来说，把它放在 `layouts` 目录中有助于保持组织性。

```shellscript nonumber
mkdir app/layouts
touch app/layouts/sidebar.tsx
```

现在只需返回一个 [`<Outlet>`][outlet-component]。

```tsx filename=app/layouts/sidebar.tsx
import { Outlet } from "react-router";

export default function SidebarLayout() {
  return <Outlet />;
}
```

👉 **将路由定义移到侧边栏布局下**

我们可以定义一个 `layout` 路由，自动为其中所有匹配的路由渲染侧边栏。这基本上就是我们之前 `root` 做的事情，但现在我们可以将其限定在特定路由范围内。

```ts filename=app/routes.ts lines=[4,9,12]
import type { RouteConfig } from "@react-router/dev/routes";
import {
  index,
  layout,
  route,
} from "@react-router/dev/routes";

export default [
  layout("layouts/sidebar.tsx", [
    index("routes/home.tsx"),
    route("contacts/:contactId", "routes/contact.tsx"),
  ]),
  route("about", "routes/about.tsx"),
] satisfies RouteConfig;
```

👉 **将布局和数据获取移到侧边栏布局**

我们要将 `clientLoader` 和 `App` 组件内的所有内容移到侧边栏布局中。最终应该像这样：

```tsx filename=app/layouts/sidebar.tsx
import { Form, Link, Outlet } from "react-router";
import { getContacts } from "../data";
import type { Route } from "./+types/sidebar";

export async function clientLoader() {
  const contacts = await getContacts();
  return { contacts };
}

export default function SidebarLayout({
  loaderData,
}: Route.ComponentProps) {
  const { contacts } = loaderData;

  return (
    <>
      <div id="sidebar">
        <h1>
          <Link to="about">React Router Contacts</Link>
        </h1>
        <div>
          <Form id="search-form" role="search">
            <input
              aria-label="Search contacts"
              id="q"
              name="q"
              placeholder="Search"
              type="search"
            />
            <div
              aria-hidden
              hidden={true}
              id="search-spinner"
            />
          </Form>
          <Form method="post">
            <button type="submit">New</button>
          </Form>
        </div>
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
                    )}
                    {contact.favorite ? (
                      <span>★</span>
                    ) : null}
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
      </div>
      <div id="detail">
        <Outlet />
      </div>
    </>
  );
}
```

在 `app/root.tsx` 中，`App` 应该只返回一个 [`<Outlet>`][outlet-component]，所有未使用的导入都可以移除。确保 `root.tsx` 中没有 `clientLoader`。

```tsx filename=app/root.tsx lines=[3-10]
// 已有的导入 and exports

export default function App() {
  return <Outlet />;
}
```

现在调整完成后，我们的 about 页面不再加载联系人数据，也不再嵌套在侧边栏布局中：

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/09.webp" />

## 预渲染静态路由

如果你刷新 about 页面，在客户端渲染之前仍然会短暂看到加载 spinner。这确实不是一个好体验，而且该页面只是静态信息，我们应该能够在构建时将其预渲染为静态 HTML。

👉 **预渲染 about 页面**

在 `react-router.config.ts` 中，我们可以向配置中添加一个 [`prerender`][pre-rendering] 数组，告诉 React Router 在构建时预渲染某些 URL。在这个例子中我们只想预渲染 about 页面。

```ts filename=react-router.config.ts lines=[5]
import { type Config } from "@react-router/dev/config";

export default {
  ssr: false,
  prerender: ["/about"],
} satisfies Config;
```

现在如果你访问 [about 页面][about-page] 并刷新，就不会再看到加载 spinner 了！

<docs-warning>

如果刷新时仍然看到 spinner，请确保你已经删除了 `root.tsx` 中的 `clientLoader`。

</docs-warning>

## 服务端渲染

React Router 是构建[单页应用][spa]的出色框架。许多应用仅靠客户端渲染就能很好地服务用户，*也许*在构建时静态预渲染几个页面。

如果你确实想在 React Router 应用中引入服务端渲染，这非常简单（还记得之前那个 `ssr: false` 布尔值吗？）。

👉 **启用服务端渲染**

```ts filename=react-router.config.ts lines=[2]
export default {
  ssr: true,
  prerender: ["/about"],
} satisfies Config;
```

现在...没什么变化？我们仍然在客户端渲染之前短暂看到 spinner？而且，我们不是还在使用 `clientLoader` 吗，所以数据仍然在客户端获取？

没错！使用 React Router 你仍然可以在你认为合适的地方使用 `clientLoader`（和 `clientAction`）进行客户端数据获取。React Router 给你很大的灵活性来选择正确的工具。

让我们改用 [`loader`][loader]，它（你猜对了）用于在服务器上获取数据。

👉 **改用 `loader` 获取数据**

```tsx filename=app/layouts/sidebar.tsx lines=[3]
// 已有的导入

export async function loader() {
  const contacts = await getContacts();
  return { contacts };
}
```

将 `ssr` 设置为 `true` 还是 `false` 取决于你和用户的需求。两种策略都完全有效。在本教程的剩余部分我们将使用服务端渲染，但请知道所有渲染策略在 React Router 中都是一等公民。

## Loader 中的 URL 参数

👉 **点击侧边栏中的一个链接**

我们应该又能看到之前的静态联系人页面了，不同的是：URL 现在包含了记录的真实 ID。

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/10.webp" />

还记得 `app/routes.ts` 中路由定义的 `:contactId` 部分吗？这些动态片段会匹配 URL 中该位置的动态（变化的）值。我们把 URL 中的这些值称为 "URL Params"，或简称 "params"。

这些 `params` 会以与动态片段匹配的键传递给 loader。例如，我们的片段命名为 `:contactId`，因此值会以 `params.contactId` 的形式传递。

这些参数最常用于根据 ID 查找记录。让我们试试看。

👉 **在联系人页面添加 `loader` 函数并通过 `loaderData` 访问数据**

<docs-info>以下代码包含类型错误，我们将在下一节中修复</docs-info>

```tsx filename=app/routes/contact.tsx lines=[2-3,5-8,10-13]
// 已有的导入
import { getContact } from "../data";
import type { Route } from "./+types/contact";

export async function loader({ params }: Route.LoaderArgs) {
  const contact = await getContact(params.contactId);
  return { contact };
}

export default function Contact({
  loaderData,
}: Route.ComponentProps) {
  const { contact } = loaderData;

  // existing code
}

// existing code
```

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/11.webp" />

## 抛出响应

你会注意到 `loaderData.contact` 的类型是 `ContactRecord | null`。基于我们的自动类型安全，TypeScript 已经知道 `params.contactId` 是一个字符串，但我们没有做任何事情来确保它是一个有效的 ID。由于联系人可能不存在，`getContact` 可能返回 `null`，这就是我们产生类型错误的原因。

我们可以在组件代码中处理联系人未找到的情况，但符合 Web 规范的做法是发送一个正确的 404。我们可以在 loader 中做到这一点，一次性解决所有问题。

```tsx filename=app/routes/contact.tsx lines=[5-7]
// 已有的导入

export async function loader({ params }: Route.LoaderArgs) {
  const contact = await getContact(params.contactId);
  if (!contact) {
    throw new Response("Not Found", { status: 404 });
  }
  return { contact };
}

// existing code
```

现在，如果用户未找到，此路径下的代码执行会停止，React Router 会转而渲染错误路径。React Router 中的组件只需关注正常路径即可 😁

## 数据变更

我们马上就会创建第一个联系人，但首先让我们谈谈 HTML。

React Router 将 HTML Form 导航仿真为数据变更的基本操作，在 JavaScript 大爆发之前这是唯一的方式。不要被其简单性迷惑！React Router 中的表单能让你获得客户端渲染应用的 UX 能力，同时保持“老派”Web 模型的简单性。

虽然某些 Web 开发者可能不熟悉，但 HTML `form` 实际上会在浏览器中引起导航，就像点击链接一样。唯一的区别在于请求：链接只能改变 URL，而 `form` 还可以改变请求方法（`GET` vs. `POST`）和请求体（`POST` 表单数据）。

没有客户端路由时，浏览器会自动序列化 `form` 的数据，并将其作为 `POST` 的请求体发送到服务器，对于 `GET` 则作为 [`URLSearchParams`][url-search-params]。React Router 做的是同样的事情，只是不是将请求发送到服务器，而是使用客户端路由将其发送到路由的 [`action`][action] 函数。

我们可以通过点击应用中的“New”按钮来测试这一点。

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/12.webp" />

React Router 返回了 405，因为服务器上没有代码来处理这个表单导航。

## 创建联系人

我们将通过在根路由中导出一个 `action` 函数来创建新联系人。当用户点击“new”按钮时，表单会 `POST` 到根路由的 action。

👉 **从 `app/root.tsx` 导出一个 `action` 函数**

```tsx filename=app/root.tsx lines=[3,5-8]
// 已有的导入

import { createEmptyContact } from "./data";

export async function action() {
  const contact = await createEmptyContact();
  return { contact };
}

// existing code
```

就这样！点击“New”按钮，你应该会看到一条新记录出现在列表中 🥳

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/13.webp" />

`createEmptyContact` 方法只是创建一个没有姓名、数据或任何内容的空联系人。但它确实还是创建了一条记录，保证！

> 🧐 等一下...侧边栏是怎么更新的？我们在哪里调用了 `action` 函数？重新获取数据的代码在哪里？`useState`、`onSubmit` 和 `useEffect` 在哪里？！

这就是“老派 Web”编程模型发挥作用的地方。[`<Form>`][form-component] 阻止浏览器向服务器发送新的文档 `POST` 请求，而是通过 [`fetch`][fetch] 将请求发送到路由的 `action` 函数。

在 Web 语义中，`POST` 通常意味着某些数据正在变更。按惯例，React Router 使用这个作为提示，在 `action` 完成后自动重新验证页面上的数据。

实际上，由于这一切都只是 HTML 和 HTTP，你可以禁用 JavaScript，整个流程仍然可以正常工作。React Router 不再序列化表单并发起 [`fetch`][fetch] 请求到服务器，而是由浏览器序列化表单并发起文档请求。然后 React Router 会在服务端渲染页面并将其发送下来。无论哪种方式，最终都是相同的 UI。

不过我们会保留 JavaScript，因为我们要打造比旋转图标和静态文档更好的用户体验。

## 更新数据

让我们添加一种方式来填写新记录的信息。

与创建数据一样，你用 [`<Form>`][form-component] 更新数据。让我们在 `app/routes/edit-contact.tsx` 中创建一个新的路由模块。

👉 **创建编辑联系人路由**

```shellscript nonumber
touch app/routes/edit-contact.tsx
```

Don't forget to add the route to `app/routes.ts`:

```tsx filename=app/routes.ts lines=[5-8]
export default [
  layout("layouts/sidebar.tsx", [
    index("routes/home.tsx"),
    route("contacts/:contactId", "routes/contact.tsx"),
    route(
      "contacts/:contactId/edit",
      "routes/edit-contact.tsx",
    ),
  ]),
  route("about", "routes/about.tsx"),
] satisfies RouteConfig;
```

👉 **Add the edit page UI**

Nothing we haven't seen before, feel free to copy/paste:

```tsx filename=app/routes/edit-contact.tsx
import { Form } from "react-router";
import type { Route } from "./+types/edit-contact";

import { getContact } from "../data";

export async function loader({ params }: Route.LoaderArgs) {
  const contact = await getContact(params.contactId);
  if (!contact) {
    throw new Response("Not Found", { status: 404 });
  }
  return { contact };
}

export default function EditContact({
  loaderData,
}: Route.ComponentProps) {
  const { contact } = loaderData;

  return (
    <Form key={contact.id} id="contact-form" method="post">
      <p>
        <span>Name</span>
        <input
          aria-label="First name"
          defaultValue={contact.first}
          name="first"
          placeholder="First"
          type="text"
        />
        <input
          aria-label="Last name"
          defaultValue={contact.last}
          name="last"
          placeholder="Last"
          type="text"
        />
      </p>
      <label>
        <span>Twitter</span>
        <input
          defaultValue={contact.twitter}
          name="twitter"
          placeholder="@jack"
          type="text"
        />
      </label>
      <label>
        <span>Avatar URL</span>
        <input
          aria-label="Avatar URL"
          defaultValue={contact.avatar}
          name="avatar"
          placeholder="https://example.com/avatar.jpg"
          type="text"
        />
      </label>
      <label>
        <span>Notes</span>
        <textarea
          defaultValue={contact.notes}
          name="notes"
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

现在点击你的新记录，然后点击“Edit”按钮。我们应该能看到新路由。

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/14.webp" />

## 用 `FormData` 更新联系人

我们刚创建的编辑路由已经渲染了一个 `form`。我们只需要添加 `action` 函数。React Router 会序列化 `form`，通过 [`fetch`][fetch] `POST` 它，并自动重新验证所有数据。

👉 **向编辑路由添加 `action` 函数**

```tsx filename=app/routes/edit-contact.tsx lines=[1,4,8,6-15]
import { Form, redirect } from "react-router";
// 已有的导入

import { getContact, updateContact } from "../data";

export async function action({
  params,
  request,
}: Route.ActionArgs) {
  const formData = await request.formData();
  const updates = Object.fromEntries(formData);
  await updateContact(params.contactId, updates);
  return redirect(`/contacts/${params.contactId}`);
}

// existing code
```

填写表单，点击保存，你应该能看到类似这样的结果！<small>（只是看起来更舒服，也许有耐心切西瓜。）</small>

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/15.webp" />

## 数据变更讨论

> 😑 它工作了，但我完全不知道这里发生了什么...

让我们深入了解一下...

打开 `app/routes/edit-contact.tsx` 并查看 `form` 元素。注意它们每个都有一个 name：

```tsx filename=app/routes/edit-contact.tsx lines=[4]
<input
  aria-label="First name"
  defaultValue={contact.first}
  name="first"
  placeholder="First"
  type="text"
/>
```

没有 JavaScript 时，当表单提交时，浏览器会创建 [`FormData`][form-data] 并将其设置为请求体发送到服务器。如前所述，React Router 阻止了这一行为，通过 [`fetch`][fetch] 将请求发送到你的 `action` 函数，包括 [`FormData`][form-data]。

`form` 中的每个字段都可以通过 `formData.get(name)` 访问。例如，给定上面的输入字段，你可以这样访问名和姓：

```tsx filename=app/routes/edit-contact.tsx  lines=[6,7] nocopy
export const action = async ({
  params,
  request,
}: ActionFunctionArgs) => {
  const formData = await request.formData();
  const firstName = formData.get("first");
  const lastName = formData.get("last");
  // ...
};
```

由于我们有多个表单字段，我们使用 [`Object.fromEntries`][object-from-entries] 将它们全部收集到一个对象中，这正是我们的 `updateContact` 函数所需要的。

```tsx filename=app/routes/edit-contact.tsx nocopy
const updates = Object.fromEntries(formData);
updates.first; // "Some"
updates.last; // "Name"
```

除了 `action` 函数，我们讨论的这些 API 都不是由 React Router 提供的：[`request`][request]、[`request.formData`][request-form-data]、[`Object.fromEntries`][object-from-entries] 都是 Web 平台提供的。

在我们完成 `action` 后，注意末尾的 [`redirect`][redirect]：

```tsx filename=app/routes/edit-contact.tsx lines=[9]
export async function action({
  params,
  request,
}: Route.ActionArgs) {
  invariant(params.contactId, "Missing contactId param");
  const formData = await request.formData();
  const updates = Object.fromEntries(formData);
  await updateContact(params.contactId, updates);
  return redirect(`/contacts/${params.contactId}`);
}
```

`action` 和 `loader` 函数都可以返回一个 `Response`（很合理，因为它们接收的是一个 [`Request`][request]！）。[`redirect`][redirect] 辅助函数只是使得返回一个告诉应用改变位置的 [`Response`][response] 更容易。

没有客户端路由时，如果服务器在 `POST` 请求后重定向，新页面会获取最新数据并渲染。如我们之前学到的，React Router 模拟这个模型，在 `action` 调用后自动重新验证页面上的数据。这就是为什么我们保存表单时侧边栏会自动更新。没有客户端路由时不需要额外的重新验证代码，所以在 React Router 的客户端路由中也不需要！

最后一件事。没有 JavaScript 时，[`redirect`][redirect] 将是一个普通的重定向。然而，有了 JavaScript 它就是客户端重定向，所以用户不会丢失客户端状态（如滚动位置或组件状态）。

## 重定向新记录到编辑页面

现在我们知道如何重定向了，让我们更新创建新联系人的 action 使其重定向到编辑页面：

👉 **重定向到新记录的编辑页面**

```tsx filename=app/root.tsx lines=[6,12]
import {
  Outlet,
  Scripts,
  ScrollRestoration,
  isRouteErrorResponse,
  redirect,
} from "react-router";
// 已有的导入

export async function action() {
  const contact = await createEmptyContact();
  return redirect(`/contacts/${contact.id}/edit`);
}

// existing code
```

现在当我们点击“New”时，应该会进入编辑页面：

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/16.webp" />

## 活跃链接样式

现在我们有了很多记录，但侧边栏中不清楚我们正在查看哪一个。我们可以使用 [`NavLink`][nav-link] 来解决这个问题。

👉 **在侧边栏中用 `<NavLink>` 替换 `<Link>`**

```tsx filename=app/layouts/sidebar.tsx lines=[1,17-26,28]
import { Form, Link, NavLink, Outlet } from "react-router";

// 已有的导入 and exports

export default function SidebarLayout({
  loaderData,
}: Route.ComponentProps) {
  const { contacts } = loaderData;

  return (
    <>
      <div id="sidebar">
        {/* existing elements */}
        <ul>
          {contacts.map((contact) => (
            <li key={contact.id}>
              <NavLink
                className={({ isActive, isPending }) =>
                  isActive
                    ? "active"
                    : isPending
                      ? "pending"
                      : ""
                }
                to={`contacts/${contact.id}`}
              >
                {/* existing elements */}
              </NavLink>
            </li>
          ))}
        </ul>
        {/* existing elements */}
      </div>
      {/* existing elements */}
    </>
  );
}
```

注意我们传递了一个函数给 `className`。当用户位于与 `<NavLink to>` 匹配的 URL 时，`isActive` 为 true。当它*即将*变为活跃状态（数据仍在加载）时 `isPending` 为 true。这让我们可以轻松指示用户当前位置，并在点击链接但数据需要加载时提供即时反馈。

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/17.webp" />

## 全局加载 UI

当用户在应用中导航时，React Router 会*保留旧页面*直到下一个页面的数据加载完成。你可能已经注意到，在列表之间点击时应用感觉有点迟铝。让我们给用户一些反馈，这样应用就不会感觉无响应。

React Router 在幕后管理所有状态，并揭示你构建动态 Web 应用所需的部分。在这个例子中，我们将使用 [`useNavigation`][use-navigation] hook。

👉 **使用 `useNavigation` 添加全局加载 UI**

```tsx filename=app/layouts/sidebar.tsx lines=[6,13,19-21]
import {
  Form,
  Link,
  NavLink,
  Outlet,
  useNavigation,
} from "react-router";

export default function SidebarLayout({
  loaderData,
}: Route.ComponentProps) {
  const { contacts } = loaderData;
  const navigation = useNavigation();

  return (
    <>
      {/* existing elements */}
      <div
        className={
          navigation.state === "loading" ? "loading" : ""
        }
        id="detail"
      >
        <Outlet />
      </div>
    </>
  );
}
```

[`useNavigation`][use-navigation] 返回当前的导航状态：可以是 `"idle"`、`"loading"` 或 `"submitting"` 之一。

在我们的例子中，如果不是空闲状态，我们就给应用的主体部分添加一个 `"loading"` 类名。CSS 会在短暂延迟后添加一个淡出效果（以避免快速加载时 UI 闪烁）。你也可以做任何你想做的事情，比如显示一个 spinner 或在顶部显示加载条。

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/18.webp" />

## 删除记录

如果我们查看联系人路由中的代码，可以找到删除按钮像这样：

```tsx filename=app/routes/contact.tsx lines=[2]
<Form
  action="destroy"
  method="post"
  onSubmit={(event) => {
    const response = confirm(
      "Please confirm you want to delete this record.",
    );
    if (!response) {
      event.preventDefault();
    }
  }}
>
  <button type="submit">Delete</button>
</Form>
```

注意 `action` 指向 `"destroy"`。像 `<Link to>` 一样，`<Form action>` 可以接受*相对*值。由于表单在路由 `contacts/:contactId` 中渲染，一个相对 action `destroy` 在点击时会将表单提交到 `contacts/:contactId/destroy`。

到这里你应该已经知道让删除按钮工作所需的一切了。也许在继续之前试试看？你需要：

1. 一个新路由
2. 该路由上的 `action`
3. `app/data.ts` 中的 `deleteContact`
4. 完成后 `redirect` 到某处

👉 **配置 "destroy" 路由模块**

```shellscript nonumber
touch app/routes/destroy-contact.tsx
```

```tsx filename=app/routes.ts lines=[3-6]
export default [
  // existing routes
  route(
    "contacts/:contactId/destroy",
    "routes/destroy-contact.tsx",
  ),
  // existing routes
] satisfies RouteConfig;
```

👉 **添加 destroy action**

```tsx filename=app/routes/destroy-contact.tsx
import { redirect } from "react-router";
import type { Route } from "./+types/destroy-contact";

import { deleteContact } from "../data";

export async function action({ params }: Route.ActionArgs) {
  await deleteContact(params.contactId);
  return redirect("/");
}
```

好的，导航到一条记录并点击“Delete”按钮。它工作了！

> 😅 我还是不明白这一切为什么能工作

当用户点击提交按钮时：

1. `<Form>` 阻止了浏览器向服务器发送新文档 `POST` 请求的默认行为，而是通过客户端路由和 [`fetch`][fetch] 创建一个 `POST` 请求来模拟浏览器
2. `<Form action="destroy">` 匹配了 `contacts/:contactId/destroy` 的新路由并将请求发送给它
3. `action` 重定向后，React Router 调用页面上所有数据的 `loader` 获取最新值（这就是“重新验证”）。`routes/contact.tsx` 中的 `loaderData` 现在有了新值，导致组件更新！

添加一个 `Form`，添加一个 `action`，React Router 处理其余的一切。

## 取消按钮

在编辑页面我们有一个取消按钮，目前它还没有任何功能。我们希望它能做和浏览器后退按钮一样的事情。

我们需要在按钮上添加一个点击处理器，以及 [`useNavigate`][use-navigate]。

👉 **使用 `useNavigate` 添加取消按钮点击处理器**

```tsx filename=app/routes/edit-contact.tsx lines=[1,8,15]
import { Form, redirect, useNavigate } from "react-router";
// 已有的导入和导出

export default function EditContact({
  loaderData,
}: Route.ComponentProps) {
  const { contact } = loaderData;
  const navigate = useNavigate();

  return (
    <Form key={contact.id} id="contact-form" method="post">
      {/* existing elements */}
      <p>
        <button type="submit">Save</button>
        <button onClick={() => navigate(-1)} type="button">
          Cancel
        </button>
      </p>
    </Form>
  );
}
```

现在当用户点击“Cancel”时，他们会被送回浏览器历史记录的上一条。

> 🧐 为什么按钮上没有 `event.preventDefault()`？

`<button type="button">` 虽然看起来多余，但它是 HTML 中阻止按钮提交其所在表单的方式。

还有两个功能要做。我们已经到了最后冲刺阶段！

## `URLSearchParams` 和 `GET` 提交

到目前为止，我们的所有交互 UI 要么是改变 URL 的链接，要么是向 `action` 函数提交数据的 `form`。搜索字段很有趣，因为它是两者的结合：它是一个 `form`，但它只改变 URL，不改变数据。

让我们看看提交搜索表单时会发生什么：

👉 **在搜索字段中输入一个名字并按回车键**

注意浏览器的 URL 现在包含你的查询作为 [`URLSearchParams`][url-search-params]：

```
http://localhost:5173/?q=ryan
```

由于它不是 `<Form method="post">`，React Router 会将 [`FormData`][form-data] 序列化为 [`URLSearchParams`][url-search-params] 而不是请求体。

`loader` 函数可以从 `request` 中访问搜索参数。让我们用它来过滤列表：

👉 **如果有 `URLSearchParams` 则过滤列表**

```tsx filename=app/layouts/sidebar.tsx lines=[3-8]
// 已有的导入和导出

export async function loader({
  request,
}: Route.LoaderArgs) {
  const url = new URL(request.url);
  const q = url.searchParams.get("q");
  const contacts = await getContacts(q);
  return { contacts };
}

// existing code
```

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/19.webp" />

因为这是 `GET` 而不是 `POST`，React Router *不会*调用 `action` 函数。提交 `GET` `form` 与点击链接相同：只有 URL 发生变化。

这也意味着它是一个正常的页面导航。你可以点击后退按钮回到之前的位置。

## 同步 URL 与表单状态

这里有一些 UX 问题我们可以快速解决。

1. 如果你在搜索后点击后退，表单字段仍然保留你输入的值，即使列表不再被过滤。
2. 如果你在搜索后刷新页面，表单字段不再有值，即使列表已被过滤。

换句话说，URL 和我们输入框的状态不同步。

让我们先解决（2），用 URL 中的值来初始化输入框。

👉 **从 `loader` 返回 `q`，将其设置为输入框的默认值**

```tsx filename=app/layouts/sidebar.tsx lines=[9,15,26]
// 已有的导入和导出

export async function loader({
  request,
}: Route.LoaderArgs) {
  const url = new URL(request.url);
  const q = url.searchParams.get("q");
  const contacts = await getContacts(q);
  return { contacts, q };
}

export default function SidebarLayout({
  loaderData,
}: Route.ComponentProps) {
  const { contacts, q } = loaderData;
  const navigation = useNavigation();

  return (
    <>
      <div id="sidebar">
        {/* existing elements */}
        <div>
          <Form id="search-form" role="search">
            <input
              aria-label="Search contacts"
              defaultValue={q || ""}
              id="q"
              name="q"
              placeholder="Search"
              type="search"
            />
            {/* existing elements */}
          </Form>
          {/* existing elements */}
        </div>
        {/* existing elements */}
      </div>
      {/* existing elements */}
    </>
  );
}
```

如果你在搜索后刷新页面，输入框现在会显示查询内容。

现在解决问题（1），点击后退按钮并更新输入框。我们可以从 React 引入 `useEffect` 来直接操作 DOM 中输入框的值。

👉 **同步输入值与 `URLSearchParams`**

```tsx filename=app/layouts/sidebar.tsx lines=[2,12-17]
// 已有的导入
import { useEffect } from "react";

// 已有的导入和导出

export default function SidebarLayout({
  loaderData,
}: Route.ComponentProps) {
  const { contacts, q } = loaderData;
  const navigation = useNavigation();

  useEffect(() => {
    const searchField = document.getElementById("q");
    if (searchField instanceof HTMLInputElement) {
      searchField.value = q || "";
    }
  }, [q]);

  // existing code
}
```

> 🤔 不应该使用受控组件和 React State 来做这件事吗？

你当然可以将其作为受控组件来做。你会有更多的同步点，但这取决于你。

<details>

<summary>展开查看具体实现</summary>

```tsx filename=app/layouts/sidebar.tsx lines=[2,11-12,14-18,30-33,36-37]
// 已有的导入
import { useEffect, useState } from "react";

// 已有的导入和导出

export default function SidebarLayout({
  loaderData,
}: Route.ComponentProps) {
  const { contacts, q } = loaderData;
  const navigation = useNavigation();
  // the query now needs to be kept in state
  const [query, setQuery] = useState(q || "");

  // we still have a `useEffect` to synchronize the query
  // to the component state on back/forward button clicks
  useEffect(() => {
    setQuery(q || "");
  }, [q]);

  return (
    <>
      <div id="sidebar">
        {/* existing elements */}
        <div>
          <Form id="search-form" role="search">
            <input
              aria-label="Search contacts"
              id="q"
              name="q"
              // synchronize user's input to component state
              onChange={(event) =>
                setQuery(event.currentTarget.value)
              }
              placeholder="Search"
              type="search"
              // switched to `value` from `defaultValue`
              value={query}
            />
            {/* existing elements */}
          </Form>
          {/* existing elements */}
        </div>
        {/* existing elements */}
      </div>
      {/* existing elements */}
    </>
  );
}
```

</details>

好的，现在你应该能够点击后退/前进/刷新按钮，输入框的值应该与 URL 和结果保持同步。

## 在 `onChange` 时提交 `Form`

我们这里有一个产品决策要做。有时你希望用户提交 `form` 来过滤某些结果，有时你希望在用户输入时就进行过滤。我们已经实现了第一种，让我们看看第二种是什么样的。

我们已经见过 `useNavigate` 了，我们将使用它的表亲 [`useSubmit`][use-submit] 来实现这个功能。

```tsx filename=app/layouts/sidebar.tsx lines=[7,16,27-29]
import {
  Form,
  Link,
  NavLink,
  Outlet,
  useNavigation,
  useSubmit,
} from "react-router";
// 已有的导入和导出

export default function SidebarLayout({
  loaderData,
}: Route.ComponentProps) {
  const { contacts, q } = loaderData;
  const navigation = useNavigation();
  const submit = useSubmit();

  // existing code

  return (
    <>
      <div id="sidebar">
        {/* existing elements */}
        <div>
          <Form
            id="search-form"
            onChange={(event) =>
              submit(event.currentTarget)
            }
            role="search"
          >
            {/* existing elements */}
          </Form>
          {/* existing elements */}
        </div>
        {/* existing elements */}
      </div>
      {/* existing elements */}
    </>
  );
}
```

现在输入时，`form` 会自动提交！

注意 [`submit`][use-submit] 的参数。`submit` 函数会序列化并提交你传给它的任何表单。我们传入的是 `event.currentTarget`。`currentTarget` 是事件附加到的 DOM 节点（即 `form`）。

## 添加搜索 Spinner

在生产应用中，这个搜索可能会在数据库中查找记录，数据库可能太大而无法一次性发送并在客户端过滤。这就是为什么这个演示有一些模拟的网络延迟。

没有任何加载指示器，搜索感觉有点迟铝。即使我们能让数据库更快，用户的网络延迟始终存在且不可控。

为了更好的用户体验，让我们为搜索添加一些即时的 UI 反馈。我们将再次使用 [`useNavigation`][use-navigation]。

👉 **添加一个变量来判断是否正在搜索**

```tsx filename=app/layouts/sidebar.tsx lines=[9-13]
// 已有的导入和导出

export default function SidebarLayout({
  loaderData,
}: Route.ComponentProps) {
  const { contacts, q } = loaderData;
  const navigation = useNavigation();
  const submit = useSubmit();
  const searching =
    navigation.location &&
    new URLSearchParams(navigation.location.search).has(
      "q",
    );

  // existing code
}
```

当没有任何事情发生时，`navigation.location` 将是 `undefined`，但当用户导航时，它会在数据加载时填充为下一个位置。然后我们通过 `location.search` 检查用户是否正在搜索。

👉 **使用新的 `searching` 状态向搜索表单元素添加类名**

```tsx filename=app/layouts/sidebar.tsx lines=[22,31]
// 已有的导入和导出

export default function SidebarLayout({
  loaderData,
}: Route.ComponentProps) {
  // existing code

  return (
    <>
      <div id="sidebar">
        {/* existing elements */}
        <div>
          <Form
            id="search-form"
            onChange={(event) =>
              submit(event.currentTarget)
            }
            role="search"
          >
            <input
              aria-label="Search contacts"
              className={searching ? "loading" : ""}
              defaultValue={q || ""}
              id="q"
              name="q"
              placeholder="Search"
              type="search"
            />
            <div
              aria-hidden
              hidden={!searching}
              id="search-spinner"
            />
          </Form>
          {/* existing elements */}
        </div>
        {/* existing elements */}
      </div>
      {/* existing elements */}
    </>
  );
}
```

额外加分项，搜索时避免淡出主屏幕：

```tsx filename=app/layouts/sidebar.tsx lines=[13]
// 已有的导入和导出

export default function SidebarLayout({
  loaderData,
}: Route.ComponentProps) {
  // existing code

  return (
    <>
      {/* existing elements */}
      <div
        className={
          navigation.state === "loading" && !searching
            ? "loading"
            : ""
        }
        id="detail"
      >
        <Outlet />
      </div>
      {/* existing elements */}
    </>
  );
}
```

现在你应该可以在搜索输入框左侧看到一个漂亮的 spinner。

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/20.webp" />

## 管理历史栈

由于每次按键都会提交表单，输入字符“alex”然后用退格键删除它们会产生巨大的历史栈 😂。我们绝对不希望这样：

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/21.webp" />

我们可以通过用下一个页面*替换*历史栈中的当前条目而不是推入新条目来避免这种情况。

👉 **在 `submit` 中使用 `replace`**

```tsx filename=app/layouts/sidebar.tsx lines=[16-19]
// 已有的导入和导出

export default function SidebarLayout({
  loaderData,
}: Route.ComponentProps) {
  // existing code

  return (
    <>
      <div id="sidebar">
        {/* existing elements */}
        <div>
          <Form
            id="search-form"
            onChange={(event) => {
              const isFirstSearch = q === null;
              submit(event.currentTarget, {
                replace: !isFirstSearch,
              });
            }}
            role="search"
          >
            {/* existing elements */}
          </Form>
          {/* existing elements */}
        </div>
        {/* existing elements */}
      </div>
      {/* existing elements */}
    </>
  );
}
```

快速检查这是否是第一次搜索后，我们决定是否替换。现在第一次搜索会添加一个新条目，但之后的每次按键都会替换当前条目。用户不再需要点击 7 次后退来删除搜索，只需点击一次后退即可。

## 无导航的 `Form`

到目前为止，我们所有的表单都会改变 URL。虽然这些用户流程很常见，但同样常见的是希望提交表单*而不*引起导航。

对于这些情况，我们有 [`useFetcher`][use-fetcher]。它允许我们与 `action` 和 `loader` 通信而不引起导航。

联系人页面上的 ★ 按钮就适合这种情况。我们不是在创建或删除新记录，也不想改变页面。我们只是想改变我们正在查看的页面上的数据。

👉 **将 `<Favorite>` 表单改为 fetcher 表单**

```tsx filename=app/routes/contact.tsx lines=[1,10,14,26]
import { Form, useFetcher } from "react-router";

// 已有的导入和导出

function Favorite({
  contact,
}: {
  contact: Pick<ContactRecord, "favorite">;
}) {
  const fetcher = useFetcher();
  const favorite = contact.favorite;

  return (
    <fetcher.Form method="post">
      <button
        aria-label={
          favorite
            ? "Remove from favorites"
            : "Add to favorites"
        }
        name="favorite"
        value={favorite ? "false" : "true"}
      >
        {favorite ? "★" : "☆"}
      </button>
    </fetcher.Form>
  );
}
```

这个表单不再引起导航，而是简单地 fetch 到 `action`。说到 action...在我们创建 `action` 之前这不会工作。

👉 **创建 `action`**

```tsx filename=app/routes/contact.tsx lines=[2,5-13]
// existing imports
import { getContact, updateContact } from "../data";
// existing imports

export async function action({
  params,
  request,
}: Route.ActionArgs) {
  const formData = await request.formData();
  return updateContact(params.contactId, {
    favorite: formData.get("favorite") === "true",
  });
}

// existing code
```

好的，我们准备好点击用户名旁边的星标了！

<img class="tutorial" loading="lazy" src="/_docs/v7_address_book_tutorial/22.webp" />

看看，两个星标都自动更新了。我们的新 `<fetcher.Form method="post">` 工作方式几乎与我们一直使用的 `<Form>` 完全相同：它调用 action 然后所有数据自动重新验证——甚至你的错误也会以相同的方式被捕获。

但有一个关键区别，它不是导航，所以 URL 不会改变，历史栈也不受影响。

## 乐观 UI

你可能注意到，当我们从上一节点击收藏按钮时，应用感觉有点无响应。再次说明，我们添加了一些网络延迟，因为在真实世界中你会遇到这种情况。

为了给用户一些反馈，我们可以用 `fetcher.state` 将星标置为加载状态（很像之前的 `navigation.state`），但这次我们可以做得更好。我们可以使用一种叫做“乐观 UI”的策略。

fetcher 知道提交给 `action` 的 [`FormData`][form-data]，所以可以通过 `fetcher.formData` 获取。我们将使用它来立即更新星标的状态，即使网络还没有完成。如果更新最终失败，UI 会恢复到真实数据。

👉 **从 `fetcher.formData` 读取乐观值**

```tsx filename=app/routes/contact.tsx lines=[9-11]
// existing code

function Favorite({
  contact,
}: {
  contact: Pick<ContactRecord, "favorite">;
}) {
  const fetcher = useFetcher();
  const favorite = fetcher.formData
    ? fetcher.formData.get("favorite") === "true"
    : contact.favorite;

  return (
    <fetcher.Form method="post">
      <button
        aria-label={
          favorite
            ? "Remove from favorites"
            : "Add to favorites"
        }
        name="favorite"
        value={favorite ? "false" : "true"}
      >
        {favorite ? "★" : "☆"}
      </button>
    </fetcher.Form>
  );
}
```

现在当你点击星标时，它会*立即*变为新状态。

---

就这样！感谢你尝试 React Router。我们希望这个教程能给你一个坚实的起点来构建优秀的用户体验。你还可以做很多其他事情，所以一定要查看所有的 [API][react-router-apis] 😀

[http-localhost-5173]: http://localhost:5173
[root-route]: ../explanation/special-files#roottsx
[error-boundaries]: ../how-to/error-boundary
[links]: ../start/framework/route-module#links
[outlet-component]: https://api.reactrouter.com/v7/functions/react-router.Outlet
[file-route-conventions]: ../how-to/file-route-conventions
[contacts-1]: http://localhost:5173/contacts/1
[link-component]: https://api.reactrouter.com/v7/functions/react-router.Link
[client-loader]: ../start/framework/route-module#clientloader
[spa]: ../how-to/spa
[type-safety]: ../explanation/type-safety
[react-router-config]: ../explanation/special-files#react-routerconfigts
[rendering-strategies]: ../start/framework/rendering
[index-route]: ../start/framework/routing#index-routes
[layout-route]: ../start/framework/routing#layout-routes
[hydrate-fallback]: ../start/framework/route-module#hydratefallback
[about-page]: http://localhost:5173/about
[pre-rendering]: ../how-to/pre-rendering
[url-search-params]: https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams
[loader]: ../start/framework/route-module#loader
[action]: ../start/framework/route-module#action
[form-component]: https://api.reactrouter.com/v7/functions/react-router.Form
[fetch]: https://developer.mozilla.org/en-US/docs/Web/API/fetch
[form-data]: https://developer.mozilla.org/en-US/docs/Web/API/FormData
[object-from-entries]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries
[request-form-data]: https://developer.mozilla.org/en-US/docs/Web/API/Request/formData
[request]: https://developer.mozilla.org/en-US/docs/Web/API/Request
[redirect]: https://api.reactrouter.com/v7/functions/react-router.redirect
[response]: https://developer.mozilla.org/en-US/docs/Web/API/Response
[nav-link]: https://api.reactrouter.com/v7/functions/react-router.NavLink
[use-navigation]: https://api.reactrouter.com/v7/functions/react-router.useNavigation
[use-navigate]: https://api.reactrouter.com/v7/functions/react-router.useNavigate
[use-submit]: https://api.reactrouter.com/v7/functions/react-router.useSubmit
[use-fetcher]: https://api.reactrouter.com/v7/functions/react-router.useFetcher
[react-router-apis]: https://api.reactrouter.com/v7/modules/react_router
