---
title: useBlocker
---

# `useBlocker`

<details>
  <summary>类型声明</summary>

```tsx
declare function useBlocker(
  shouldBlock: boolean | BlockerFunction,
): Blocker;

type BlockerFunction = (args: {
  currentLocation: Location;
  nextLocation: Location;
  historyAction: HistoryAction;
}) => boolean;

type Blocker =
  | {
      state: "unblocked";
      reset: undefined;
      proceed: undefined;
      location: undefined;
    }
  | {
      state: "blocked";
      reset(): void;
      proceed(): void;
      location: Location;
    }
  | {
      state: "proceeding";
      reset: undefined;
      proceed: undefined;
      location: Location;
    };

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

`useBlocker` hook 允许你阻止用户从当前位置导航离开，并向他们展示自定义 UI 以确认导航。

<docs-warning>此功能仅在使用数据路由器时有效，参见[选择路由器][pickingarouter]</docs-warning>

<docs-info>
这仅适用于 React Router 应用内的客户端导航，不会阻止文档请求。要阻止文档导航，你需要添加自己的 <a href="https://developer.mozilla.org/en-US/docs/Web/API/Window/beforeunload_event" target="_blank">`beforeunload`</a> 事件处理器。
</docs-info>

<docs-warning>
阻止用户导航在某种程度上是一种反模式，因此请仔细考虑此 hook 的使用场景并谨慎使用。在阻止用户从填写了一半的表单中导航离开的典型用例中，你可能需要考虑将未保存的状态持久化到 `sessionStorage`，并在用户返回时自动重新填充，而不是阻止他们导航离开。
</docs-warning>

```tsx
function ImportantForm() {
  let [value, setValue] = React.useState("");

  // Block navigating elsewhere when data has been entered into the input
  let blocker = useBlocker(
    ({ currentLocation, nextLocation }) =>
      value !== "" &&
      currentLocation.pathname !== nextLocation.pathname,
  );

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

      {blocker.state === "blocked" ? (
        <div>
          <p>Are you sure you want to leave?</p>
          <button onClick={() => blocker.proceed()}>
            Proceed
          </button>
          <button onClick={() => blocker.reset()}>
            Cancel
          </button>
        </div>
      ) : null}
    </Form>
  );
}
```

更完整的示例请参考仓库中的[示例][example]。

## 属性

### `state`

阻止器的当前状态

- `unblocked` - 阻止器处于空闲状态，未阻止任何导航
- `blocked` - 阻止器已阻止了一次导航
- `proceeding` - 阻止器正在从被阻止的导航中继续前进

### `location`

当处于 `blocked` 状态时，这表示我们阻止导航到的位置。当处于 `proceeding` 状态时，这是在调用 `blocker.proceed()` 后正在导航到的位置。

## 方法

### `proceed()`

当处于 `blocked` 状态时，你可以调用 `blocker.proceed()` 来继续前往被阻止的位置。

### `reset()`

当处于 `blocked` 状态时，你可以调用 `blocker.reset()` 将阻止器恢复到 `unblocked` 状态，并让用户留在当前位置。

[example]: https://github.com/remix-run/react-router/tree/main/examples/navigation-blocking
[pickingarouter]: ../routers/picking-a-router
