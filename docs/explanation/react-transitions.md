---
title: React Transitions
unstable: true
---

# React Transitions

[MODES: framework, data, declarative]

<br/>
<br/>

<docs-warning>`unstable_useTransitions` prop 是实验性的，可能在次要/补丁版本中发生破坏性变更。请谨慎使用，并**密切**关注发布说明中的相关变更。</docs-warning>

[React 18][react-18] 引入了"过渡"（Transitions）的概念，允许你区分紧急和非紧急的 UI 更新。要了解更多关于 React Transitions 和"并发渲染"的信息，请参阅 React 的官方文档：

- [什么是并发 React][concurrent]
- [Transitions][transitions]
- [`React.useTransition`][use-transition]
- [`React.startTransition`][start-transition]

[React 19][react-19] 通过引入 [Actions][actions] 和对 Transitions 中异步函数的支持来增强了异步/并发场景。随着对异步 Transitions 的支持，还引入了新的 [`React.useOptimistic`][use-optimistic-blog] [hook][use-optimistic]，允许你在 Transition 期间呈现状态更新，以向用户显示即时反馈。

## React Router 中的 Transitions

React 中引入 Transitions 使得 React Router 管理导航和路由状态的方式变得更加复杂。这些是强大的 API，但并非没有一些细微差别和额外复杂性。我们的目标是让 React Router 与新的 React 特性无缝协作，但在某些情况下，新的 React 做事方式与你在 React Router 应用中已使用的某些模式（如加载状态、乐观 UI）之间可能存在一些张力。

为确保平滑的采用过程，我们将与 Transitions 相关的变更放在了选择性加入的 `unstable_useTransitions` 标志后面，以便你可以在不破坏现有功能的情况下升级。

### 当前行为

我们首先在 React Router [6.13.0][rr-6-13-0] 中通过 `future.v7_startTransition` 标志利用 `React.startTransition` 使 React Router 对 Suspense 更加友好。在 v7 中，这成为了默认行为，所有路由状态更新当前都包装在 `React.startTransition` 中。

此默认行为存在 2 个 `unstable_useTransitions` 旨在解决的潜在问题：

- 在某些有效的用例中，你*不*希望你的更新被包装在 `startTransition` 中
  - 一个具体问题是 `React.useSyncExternalStore` 的更新不能是 Transitions（[^1][uses-transition-issue]、[^2][uses-transition-tweet]）。`useSyncExternalStore` 强制同步更新，这意味着在更新 transitions 中可能会显示 fallback，而本来可以避免显示 fallback。
  - React Router 在导航上有一个 `flushSync` 选项来使用 [`React.flushSync`][flush-sync] 进行状态更新，但这并不总是合适的解决方案
- React 19 添加了新的 `startTransition(() => Promise))` API 以及新的 `useOptimistic` hook 来在 Transitions 期间呈现更新
  - 如果不对 React Router 进行一些更新，`startTransition(() => navigate(path))` 不会像你预期的那样工作，因为我们内部没有使用 `useOptimistic`，所以路由状态更新不会在导航期间呈现，这会破坏像 `useNavigation` 这样的 hooks

为了解决上述两个问题，我们引入了新的 `unstable_useTransitions` prop 到路由器组件中，让你可以选择不在路由状态更新中使用 `startTransition`（解决第一个问题），或选择加入更增强的 `startTransition` + `useOptimistic` 使用方式（解决第二个问题）。因为当前行为在新的 React 19 API 下有些不完整，我们计划在 React Router v8 中将选择加入行为设为默认，但我们可能会保留选择退出标志用于 `useSyncExternalStore` 等用例。

### 通过 `unstable_useTransitions=false` 选择退出

如果你的应用由于使用 `useSyncExternalStore`（或其他原因）而不适合使用 Transition，你可以通过此 prop 选择退出：

```tsx
// 框架模式 (entry.client.tsx)
<HydratedRouter unstable_useTransitions={false} />

// 数据模式
<RouterProvider unstable_useTransitions={false} />

// 声明式模式
<BrowserRouter unstable_useTransitions={false} />
```

这将阻止路由在内部状态更新中使用 `startTransition`。

### 通过 `unstable_useTransitions=true` 选择加入

<docs-info>在框架模式或数据模式中选择加入此功能需要你使用 React 19，因为它需要访问 [`React.useOptimistic`][use-optimistic]</docs-info>

如果你想让你的应用与所有依赖并发模式和 Transitions 的新 React 19 特性良好配合，你可以通过新的 prop 选择加入：

```tsx
// 框架模式 (entry.client.tsx)
<HydratedRouter unstable_useTransitions />

// 数据模式
<RouterProvider unstable_useTransitions />

// 声明式模式
<BrowserRouter unstable_useTransitions />
```

启用此标志后：

- 所有内部状态更新都包装在 `React.startTransition` 中（与不使用此标志时的当前行为相同）
- 所有 `<Link>`/`<Form>` 导航将被包装在 `React.startTransition` 中，使用 `useNavigate`/`useSubmit` 返回的 promise，以便 Transition 持续整个导航时间
  - `useNavigate`/`useSubmit` 不会自动包装在 `React.startTransition` 中，因此你可以通过直接使用它们来选择退出启用 Transition 的导航
- 在框架/数据模式中，导航期间的部分路由状态更新将通过 `useOptimistic` 呈现到 UI
  - 与*进行中*的导航相关的状态和所有 fetcher 信息将被呈现：
    - `state.navigation` 用于 `useNavigation()`
    - `state.revalidation` 用于 `useRevalidator()`
    - `state.actionData` 用于 `useActionData()`
    - `state.fetchers` 用于 `useFetcher()` 和 `useFetchers()`
  - 与*当前* location 相关的状态不会被呈现：
    - `state.location` 用于 `useLocation`
    - `state.matches` 用于 `useMatches()`
    - `state.loaderData` 用于 `useLoaderData()`
    - `state.errors` 用于 `useRouteError()`
    - 等等

启用此标志意味着你现在可以拥有完全启用 Transition 的导航，它能与应用中其他启用 Transition 的部分良好配合。

唯一自动包装在异步 Transition 中的 API 是 `<Link>` 和 `<Form>`。对于其他所有情况，你需要自行将操作包装在 `startTransition` 中。

```tsx
// 自动启用 Transition
<Link to="/path" />
<Form method="post" action="/path" />

// 手动启用 Transition
startTransition(() => navigate("/path"));
startTransition(() => submit(data, { method: 'post', action: "/path" }));
startTransition(() => fetcher.load("/path"));
startTransition(() => fetcher.submit(data, { method: "post", action: "/path" }));

// 未启用 Transition
navigate("/path");
submit(data, { method: 'post', action: "/path" });
fetcher.load("/path");
fetcher.submit(data, { method: "post", action: "/path" });
```

**重要**：你必须始终在 `startTransition` 中 `return` 或 `await` navigate 的 promise，以便 Transition 涵盖导航的全部时间。如果你忘记 `return` 或 `await` promise，Transition 将提前结束，事情不会按预期工作。

```tsx
// ✅ 返回的 promise
startTransition(() => navigate("/path"));
startTransition(() => {
  setOptimistic(something);
  return navigate("/path"));
});

// ✅ Await 的 promise
startTransition(async () => {
  setOptimistic(something);
  await navigate("/path"));
});

// ❌ 未返回的 promise
startTransition(() => {
  setOptimistic(something);
  navigate("/path"));
});

// ❌ 未 await 的 promise
startTransition(async () => {
  setOptimistic(something);
  navigate("/path"));
});
```

#### `popstate` 导航

目前乐观状态和 `popstate` 存在一个 bug。如果你需要在后退导航期间读取当前路由，而该导航无法同步完成（例如，对未缓存的数据进行 Suspend），你可以在导航回退之前设置乐观状态，或在定时器或微任务中延迟乐观更新。

[react-18]: https://react.dev/blog/2022/03/29/react-v18
[concurrent]: https://react.dev/blog/2022/03/29/react-v18#what-is-concurrent-react
[transitions]: https://react.dev/blog/2022/03/29/react-v18#new-feature-transitions
[use-transition]: https://react.dev/reference/react/useTransition#reference
[start-transition]: https://react.dev/reference/react/startTransition
[react-19]: https://react.dev/blog/2024/12/05/react-19
[actions]: https://react.dev/blog/2024/12/05/react-19#actions
[use-optimistic-blog]: https://react.dev/blog/2024/12/05/react-19#new-hook-optimistic-updates
[use-optimistic]: https://react.dev/reference/react/useOptimistic
[flush-sync]: https://react.dev/reference/react-dom/flushSync
[rr-6-13-0]: https://github.com/remix-run/react-router/blob/main/CHANGELOG.md#v6130
[uses-transition-issue]: https://github.com/facebook/react/issues/26382
[uses-transition-tweet]: https://x.com/rickhanlonii/status/1683636856808775682
