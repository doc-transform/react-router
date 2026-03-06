---
title: useRevalidator
new: true
---

# `useRevalidator`

此 hook 允许你因任何原因重新验证数据。React Router 在 action 调用后会自动重新验证数据，但你可能希望因其他原因重新验证，例如当焦点回到窗口时。

<docs-warning>此功能仅在使用数据路由器时有效，参见[选择路由器][pickingarouter]</docs-warning>

```tsx
import { useRevalidator } from "react-router-dom";

function WindowFocusRevalidator() {
  let revalidator = useRevalidator();

  useFakeWindowFocus(() => {
    revalidator.revalidate();
  });

  return (
    <div hidden={revalidator.state === "idle"}>
      Revalidating...
    </div>
  );
}
```

同样，React Router 在绝大多数情况下已经自动重新验证页面上的数据，所以很少需要用到这个。如果你发现自己在用户交互响应中的正常 CRUD 操作中使用此 hook，你可能没有充分利用其他自动执行此操作的 API，如 [`<Form>`][form]、[`useSubmit`][usesubmit] 或 [`useFetcher`][usefetcher]。

## `revalidator.state`

告诉你重新验证所处的状态，可以是 `"idle"` 或 `"loading"`。

这对于创建加载指示器和 spinner 让用户知道应用正在处理很有用。

## `revalidator.revalidate()`

这会启动一次重新验证。

```tsx
function useLivePageData() {
  let revalidator = useRevalidator();
  let interval = useInterval(5000);

  useEffect(() => {
    if (revalidator.state === "idle") {
      revalidator.revalidate();
    }
  }, [interval]);
}
```

## 注意事项

虽然你可以同时渲染多个 `useRevalidator` 实例，但底层它是一个单例。这意味着当一个 `revalidator.revalidate()` 被调用时，所有实例都会一起进入 `"loading"` 状态（或者更准确地说，它们都会更新以报告单例状态）。

当重新验证已在进行时调用 `revalidate()` 会自动处理竞态条件。

如果在重新验证进行中时发生了导航，重新验证会被取消，并且会为下一页从所有 loader 请求新数据。

[form]: ../components/form
[usefetcher]: ./use-fetcher
[usesubmit]: ./use-submit
[pickingarouter]: ../routers/picking-a-router
