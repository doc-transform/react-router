---
title: 会话与 Cookie
---

# 会话与 Cookie

[MODES: framework, data]

## 会话

会话是网站的重要组成部分，允许服务器识别来自同一用户的请求，特别是在服务端表单验证或页面上没有 JavaScript 时。会话是许多允许用户"登录"的站点的基本构建块，包括社交、电商、商务和教育网站。

在使用 React Router 作为框架时，会话是在路由级别（而非像 express 中间件那样）在 `loader` 和 `action` 方法中使用"会话存储"对象（实现 [`SessionStorage`][session-storage] 接口）来管理的。会话存储知道如何解析和生成 cookie，以及如何在数据库或文件系统中存储会话数据。

### 使用会话

这是一个 cookie 会话存储的示例：

```ts filename=app/sessions.server.ts
import { createCookieSessionStorage } from "react-router";

type SessionData = {
  userId: string;
};

type SessionFlashData = {
  error: string;
};

const { getSession, commitSession, destroySession } =
  createCookieSessionStorage<SessionData, SessionFlashData>(
    {
      // 来自 `createCookie` 的 Cookie 或用于创建的 CookieOptions
      cookie: {
        name: "__session",

        // 以下都是可选的
        domain: "reactrouter.com",
        // Expires 也可以设置（但当与 maxAge 一起使用时，maxAge 会覆盖它）。
        // 注意不推荐此方法，因为 `new Date` 只在每次服务器部署时创建一个日期，
        // 而不是未来的动态日期！
        //
        // expires: new Date(Date.now() + 60_000),
        httpOnly: true,
        maxAge: 60,
        path: "/",
        sameSite: "lax",
        secrets: ["s3cret1"],
        secure: true,
      },
    },
  );

export { getSession, commitSession, destroySession };
```

我们建议在 `app/sessions.server.ts` 中设置会话存储对象，这样所有需要访问会话数据的路由都可以从同一位置导入。

会话存储对象的输入/输出是 HTTP cookie。`getSession()` 从传入请求的 `Cookie` 头中获取当前会话，而 `commitSession()`/`destroySession()` 提供传出响应的 `Set-Cookie` 头。

你会在 `loader` 和 `action` 函数中使用这些方法来访问会话。

通过 `getSession` 获取会话后，返回的会话对象有一系列方法和属性：

```tsx
export async function action({
  request,
}: ActionFunctionArgs) {
  const session = await getSession(
    request.headers.get("Cookie"),
  );
  session.get("foo");
  session.has("bar");
  // 等等
}
```

查看 [Session API][session-api] 了解会话对象上所有可用的方法。

### 登录表单示例

登录表单可能看起来像这样：

```tsx filename=app/routes/login.tsx lines=[4-7,12-14,16,22,25,33-35,46,51,56,61]
import { data, redirect } from "react-router";
import type { Route } from "./+types/login";

import {
  getSession,
  commitSession,
} from "../sessions.server";

export async function loader({
  request,
}: Route.LoaderArgs) {
  const session = await getSession(
    request.headers.get("Cookie"),
  );

  if (session.has("userId")) {
    // 如果已登录则重定向到首页。
    return redirect("/");
  }

  return data(
    { error: session.get("error") },
    {
      headers: {
        "Set-Cookie": await commitSession(session),
      },
    },
  );
}

export async function action({
  request,
}: Route.ActionArgs) {
  const session = await getSession(
    request.headers.get("Cookie"),
  );
  const form = await request.formData();
  const username = form.get("username");
  const password = form.get("password");

  const userId = await validateCredentials(
    username,
    password,
  );

  if (userId == null) {
    session.flash("error", "用户名或密码无效");

    // 带着错误信息重定向回登录页面。
    return redirect("/login", {
      headers: {
        "Set-Cookie": await commitSession(session),
      },
    });
  }

  session.set("userId", userId);

  // 登录成功，将他们发送到首页。
  return redirect("/", {
    headers: {
      "Set-Cookie": await commitSession(session),
    },
  });
}

export default function Login({
  loaderData,
}: Route.ComponentProps) {
  const { error } = loaderData;

  return (
    <div>
      {error ? <div className="error">{error}</div> : null}
      <form method="POST">
        <div>
          <p>请登录</p>
        </div>
        <label>
          用户名: <input type="text" name="username" />
        </label>
        <label>
          密码: <input type="password" name="password" />
        </label>
      </form>
    </div>
  );
}
```

退出登录表单可能看起来像这样：

```tsx filename=app/routes/logout.tsx
import {
  getSession,
  destroySession,
} from "../sessions.server";
import type { Route } from "./+types/logout";

export async function action({
  request,
}: Route.ActionArgs) {
  const session = await getSession(
    request.headers.get("Cookie"),
  );
  return redirect("/login", {
    headers: {
      "Set-Cookie": await destroySession(session),
    },
  });
}

export default function LogoutRoute() {
  return (
    <>
      <p>确定要退出登录吗？</p>
      <Form method="post">
        <button>退出登录</button>
      </Form>
      <Link to="/">算了</Link>
    </>
  );
}
```

<docs-warning>重要的是要在 `action` 中执行退出登录（或执行任何数据变更操作），而不是 `loader` 中。否则你的用户将面临[跨站请求伪造 (CSRF)][csrf] 攻击的风险。</docs-warning>

### 会话注意事项

由于嵌套路由的存在，多个 loader 可以被调用来构建单个页面。当使用 `session.flash()` 或 `session.unset()` 时，你需要确保请求中没有其他 loader 会读取它，否则会出现竞态条件。通常如果你使用 flash，你会希望只有一个 loader 读取它，如果另一个 loader 想要 flash 消息，请为该 loader 使用不同的 key。

### 创建自定义会话存储

如果需要，React Router 使你可以轻松地将会话存储在自己的数据库中。[`createSessionStorage()`][create-session-storage] API 需要一个 `cookie`（用于创建 cookie 的选项，请参阅 [cookies][cookies]）和一组用于管理会话数据的创建、读取、更新和删除 (CRUD) 方法。cookie 用于持久化会话 ID。

- `createData` 将在初始会话创建时由 `commitSession` 调用（当 cookie 中不存在会话 ID 时）
- `readData` 将在 cookie 中存在会话 ID 时由 `getSession` 调用
- `updateData` 将在 cookie 中已存在会话 ID 时由 `commitSession` 调用
- `deleteData` 由 `destroySession` 调用

以下示例展示了如何使用通用数据库客户端来实现：

```ts
import { createSessionStorage } from "react-router";

function createDatabaseSessionStorage({
  cookie,
  host,
  port,
}) {
  // 配置你的数据库客户端...
  const db = createDatabaseClient(host, port);

  return createSessionStorage({
    cookie,
    async createData(data, expires) {
      // `expires` 是一个 Date，之后数据应被视为无效。
      // 你可以用它以某种方式使数据无效或
      // 自动从数据库中清除此记录。
      const id = await db.insert(data);
      return id;
    },
    async readData(id) {
      return (await db.select(id)) || null;
    },
    async updateData(id, data, expires) {
      await db.update(id, data);
    },
    async deleteData(id) {
      await db.delete(id);
    },
  });
}
```

然后你可以这样使用它：

```ts
const { getSession, commitSession, destroySession } =
  createDatabaseSessionStorage({
    host: "localhost",
    port: 1234,
    cookie: {
      name: "__session",
      sameSite: "lax",
    },
  });
```

`createData` 和 `updateData` 的 `expires` 参数与 cookie 本身过期且不再有效的 `Date` 相同。你可以使用此信息自动从数据库中清除会话记录以节省空间，或确保你不会为旧的过期 cookie 返回任何数据。

### 其他会话工具

如果需要，还有其他几个可用的会话工具：

- [`isSession`][is-session]
- [`createMemorySessionStorage`][create-memory-session-storage]
- [`createSession`][create-session]（自定义存储）
- [`createFileSessionStorage`][create-file-session-storage]（node）
- [`createWorkersKVSessionStorage`][create-workers-kv-session-storage]（Cloudflare Workers）
- [`createArcTableSessionStorage`][create-arc-table-session-storage]（architect, Amazon DynamoDB）

## Cookies

[Cookie][cookie] 是服务器在 HTTP 响应中发送给客户端的一小段信息，浏览器会在后续请求中将其发回。这种技术是许多添加状态的交互式网站的基本构建块，你可以用它构建认证（参见[会话][sessions]）、购物车、用户偏好设置和许多其他需要记住谁"已登录"的功能。

React Router 的 [`Cookie` 接口][cookie-api]提供了一个逻辑化、可复用的 cookie 元数据容器。

### 使用 Cookies

虽然你可以手动创建这些 cookie，但更常见的做法是使用[会话存储][sessions]。

在 React Router 中，你通常会在 `loader` 和/或 `action` 函数中使用 cookie，因为这些是你需要读写数据的地方。

假设你的电商网站上有一个横幅，提示用户查看当前促销商品。横幅横跨首页顶部，并包含一个按钮让用户关闭横幅，这样至少在接下来的一周内不会再看到它。

首先，创建一个 cookie：

```ts filename=app/cookies.server.ts
import { createCookie } from "react-router";

export const userPrefs = createCookie("user-prefs", {
  maxAge: 604_800, // 一周
});
```

然后，你可以 `import` 该 cookie 并在 `loader` 和/或 `action` 中使用它。此处的 `loader` 只是检查用户偏好的值，以便你可以在组件中决定是否渲染横幅。当按钮被点击时，`<form>` 在服务器上调用 `action` 并重新加载不带横幅的页面。

### 用户偏好设置示例

```tsx filename=app/routes/home.tsx lines=[4,9-11,18-20,29]
import { Link, Form, redirect } from "react-router";
import type { Route } from "./+types/home";

import { userPrefs } from "../cookies.server";

export async function loader({
  request,
}: Route.LoaderArgs) {
  const cookieHeader = request.headers.get("Cookie");
  const cookie =
    (await userPrefs.parse(cookieHeader)) || {};
  return { showBanner: cookie.showBanner };
}

export async function action({
  request,
}: Route.ActionArgs) {
  const cookieHeader = request.headers.get("Cookie");
  const cookie =
    (await userPrefs.parse(cookieHeader)) || {};
  const bodyParams = await request.formData();

  if (bodyParams.get("bannerVisibility") === "hidden") {
    cookie.showBanner = false;
  }

  return redirect("/", {
    headers: {
      "Set-Cookie": await userPrefs.serialize(cookie),
    },
  });
}

export default function Home({
  loaderData,
}: Route.ComponentProps) {
  return (
    <div>
      {loaderData.showBanner ? (
        <div>
          <Link to="/sale">不要错过我们的促销！</Link>
          <Form method="post">
            <input
              type="hidden"
              name="bannerVisibility"
              value="hidden"
            />
            <button type="submit">隐藏</button>
          </Form>
        </div>
      ) : null}
      <h1>欢迎！</h1>
    </div>
  );
}
```

### Cookie 属性

Cookie 有[多个属性][cookie-attrs]控制它们何时过期、如何访问以及发送到哪里。这些属性中的任何一个都可以在 `createCookie(name, options)` 中指定，或在生成 `Set-Cookie` 头时在 `serialize()` 中指定。

```ts
const cookie = createCookie("user-prefs", {
  // 此 cookie 的默认值。
  path: "/",
  sameSite: "lax",
  httpOnly: true,
  secure: true,
  expires: new Date(Date.now() + 60_000),
  maxAge: 60,
});

// 你可以使用默认值：
cookie.serialize(userPrefs);

// 或根据需要覆盖个别值：
cookie.serialize(userPrefs, { sameSite: "strict" });
```

请阅读[关于这些属性的更多信息][cookie-attrs]以更好地了解它们的作用。

### 签名 Cookies

可以对 cookie 进行签名，以便在接收时自动验证其内容。由于伪造 HTTP 头相对容易，对于任何你不希望被人伪造的信息（如认证信息，参见[会话][sessions]），这是一个好主意。

要签名一个 cookie，在首次创建 cookie 时提供一个或多个 `secrets`：

```ts
const cookie = createCookie("user-prefs", {
  secrets: ["s3cret1"],
});
```

拥有一个或多个 `secrets` 的 cookie 将以确保 cookie 完整性的方式存储和验证。

可以通过在 `secrets` 数组前面添加新的 secret 来轮换密钥。使用旧 secret 签名的 cookie 仍然可以在 `cookie.parse()` 中成功解码，而最新的 secret（数组中的第一个）将始终用于签名 `cookie.serialize()` 中创建的传出 cookie。

```ts filename=app/cookies.server.ts
export const cookie = createCookie("user-prefs", {
  secrets: ["n3wsecr3t", "olds3cret"],
});
```

```tsx filename=app/routes/my-route.tsx
import { data } from "react-router";
import { cookie } from "../cookies.server";
import type { Route } from "./+types/my-route";

export async function loader({
  request,
}: Route.LoaderArgs) {
  const oldCookie = request.headers.get("Cookie");
  // oldCookie 可能是用 "olds3cret" 签名的，但仍然可以正常解析
  const value = await cookie.parse(oldCookie);

  return data("...", {
    headers: {
      // Set-Cookie 使用 "n3wsecr3t" 签名
      "Set-Cookie": await cookie.serialize(value),
    },
  });
}
```

### 其他 Cookie 工具

如果需要，还有其他几个可用的 cookie 工具：

- [`isCookie`][is-cookie]
- [`createCookie`][create-cookie]

要了解每个属性的更多信息，请参阅 [MDN Set-Cookie 文档][cookie-attrs]。

[csrf]: https://developer.mozilla.org/en-US/docs/Glossary/CSRF
[cookies]: #cookies
[sessions]: #sessions
[session-storage]: https://api.reactrouter.com/v7/interfaces/react-router.SessionStorage
[session-api]: https://api.reactrouter.com/v7/interfaces/react-router.Session
[is-session]: https://api.reactrouter.com/v7/functions/react-router.isSession
[cookie-api]: https://api.reactrouter.com/v7/interfaces/react-router.Cookie
[create-session-storage]: https://api.reactrouter.com/v7/functions/react-router.createSessionStorage
[create-session]: https://api.reactrouter.com/v7/functions/react-router.createSession
[create-memory-session-storage]: https://api.reactrouter.com/v7/functions/react-router.createMemorySessionStorage
[create-file-session-storage]: https://api.reactrouter.com/v7/functions/_react-router_node.createFileSessionStorage
[create-workers-kv-session-storage]: https://api.reactrouter.com/v7/functions/_react-router_cloudflare.createWorkersKVSessionStorage
[create-arc-table-session-storage]: https://api.reactrouter.com/v7/functions/_react-router_architect.createArcTableSessionStorage
[cookie]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies
[cookie-attrs]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie#attributes
[is-cookie]: https://api.reactrouter.com/v7/functions/react-router.isCookie
[create-cookie]: https://api.reactrouter.com/v7/functions/react-router.createCookie
