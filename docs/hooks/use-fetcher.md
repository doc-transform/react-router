---
title: useFetcher
new: true
---

# `useFetcher`

在 HTML/HTTP 中，数据变更和加载通过导航来建模：`<a href>` 和 `<form action>`。两者都会在浏览器中引起导航。React Router 的等价物是 [`<Link>`][link] 和 [`<Form>`][form]。

但有时你希望在导航之外调用 [`loader`][loader]，或者在不改变 URL 的情况下调用 [`action`][action]（并让页面上的数据重新验证）。或者你需要同时进行多个变更。

与服务器的许多交互不是导航事件。此 hook 允许你将 UI 接入你的 action 和 loader 而无需导航。

<docs-warning>此功能仅在使用数据路由器时有效，参见[选择路由器][pickingarouter]</docs-warning>

这在以下情况下很有用：

- 获取与 UI 路由无关的数据（弹出框、动态表单等）
- 不导航就向 action 提交数据（共享组件，如新闻通讯注册）
- 处理列表中的多个并发提交（典型的“待办事项”列表，你可以点击多个按钮，所有按钮都应同时处于待处理状态）
- 无限滚动容器
- 以及更多！

如果你正在构建一个高度交互的、“类似应用”的用户界面，你会经常使用 `useFetcher`。

```tsx
import { useFetcher } from "react-router-dom";

function SomeComponent() {
  const fetcher = useFetcher();

  // call submit or load in a useEffect
  React.useEffect(() => {
    fetcher.submit(data, options);
    fetcher.load(href);
  }, [fetcher]);

  // build your UI with these properties
  fetcher.state;
  fetcher.formData;
  fetcher.json;
  fetcher.text;
  fetcher.formMethod;
  fetcher.formAction;
  fetcher.data;

  // render a form that doesn't cause navigation
  return <fetcher.Form />;
}
```

Fetcher 有很多内置行为：

- 自动处理 fetch 中断时的取消
- 使用 POST、PUT、PATCH、DELETE 提交时，会先调用 action
  - action 完成后，页面上的数据会重新验证以捕获可能发生的任何变更，自动保持 UI 与服务器状态同步
- 当多个 fetcher 同时进行时，它会
  - 在每个响应到达时提交最新的可用数据
  - 确保不会有过时的加载覆盖较新的数据，无论响应以什么顺序返回
- 通过渲染最近的 `errorElement` 处理未捕获的错误（就像从 `<Link>` 或 `<Form>` 的正常导航一样）
- 如果被调用的 action/loader 返回重定向，会重定向应用（就像从 `<Link>` 或 `<Form>` 的正常导航一样）

## 选项

### `key`

默认情况下，`useFetcher` 会生成一个作用于该组件的唯一 fetcher（但在进行中时可以通过 [`useFetchers()`][use-fetchers] 查找）。如果你想用自己的 `key` 标识一个 fetcher，以便在应用的其他地方访问它，可以使用 `key` 选项：

```tsx lines=[2,8]
function AddToBagButton() {
  const fetcher = useFetcher({ key: "add-to-bag" });
  return <fetcher.Form method="post">...</fetcher.Form>;
}

// Then, up in the header...
function CartCount({ count }) {
  const fetcher = useFetcher({ key: "add-to-bag" });
  const inFlightCount = Number(
    fetcher.formData?.get("quantity") || 0,
  );
  const optimisticCount = count + inFlightCount;
  return (
    <>
      <BagIcon />
      <span>{optimisticCount}</span>
    </>
  );
}
```

## 组件

### `fetcher.Form`

与 `<Form>` 完全相同，只是不会引起导航。<small>（你会习惯 JSX 中的点号的……我们希望！）</small>

```tsx
function SomeComponent() {
  const fetcher = useFetcher();
  return (
    <fetcher.Form method="post" action="/some/route">
      <input type="text" />
    </fetcher.Form>
  );
}
```

## 方法

### `fetcher.load(href, options)`

从路由 loader 加载数据。

```tsx lines=[8]
import { useFetcher } from "react-router-dom";

function SomeComponent() {
  const fetcher = useFetcher();

  useEffect(() => {
    if (fetcher.state === "idle" && !fetcher.data) {
      fetcher.load("/some/route");
    }
  }, [fetcher]);

  return <div>{fetcher.data || "Loading..."}</div>;
}
```

虽然一个 URL 可能匹配多个嵌套路由，但 `fetcher.load()` 调用只会调用叶子匹配的 loader（或 [index 路由][indexsearchparam] 的父级）。

如果你发现自己在点击处理器中调用此函数，你可能可以改用 `<fetcher.Form>` 来简化代码。

<docs-info>页面上任何活跃的 `fetcher.load` 调用都会作为重新验证的一部分被重新执行（无论是导航提交、另一个 fetcher 提交，还是 `useRevalidator()` 调用之后）</docs-info>

#### `options.flushSync`

`flushSync` 选项告诉 React Router DOM 将此 `fetcher.load` 的初始状态更新包装在 [`ReactDOM.flushSync`][flush-sync] 调用中，而不是默认的 [`React.startTransition`][start-transition] 中。这允许你在更新刷新到 DOM 后立即执行同步 DOM 操作。

### `fetcher.submit()`

`<fetcher.Form>` 的命令式版本。如果用户交互应该发起 fetch，你应该使用 `<fetcher.Form>`。但如果是你（程序员）发起 fetch（不是响应用户点击按钮等），则使用此函数。

例如，你可能希望在用户空闲一段时间后将其注销：

```tsx lines=[1,5,10-13]
import { useFetcher } from "react-router-dom";
import { useFakeUserIsIdle } from "./fake/hooks";

export function useIdleLogout() {
  const fetcher = useFetcher();
  const userIsIdle = useFakeUserIsIdle();

  useEffect(() => {
    if (userIsIdle) {
      fetcher.submit(
        { idle: true },
        { method: "post", action: "/logout" },
      );
    }
  }, [userIsIdle]);
}
```

`fetcher.submit` 是 fetcher 实例的 [`useSubmit`][use-submit] 调用的包装器，因此它也接受与 `useSubmit` 相同的选项。

如果你想提交到 index 路由，请使用 [`?index` 参数][indexsearchparam]。

如果你发现自己在点击处理器中调用此函数，你可能可以改用 `<fetcher.Form>` 来简化代码。

## 属性

### `fetcher.state`

你可以通过 `fetcher.state` 了解 fetcher 的状态。它将是以下之一：

- **idle** - 没有正在获取的内容。
- **submitting** - 由于使用 POST、PUT、PATCH 或 DELETE 的 fetcher 提交，正在调用路由 action
- **loading** - fetcher 正在调用 loader（通过 `fetcher.load`）或在单独的提交或 `useRevalidator` 调用后正在重新验证

### `fetcher.data`

从 loader 或 action 返回的数据存储在这里。一旦数据被设置，即使在重新加载和重新提交之后，它也会保留在 fetcher 上。

```tsx
function ProductDetails({ product }) {
  const fetcher = useFetcher();

  return (
    <details
      onToggle={(event) => {
        if (
          event.currentTarget.open &&
          fetcher.state === "idle" &&
          !fetcher.data
        ) {
          fetcher.load(`/product/${product.id}/details`);
        }
      }}
    >
      <summary>{product.name}</summary>
      {fetcher.data ? (
        <div>{fetcher.data}</div>
      ) : (
        <div>Loading product details...</div>
      )}
    </details>
  );
}
```

### `fetcher.formData`

使用 `<fetcher.Form>` 或 `fetcher.submit()` 时，表单数据可用于构建乐观 UI。

```tsx
function TaskCheckbox({ task }) {
  let fetcher = useFetcher();

  // while data is in flight, use that to immediately render
  // the state you expect the task to be in when the form
  // submission completes, instead of waiting for the
  // network to respond. When the network responds, the
  // formData will no longer be available and the UI will
  // use the value in `task.status` from the revalidation
  let status =
    fetcher.formData?.get("status") || task.status;

  let isComplete = status === "complete";

  return (
    <fetcher.Form method="post">
      <button
        type="submit"
        name="status"
        value={isComplete ? "complete" : "incomplete"}
      >
        {isComplete ? "Mark Complete" : "Mark Incomplete"}
      </button>
    </fetcher.Form>
  );
}
```

### `fetcher.json`

使用 `fetcher.submit(data, { formEncType: "application/json" })` 时，提交的 JSON 可通过 `fetcher.json` 获取。

### `fetcher.text`

使用 `fetcher.submit(data, { formEncType: "text/plain" })` 时，提交的文本可通过 `fetcher.text` 获取。

### `fetcher.formAction`

告诉你表单正在提交到的 action URL。

```tsx
<fetcher.Form action="/mark-as-read" />;

// when the form is submitting
fetcher.formAction; // "mark-as-read"
```

### `fetcher.formMethod`

告诉你正在提交的表单方法：get、post、put、patch 或 delete。

```tsx
<fetcher.Form method="post" />;

// when the form is submitting
fetcher.formMethod; // "post"
```

<docs-warning>没有 `future.v7_normalizeFormMethod` [Future Flag][api-development-strategy] 时，`fetcher.formMethod` 字段是小写的。这将在 v7 中规范化为大写以与 `fetch()` 行为保持一致，因此请升级你的 React Router v6 应用以采用大写 HTTP 方法。</docs-warning>

[loader]: ../route/loader
[action]: ../route/action
[pickingarouter]: ../routers/picking-a-router
[indexsearchparam]: ../guides/index-search-param
[link]: ../components/link
[form]: ../components/form
[api-development-strategy]: ../guides/api-development-strategy
[use-submit]: ./use-submit
[use-fetchers]: ./use-fetchers
[flush-sync]: https://react.dev/reference/react-dom/flushSync
[start-transition]: https://react.dev/reference/react/startTransition
