---
title: unstable_usePrompt
---

# `unstable_usePrompt`

<details>
  <summary>类型声明</summary>

```tsx
declare function unstable_usePrompt({
  when,
  message,
}: {
  when: boolean | BlockerFunction;
  message: string;
}) {

type BlockerFunction = (args: {
  currentLocation: Location;
  nextLocation: Location;
  historyAction: HistoryAction;
}) => boolean;

interface Location<State = any> extends Path {
  state: State;
  key: string;
}

interface Path {
  pathname: string;
  search: string;
  hash: string;
}

enum HistoryAction {
  Pop = "POP",
  Push = "PUSH",
  Replace = "REPLACE",
}
```

</details>

`unstable_usePrompt` hook 允许你在用户从当前位置导航离开之前，通过 [`window.confirm`][window-confirm] 提示用户确认。

<docs-info>
这仅适用于 React Router 应用内的客户端导航，不会阻止文档请求。要阻止文档导航，你需要添加自己的 <a href="https://developer.mozilla.org/en-US/docs/Web/API/Window/beforeunload_event" target="_blank">`beforeunload`</a> 事件处理器。
</docs-info>

<docs-warning>
阻止用户导航在某种程度上是一种反模式，因此请仔细考虑此 hook 的使用场景并谨慎使用。在阻止用户从填写了一半的表单中导航离开的典型用例中，你可能需要考虑将未保存的状态持久化到 `sessionStorage`，并在用户返回时自动重新填充，而不是阻止他们导航离开。
</docs-warning>

<docs-warning>
我们不打算从此 hook 中移除 `unstable_` 前缀，因为当提示框打开时，跨浏览器的行为是不确定的，所以 React Router 无法保证在所有场景中都能正确运行。为了避免这种不确定性，我们建议使用 `useBlocker` 代替，它也能让你控制确认 UX。
</docs-warning>

```tsx
function ImportantForm() {
  let [value, setValue] = React.useState("");

  // Block navigating elsewhere when data has been entered into the input
  unstable_usePrompt({
    message: "Are you sure?",
    when: ({ currentLocation, nextLocation }) =>
      value !== "" &&
      currentLocation.pathname !== nextLocation.pathname,
  });

  return (
    <Form method="post">
      <label>
        Enter some important data:
        <input
          name="data"
          value={value}
          onChange={(e) => setValue(e.target.value)}
        />
      </label>
      <button type="submit">Save</button>
    </Form>
  );
}
```

[window-confirm]: https://developer.mozilla.org/en-US/docs/Web/API/Window/confirm
