---
title: 状态管理
---

# 状态管理

[MODES: framework, data]

<br/>
<br/>

React 中的状态管理通常涉及在客户端维护服务器数据的同步缓存。然而，当使用 React Router 作为框架时，由于它内在的数据同步处理方式，大多数传统的缓存解决方案变得多余。

## 理解 React 中的状态管理

在典型的 React 上下文中，当我们提到"状态管理"时，主要是讨论如何将服务器状态与客户端同步。更恰当的术语可能是"缓存管理"，因为服务器是真实来源，而客户端状态主要作为缓存功能。

React 中流行的缓存解决方案包括：

- **Redux**：JavaScript 应用的可预测状态容器。
- **React Query**：用于在 React 中获取、缓存和更新异步数据的 Hooks。
- **Apollo**：与 GraphQL 集成的全面状态管理库。

在某些场景下，使用这些库可能是合理的。然而，凭借 React Router 独特的以服务器为中心的方法，它们的实用性变得不那么普遍。事实上，大多数 React Router 应用完全不使用它们。

## React Router 如何简化状态管理

React Router 通过 loader、action 和表单等机制，以及通过重新验证实现的自动同步，无缝连接了后端和前端。这为开发者提供了直接在组件中使用服务器状态的能力，无需管理缓存、网络通信或数据重新验证，使得大多数客户端缓存变得多余。

以下是为什么使用典型 React 状态模式在 React Router 中可能是反模式的原因：

1. **网络相关状态**：如果你的 React 状态管理的是与网络相关的任何内容——如来自 loader 的数据、待处理的表单提交或导航状态——那么你可能在管理 React Router 已经管理的状态：
   - **[`useNavigation`][use_navigation]**：此 hook 让你访问 `navigation.state`、`navigation.formData`、`navigation.location` 等。
   - **[`useFetcher`][use_fetcher]**：这便于与 `fetcher.state`、`fetcher.formData`、`fetcher.data` 等交互。
   - **[`loaderData`][loader_data]**：访问路由的数据。
   - **[`actionData`][action_data]**：访问最新 action 的数据。

2. **在 React Router 中存储数据**：开发者可能倾向于存储在 React 状态中的许多数据在 React Router 中有更自然的归宿，例如：
   - **URL 搜索参数**：URL 中包含状态的参数。
   - **[Cookies][cookies]**：存储在用户设备上的小段数据。
   - **[服务器会话][sessions]**：服务器管理的用户会话。
   - **服务器缓存**：服务端的缓存数据，用于更快的检索。

3. **性能考虑**：有时候，客户端状态被用来避免冗余的数据获取。使用 React Router，你可以在 `loader` 中使用 [`Cache-Control`][cache_control_header] 头，让你利用浏览器的原生缓存。然而，这种方法有其局限性，应谨慎使用。通常更好的做法是优化后端查询或实现服务器缓存。这是因为这些变更对所有用户都有益，并且不需要单独的浏览器缓存。

作为迁移到 React Router 的开发者，认识并拥抱其内在的效率至关重要，而不是套用传统的 React 模式。React Router 提供了精简的状态管理解决方案，带来更少的代码、新鲜的数据和没有状态同步 bug。

## 示例

### 网络相关状态

有关使用 React Router 内部状态管理网络相关状态的示例，请参阅[加载中 UI][pending_ui]。

### URL 搜索参数

考虑一个让用户在列表视图和详情视图之间自定义切换的 UI。你的本能可能是使用 React 状态：

```tsx bad lines=[2,6,9]
export function List() {
  const [view, setView] = useState("list");
  return (
    <div>
      <div>
        <button onClick={() => setView("list")}>
          列表视图
        </button>
        <button onClick={() => setView("details")}>
          详情视图
        </button>
      </div>
      {view === "list" ? <ListView /> : <DetailView />}
    </div>
  );
}
```

现在考虑你想在用户更改视图时更新 URL。注意状态同步：

```tsx bad lines=[7,16,24]
import { useNavigate, useSearchParams } from "react-router";

export function List() {
  const navigate = useNavigate();
  const [searchParams] = useSearchParams();
  const [view, setView] = useState(
    searchParams.get("view") || "list",
  );

  return (
    <div>
      <div>
        <button
          onClick={() => {
            setView("list");
            navigate(`?view=list`);
          }}
        >
          列表视图
        </button>
        <button
          onClick={() => {
            setView("details");
            navigate(`?view=details`);
          }}
        >
          详情视图
        </button>
      </div>
      {view === "list" ? <ListView /> : <DetailView />}
    </div>
  );
}
```

与其同步状态，你可以直接使用普通的 HTML 表单来读取和设置 URL 中的状态：

```tsx good lines=[5,9-16]
import { Form, useSearchParams } from "react-router";

export function List() {
  const [searchParams] = useSearchParams();
  const view = searchParams.get("view") || "list";

  return (
    <div>
      <Form>
        <button name="view" value="list">
          列表视图
        </button>
        <button name="view" value="details">
          详情视图
        </button>
      </Form>
      {view === "list" ? <ListView /> : <DetailView />}
    </div>
  );
}
```

### 持久化 UI 状态

考虑一个切换侧边栏可见性的 UI。我们有三种方式处理状态：

1. React 状态
2. 浏览器本地存储
3. Cookies

在这个讨论中，我们将分析每种方法的利弊权衡。

#### React 状态

React 状态提供了一种简单的临时状态存储解决方案。

**优点**：

- **简单**：易于实现和理解。
- **封装性**：状态的作用域限于组件。

**缺点**：

- **瞬时性**：不能在页面刷新、稍后返回页面或卸载和重新挂载组件时保留。

**实现**：

```tsx
function Sidebar() {
  const [isOpen, setIsOpen] = useState(false);
  return (
    <div>
      <button onClick={() => setIsOpen((open) => !open)}>
        {isOpen ? "关闭" : "打开"}
      </button>
      <aside hidden={!isOpen}>
        <Outlet />
      </aside>
    </div>
  );
}
```

#### 本地存储

要在组件生命周期之外持久化状态，浏览器本地存储是一个进阶选择。参见我们关于[客户端数据][client_data]的文档了解更高级的示例。

**优点**：

- **持久性**：在页面刷新和组件挂载/卸载时保持状态。
- **封装性**：状态的作用域限于组件。

**缺点**：

- **需要同步**：React 组件必须与本地存储同步以初始化和保存当前状态。
- **服务端渲染限制**：[`window`][window_global] 和 [`localStorage`][local_storage_global] 对象在服务端渲染期间不可用，因此状态必须在浏览器中通过 effect 初始化。
- **UI 闪烁**：在初始页面加载时，本地存储中的状态可能与服务器渲染的不一致，当 JavaScript 加载时 UI 会闪烁。

**实现**：

```tsx
function Sidebar() {
  const [isOpen, setIsOpen] = useState(false);

  // 初始同步
  useLayoutEffect(() => {
    const isOpen = window.localStorage.getItem("sidebar");
    setIsOpen(isOpen);
  }, []);

  // 变更时同步
  useEffect(() => {
    window.localStorage.setItem("sidebar", isOpen);
  }, [isOpen]);

  return (
    <div>
      <button onClick={() => setIsOpen((open) => !open)}>
        {isOpen ? "关闭" : "打开"}
      </button>
      <aside hidden={!isOpen}>
        <Outlet />
      </aside>
    </div>
  );
}
```

在这种方法中，状态必须在 effect 中初始化。这对于避免服务端渲染期间的问题至关重要。直接从 `localStorage` 初始化 React 状态会导致错误，因为 `window.localStorage` 在服务端渲染期间不可用。

```tsx bad lines=[4]
function Sidebar() {
  const [isOpen, setIsOpen] = useState(
    // 错误: window is not defined
    window.localStorage.getItem("sidebar"),
  );

  // ...
}
```

通过在 effect 中初始化状态，服务端渲染的状态和本地存储中的状态可能不匹配。这种差异会导致页面渲染后短暂的 UI 闪烁，应该避免。

#### Cookies

Cookie 为此用例提供了全面的解决方案。然而，此方法在组件中使状态可访问之前需要额外的初始设置。

**优点**：

- **服务端渲染**：状态在服务器上可用于渲染，甚至可用于服务器 action。
- **单一真实来源**：消除了状态同步的麻烦。
- **持久性**：在页面加载和组件挂载/卸载时保持状态。如果切换到数据库支持的会话，状态甚至可以跨设备持久化。
- **渐进增强**：即使在 JavaScript 加载之前也能工作。

**缺点**：

- **样板代码**：由于网络需求，需要更多代码。
- **暴露性**：状态不封装在单个组件中，应用的其他部分必须了解该 cookie。

**实现**：

首先我们需要创建一个 cookie 对象：

```tsx
import { createCookie } from "react-router";
export const prefs = createCookie("prefs");
```

接下来设置服务器 action 和 loader 来读写 cookie：

```tsx filename=app/routes/sidebar.tsx
import { data, Outlet } from "react-router";
import type { Route } from "./+types/sidebar";

import { prefs } from "./prefs-cookie";

// 从 cookie 中读取状态
export async function loader({
  request,
}: Route.LoaderArgs) {
  const cookieHeader = request.headers.get("Cookie");
  const cookie = (await prefs.parse(cookieHeader)) || {};
  return data({ sidebarIsOpen: cookie.sidebarIsOpen });
}

// 将状态写入 cookie
export async function action({
  request,
}: Route.ActionArgs) {
  const cookieHeader = request.headers.get("Cookie");
  const cookie = (await prefs.parse(cookieHeader)) || {};
  const formData = await request.formData();

  const isOpen = formData.get("sidebar") === "open";
  cookie.sidebarIsOpen = isOpen;

  return data(isOpen, {
    headers: {
      "Set-Cookie": await prefs.serialize(cookie),
    },
  });
}
```

服务器代码设置完成后，我们可以在 UI 中使用 cookie 状态：

```tsx
function Sidebar({ loaderData }: Route.ComponentProps) {
  const fetcher = useFetcher();
  let { sidebarIsOpen } = loaderData;

  // 使用乐观 UI 来立即改变 UI 状态
  if (fetcher.formData?.has("sidebar")) {
    sidebarIsOpen =
      fetcher.formData.get("sidebar") === "open";
  }

  return (
    <div>
      <fetcher.Form method="post">
        <button
          name="sidebar"
          value={sidebarIsOpen ? "closed" : "open"}
        >
          {sidebarIsOpen ? "关闭" : "打开"}
        </button>
      </fetcher.Form>
      <aside hidden={!sidebarIsOpen}>
        <Outlet />
      </aside>
    </div>
  );
}
```

虽然这确实需要更多的代码来处理网络请求和响应，涉及应用的更多部分，但 UX 大大改善了。此外，状态来自单一真实来源，无需任何状态同步。

总结来说，讨论的每种方法都有其独特的优势和挑战：

- **React 状态**：提供简单但瞬时的状态管理。
- **本地存储**：提供持久性，但需要同步且有 UI 闪烁问题。
- **Cookies**：以额外样板代码为代价，提供健壮的、持久化的状态管理。

这些都没有错，但如果你想在不同访问之间持久化状态，cookie 提供了最好的用户体验。

### 表单验证和 Action 数据

客户端验证可以增强用户体验，但通过更多地依赖服务端处理并让它处理复杂性，可以实现类似的增强。

以下示例说明了管理网络状态、协调来自服务器的状态以及在客户端和服务端冗余实现验证的固有复杂性。这仅用于说明，请忽略你发现的任何明显 bug 或问题。

```tsx bad lines=[2,11,27,38,63]
export function Signup() {
  // 大量的 React State 声明
  const [isSubmitting, setIsSubmitting] = useState(false);

  const [userName, setUserName] = useState("");
  const [userNameError, setUserNameError] = useState(null);

  const [password, setPassword] = useState(null);
  const [passwordError, setPasswordError] = useState("");

  // 在客户端复制服务端逻辑
  function validateForm() {
    setUserNameError(null);
    setPasswordError(null);
    const errors = validateSignupForm(userName, password);
    if (errors) {
      if (errors.userName) {
        setUserNameError(errors.userName);
      }
      if (errors.password) {
        setPasswordError(errors.password);
      }
    }
    return Boolean(errors);
  }

  // 手动处理网络交互
  async function handleSubmit() {
    if (validateForm()) {
      setSubmitting(true);
      const res = await postJSON("/api/signup", {
        userName,
        password,
      });
      const json = await res.json();
      setIsSubmitting(false);

      // 将服务器状态同步到客户端
      if (json.errors) {
        if (json.errors.userName) {
          setUserNameError(json.errors.userName);
        }
        if (json.errors.password) {
          setPasswordError(json.errors.password);
        }
      }
    }
  }

  return (
    <form
      onSubmit={(event) => {
        event.preventDefault();
        handleSubmit();
      }}
    >
      <p>
        <input
          type="text"
          name="username"
          value={userName}
          onChange={() => {
            // 为 fetch 同步表单状态
            setUserName(event.target.value);
          }}
        />
        {userNameError ? <i>{userNameError}</i> : null}
      </p>

      <p>
        <input
          type="password"
          name="password"
          onChange={(event) => {
            // 为 fetch 同步表单状态
            setPassword(event.target.value);
          }}
        />
        {passwordError ? <i>{passwordError}</i> : null}
      </p>

      <button disabled={isSubmitting} type="submit">
        注册
      </button>

      {isSubmitting ? <BusyIndicator /> : null}
    </form>
  );
}
```

后端端点 `/api/signup` 也执行验证并发送错误反馈。注意一些重要的验证，如检测重复用户名，只能在服务端使用客户端无法访问的信息来完成。

```tsx bad
export async function signupHandler(request: Request) {
  const errors = await validateSignupRequest(request);
  if (errors) {
    return { ok: false, errors: errors };
  }
  await signupUser(request);
  return { ok: true, errors: null };
}
```

现在，让我们将其与基于 React Router 的实现进行对比。action 保持一致，但组件由于直接利用通过 `actionData` 获取的服务器状态，以及利用 React Router 内在管理的网络状态，被大大简化了。

```tsx filename=app/routes/signup.tsx good lines=[20-22]
import { useNavigation } from "react-router";
import type { Route } from "./+types/signup";

export async function action({
  request,
}: ActionFunctionArgs) {
  const errors = await validateSignupRequest(request);
  if (errors) {
    return { ok: false, errors: errors };
  }
  await signupUser(request);
  return { ok: true, errors: null };
}

export function Signup({
  actionData,
}: Route.ComponentProps) {
  const navigation = useNavigation();

  const userNameError = actionData?.errors?.userName;
  const passwordError = actionData?.errors?.password;
  const isSubmitting = navigation.formAction === "/signup";

  return (
    <Form method="post">
      <p>
        <input type="text" name="username" />
        {userNameError ? <i>{userNameError}</i> : null}
      </p>

      <p>
        <input type="password" name="password" />
        {passwordError ? <i>{passwordError}</i> : null}
      </p>

      <button disabled={isSubmitting} type="submit">
        注册
      </button>

      {isSubmitting ? <BusyIndicator /> : null}
    </Form>
  );
}
```

之前示例中大量的状态管理被精简为仅仅三行代码。我们消除了对 React 状态、change 事件监听器、提交处理器和状态管理库的需求，用于此类网络交互。

通过 `actionData` 可以直接访问服务器状态，通过 `useNavigation`（或 `useFetcher`）访问网络状态。

作为额外的妙招，表单甚至在 JavaScript 加载之前就能工作（参见[渐进增强][progressive_enhancement]）。React Router 不再管理网络操作，而是由默认的浏览器行为来接管。

如果你发现自己陷入了管理和同步网络操作状态的泥潭，React Router 很可能提供了一个更优雅的解决方案。

[use_navigation]: https://api.reactrouter.com/v7/functions/react-router.useNavigation
[use_fetcher]: https://api.reactrouter.com/v7/functions/react-router.useFetcher
[loader_data]: ../start/framework/data-loading
[action_data]: ../start/framework/actions
[cookies]: ./sessions-and-cookies#cookies
[sessions]: ./sessions-and-cookies#sessions
[cache_control_header]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control
[pending_ui]: ../start/framework/pending-ui
[client_data]: ../how-to/client-data
[window_global]: https://developer.mozilla.org/en-US/docs/Web/API/Window/window
[local_storage_global]: https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage
[progressive_enhancement]: ./progressive-enhancement
