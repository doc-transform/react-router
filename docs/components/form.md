---
title: Form
new: true
---

# `<Form>`

<details>
  <summary>类型声明</summary>

```tsx
declare function Form(props: FormProps): React.ReactElement;

interface FormProps
  extends React.FormHTMLAttributes<HTMLFormElement> {
  method?: "get" | "post" | "put" | "patch" | "delete";
  encType?:
    | "application/x-www-form-urlencoded"
    | "multipart/form-data"
    | "text/plain";
  action?: string;
  onSubmit?: React.FormEventHandler<HTMLFormElement>;
  fetcherKey?: string;
  navigate?: boolean;
  preventScrollReset?: boolean;
  relative?: "route" | "path";
  reloadDocument?: boolean;
  replace?: boolean;
  state?: any;
  viewTransition?: boolean;
}
```

</details>

Form 组件是对原生 HTML [form][htmlform] 的包装，用于模拟浏览器的客户端路由和数据变更行为。它*不是*像你在 React 生态中可能习惯使用的那种表单验证/状态管理库（对于表单验证，我们推荐使用浏览器内置的 [HTML 表单验证][formvalidation] 以及后端服务器上的数据验证）。

<docs-warning>此功能仅在使用数据路由器时有效，参见[选择路由器][pickingarouter]</docs-warning>

```tsx
import { Form } from "react-router-dom";

function NewEvent() {
  return (
    <Form method="post" action="/events">
      <input type="text" name="title" />
      <input type="text" name="description" />
      <button type="submit">Create</button>
    </Form>
  );
}
```

<docs-info>确保你的输入框有 name 属性，否则 `FormData` 将不会包含该字段的值。</docs-info>

所有这些都将触发对已渲染的 [`useNavigation`][usenavigation] hook 的状态更新，使你可以在异步操作进行期间构建加载中指示器和乐观 UI。

如果表单操作*感觉*不像导航，你可能需要使用 [`useFetcher`][usefetcher]。

## `action`

表单将提交到的 URL，与 [HTML form action][htmlformaction] 相同。唯一的区别是默认 action。在 HTML 表单中，默认为完整 URL。在 `<Form>` 中，默认为上下文中最近路由的相对 URL。

考虑以下路由和组件：

```jsx
function ProjectsLayout() {
  return (
    <>
      <Form method="post" />
      <Outlet />
    </>
  );
}

function ProjectsPage() {
  return <Form method="post" />;
}

<DataBrowserRouter>
  <Route
    path="/projects"
    element={<ProjectsLayout />}
    action={ProjectsLayout.action}
  >
    <Route
      path=":projectId"
      element={<ProjectsPage />}
      action={ProjectsPage.action}
    />
  </Route>
</DataBrowserRouter>;
```

如果当前 URL 是 `"/projects/123"`，子路由 `ProjectsPage` 内的表单将拥有你可能预期的默认 action：`"/projects/123"`。在这种情况下，路由是最深的匹配路由，`<Form>` 和普通 HTML 表单的结果相同。

但 `ProjectsLayout` 内的表单将指向 `"/projects"`，而不是完整 URL。换句话说，它指向表单所渲染的路由的匹配 URL 片段。

这有助于提高可移植性，以及表单与其 action 处理程序的就近放置（如果你在路由模块中采用某种约定的话）。

如果你需要提交到不同的路由，添加一个 action 属性：

```tsx
<Form action="/projects/new" method="post" />
```

**另请参阅：**

- [Index 搜索参数][indexsearchparam]（索引路由与父路由的区分）

<docs-info>请参阅 `useResolvedPath` 文档中的[通配路径][relativesplatpath]部分，了解 `future.v7_relativeSplatPath` future flag 对通配路由中相对 `useNavigate()` 行为的影响</docs-info>

## `method`

此属性决定要使用的 [HTTP 方法](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)。与普通 HTML [form method][htmlform-method] 相同，但除了 "get" 和 "post" 之外还支持 "put"、"patch" 和 "delete"。默认为 "get"。

### GET 提交

默认方法是 "get"。Get 提交*不会调用 action*。Get 提交与普通导航（用户点击链接）相同，只是用户可以通过表单提供进入 URL 的搜索参数。

```tsx
<Form method="get" action="/products">
  <input
    aria-label="search products"
    type="text"
    name="q"
  />
  <button type="submit">Search</button>
</Form>
```

假设用户输入了 "running shoes" 并提交了表单。React Router 模拟浏览器行为，将表单序列化为 [URLSearchParams][urlsearchparams]，然后将用户导航到 `"/products?q=running+shoes"`。就好像你作为开发者渲染了一个 `<Link to="/products?q=running+shoes">`，只不过你让用户动态地提供了查询字符串。

你的路由 loader 可以通过从 `request.url` 创建一个新的 [`URL`][url] 来最方便地访问这些值，然后加载数据。

```tsx
<Route
  path="/products"
  loader={async ({ request }) => {
    let url = new URL(request.url);
    let searchTerm = url.searchParams.get("q");
    return fakeSearchProducts(searchTerm);
  }}
/>
```

### 数据变更提交

所有其他方法都是"数据变更提交"，意味着你打算用 POST、PUT、PATCH 或 DELETE 来更改某些数据。注意，普通 HTML 表单只支持 "post" 和 "get"，我们也倾向于只使用这两种。

当用户提交表单时，React Router 会将 `action` 匹配到应用的路由并调用 `<Route action>`，传入序列化的 [`FormData`][formdata]。当 action 完成后，页面上的所有 loader 数据将自动重新验证，以保持 UI 与数据同步。

`method` 可在被调用的路由 action 中通过 [`request.method`][requestmethod] 获取。你可以使用它来指示数据抽象层关于提交的意图。

```tsx
<Route
  path="/projects/:id"
  element={<Project />}
  loader={async ({ params }) => {
    return fakeLoadProject(params.id);
  }}
  action={async ({ request, params }) => {
    switch (request.method) {
      case "PUT": {
        let formData = await request.formData();
        let name = formData.get("projectName");
        return fakeUpdateProject(name);
      }
      case "DELETE": {
        return fakeDeleteProject(params.id);
      }
      default: {
        throw new Response("", { status: 405 });
      }
    }
  }}
/>;

function Project() {
  let project = useLoaderData();

  return (
    <>
      <Form method="put">
        <input
          type="text"
          name="projectName"
          defaultValue={project.name}
        />
        <button type="submit">Update Project</button>
      </Form>

      <Form method="delete">
        <button type="submit">Delete Project</button>
      </Form>
    </>
  );
}
```

如你所见，两个表单都提交到同一个路由，但你可以使用 `request.method` 来区分你的意图。action 完成后，`loader` 将被重新验证，UI 将自动与新数据同步。

## `navigate`

你可以通过指定 `<Form navigate={false}>` 来告诉表单跳过导航并在内部使用 [fetcher][usefetcher]。这本质上是 `useFetcher()` + `<fetcher.Form>` 的简写形式，适用于你不关心返回数据、只想触发提交并通过 [`useFetchers()`][usefetchers] 访问待定状态的场景。

## `fetcherKey`

使用非导航 `Form` 时，你也可以通过 `<Form navigate={false} fetcherKey="my-key">` 来指定自己的 fetcher key。

## `replace`

指示表单替换历史栈中的当前条目，而不是推入新条目。

```tsx
<Form replace />
```

默认行为取决于表单的行为：

- `method=get` 表单默认为 `false`
- 提交方法取决于 `formAction` 和 `action` 的行为：
  - 如果你的 `action` 抛出异常，默认为 `false`
  - 如果你的 `action` 重定向到当前位置，默认为 `true`
  - 如果你的 `action` 重定向到其他位置，默认为 `false`
  - 如果你的 `formAction` 是当前位置，默认为 `true`
  - 否则默认为 `false`

我们发现，对于 `get`，你通常希望用户能够点击"后退"查看之前的搜索结果/筛选条件等。但对于其他方法，默认为 `true` 以避免"你确定要重新提交表单吗？"的提示。注意，即使 `replace={false}`，React Router 在点击后退按钮时也*不会*重新提交 method 为 post、put、patch 或 delete 的表单。

换句话说，这实际上只对 GET 提交有用，用于避免后退按钮显示之前的结果。

## `relative`

默认情况下，路径相对于路由层级，因此 `..` 会上升一个 `Route` 层级。有时你可能会发现某些匹配的 URL 模式嵌套在一起没有意义，此时你更希望使用相对*路径*路由。你可以使用 `<Form to="../some/where" relative="path">` 来启用此行为。

## `reloadDocument`

指示表单跳过 React Router，使用浏览器内置行为提交表单。

```tsx
<Form reloadDocument />
```

推荐使用此方式而不是 `<form>`，这样你可以获得默认和相对 `action` 的好处，但在其他方面与普通 HTML 表单相同。

如果没有像 [Remix][remix] 这样的框架，或者你自己的服务器处理路由的 post 请求，这不是很有用。

另请参阅：

- [`useNavigation`][usenavigation]
- [`useActionData`][useactiondata]
- [`useSubmit`][usesubmit]

## `state`

`state` 属性可用于为新位置设置一个存储在 [history state][history-state] 中的有状态值。该值随后可以通过 `useLocation()` 访问。

```tsx
<Form
  method="post"
  action="new-path"
  state={{ some: "value" }}
/>
```

你可以在 "new-path" 路由上访问此状态值：

```ts
let { state } = useLocation();
```

## `preventScrollReset`

如果你使用了 [`<ScrollRestoration>`][scrollrestoration]，此属性可以阻止在表单 action 重定向到新位置时将滚动位置重置到窗口顶部。

```tsx
<Form method="post" preventScrollReset={true} />
```

参见：[`<Link preventScrollReset>`][link-preventscrollreset]

## `viewTransition`

`viewTransition` 属性通过将最终状态更新包装在 `document.startViewTransition()` 中，为此次导航启用[视图过渡][view-transitions]。如果你需要为此视图过渡应用特定样式，还需要使用 [`useViewTransitionState()`][use-view-transition-state] hook。

# 示例

TODO: 更多示例

## 大列表筛选

GET 提交的一个常见用例是筛选大型列表，如电商和旅行预订网站。

```tsx
function FilterForm() {
  return (
    <Form method="get" action="/slc/hotels">
      <select name="sort">
        <option value="price">Price</option>
        <option value="stars">Stars</option>
        <option value="distance">Distance</option>
      </select>

      <fieldset>
        <legend>Star Rating</legend>
        <label>
          <input type="radio" name="stars" value="5" />{" "}
          ★★★★★
        </label>
        <label>
          <input type="radio" name="stars" value="4" /> ★★★★
        </label>
        <label>
          <input type="radio" name="stars" value="3" /> ★★★
        </label>
        <label>
          <input type="radio" name="stars" value="2" /> ★★
        </label>
        <label>
          <input type="radio" name="stars" value="1" /> ★
        </label>
      </fieldset>

      <fieldset>
        <legend>Amenities</legend>
        <label>
          <input
            type="checkbox"
            name="amenities"
            value="pool"
          />{" "}
          Pool
        </label>
        <label>
          <input
            type="checkbox"
            name="amenities"
            value="exercise"
          />{" "}
          Exercise Room
        </label>
      </fieldset>
      <button type="submit">Search</button>
    </Form>
  );
}
```

当用户提交此表单时，表单将根据用户的选择被序列化到 URL 中，类似这样：

```
/slc/hotels?sort=price&stars=4&amenities=pool&amenities=exercise
```

你可以从 `request.url` 中访问这些值：

```tsx
<Route
  path="/:city/hotels"
  loader={async ({ request }) => {
    let url = new URL(request.url);
    let sort = url.searchParams.get("sort");
    let stars = url.searchParams.get("stars");
    let amenities = url.searchParams.getAll("amenities");
    return fakeGetHotels({ sort, stars, amenities });
  }}
/>
```

**另请参阅：**

- [useSubmit][usesubmit]

[usenavigation]: ../hooks/use-navigation
[useactiondata]: ../hooks/use-action-data
[formdata]: https://developer.mozilla.org/en-US/docs/Web/API/FormData
[usefetcher]: ../hooks/use-fetcher
[usefetchers]: ../hooks/use-fetchers
[htmlform]: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form
[htmlformaction]: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form#attr-action
[htmlform-method]: https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form#attr-method
[urlsearchparams]: https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams
[url]: https://developer.mozilla.org/en-US/docs/Web/API/URL
[usesubmit]: ../hooks/use-submit
[requestmethod]: https://developer.mozilla.org/en-US/docs/Web/API/Request/method
[remix]: https://remix.run
[formvalidation]: https://developer.mozilla.org/en-US/docs/Learn/Forms/Form_validation
[indexsearchparam]: ../guides/index-search-param
[pickingarouter]: ../routers/picking-a-router
[scrollrestoration]: ./scroll-restoration
[link-preventscrollreset]: ./link#preventscrollreset
[history-state]: https://developer.mozilla.org/en-US/docs/Web/API/History/state
[use-view-transition-state]: ../hooks//use-view-transition-state
[view-transitions]: https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API
[relativesplatpath]: ../hooks/use-resolved-path#splat-paths
