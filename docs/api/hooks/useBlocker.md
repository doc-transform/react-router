---
title: useBlocker
---

# useBlocker

<!--
⚠️ ⚠️ IMPORTANT ⚠️ ⚠️

Thank you for helping improve our documentation!

This file is auto-generated from the JSDoc comments in the source
code, so please edit the JSDoc comments in the file below and this
file will be re-generated once those changes are merged.

https://github.com/remix-run/react-router/blob/main/packages/react-router/lib/hooks.tsx
-->

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useBlocker.html)

允许应用阻止 SPA 内的导航，并向用户展示确认对话框来确认导航。主要用于避免丢失半填写的表单数据。此功能不处理强制刷新或跨域导航。

Hook 返回的 [`Blocker`](https://api.reactrouter.com/v7/types/react-router.Blocker.html) 对象具有以下属性：

- **`state`**
  - `unblocked` — 拦截器处于空闲状态，未阻止任何导航
  - `blocked` — 拦截器已阻止一次导航
  - `proceeding` — 拦截器正在从被阻止的导航中继续前进
- **`location`**
  - 处于 `blocked` 状态时，表示被阻止导航的目标 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html)。处于 `proceeding` 状态时，表示调用 `blocker.proceed()` 后正在导航到的位置。
- **`proceed()`**
  - 处于 `blocked` 状态时，可以调用 `blocker.proceed()` 继续前往被阻止的位置。
- **`reset()`**
  - 处于 `blocked` 状态时，可以调用 `blocker.reset()` 将拦截器重置为 `unblocked` 状态，让用户留在当前位置。

```tsx
// 布尔值版本
let blocker = useBlocker(value !== "");

// 函数版本
let blocker = useBlocker(
  ({ currentLocation, nextLocation, historyAction }) =>
    value !== "" &&
    currentLocation.pathname !== nextLocation.pathname,
);
```

## 函数签名

```tsx
function useBlocker(
  shouldBlock: boolean | BlockerFunction,
): Blocker;
```

## 参数

### shouldBlock

布尔值或返回布尔值的函数，指示是否应阻止导航。函数形式接收一个包含 `currentLocation`、`nextLocation` 和 `historyAction` 的对象参数。

## 返回值

包含状态和重置功能的 [`Blocker`](https://api.reactrouter.com/v7/types/react-router.Blocker.html) 对象。

## 示例

```tsx
import { useCallback, useState } from "react";
import { BlockerFunction, useBlocker } from "react-router";

export function ImportantForm() {
  const [value, setValue] = useState("");

  const shouldBlock = useCallback<BlockerFunction>(
    () => value !== "",
    [value],
  );
  const blocker = useBlocker(shouldBlock);

  return (
    <form
      onSubmit={(e) => {
        e.preventDefault();
        setValue("");
        if (blocker.state === "blocked") {
          blocker.proceed();
        }
      }}
    >
      <input
        name="data"
        value={value}
        onChange={(e) => setValue(e.target.value)}
      />

      <button type="submit">Save</button>

      {blocker.state === "blocked" ? (
        <>
          <p style={{ color: "red" }}>
            Blocked the last navigation to
          </p>
          <button
            type="button"
            onClick={() => blocker.proceed()}
          >
            Let me through
          </button>
          <button
            type="button"
            onClick={() => blocker.reset()}
          >
            Keep me here
          </button>
        </>
      ) : blocker.state === "proceeding" ? (
        <p style={{ color: "orange" }}>
          Proceeding through blocked navigation
        </p>
      ) : (
        <p style={{ color: "green" }}>
          Blocker is currently unblocked
        </p>
      )}
    </form>
  );
}
```
