---
title: useNavigate
---

# useNavigate

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useNavigate.html)

返回一个函数，允许你在浏览器中以编程方式响应用户交互或副作用进行导航。

通常在 [`action`](../../start/framework/route-module#action)/[`loader`](../../start/framework/route-module#loader) 函数中使用 [`redirect`](../utils/redirect) 比使用此 Hook 更好。

返回的函数签名为 `navigate(to, options?)`/`navigate(delta)`，其中：

- `to` 可以是字符串路径、[`To`](https://api.reactrouter.com/v7/types/react-router.To.html) 对象或数字（delta）
- `options` 包含修改导航行为的选项
  - 以下选项在所有模式（框架、数据和声明式）中都有效：
    - `relative`：`"route"` 或 `"path"`，控制相对路由逻辑
    - `replace`：替换 [`History`](https://developer.mozilla.org/en-US/docs/Web/API/History) 栈中的当前条目
    - `state`：可选的 [`history.state`](https://developer.mozilla.org/en-US/docs/Web/API/History/state)，包含在新的 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html) 中
  - 以下选项仅在框架模式和数据模式中有效：
    - `flushSync`：将 DOM 更新包裹在 [`ReactDom.flushSync`](https://react.dev/reference/react-dom/flushSync) 中
    - `preventScrollReset`：导航后不将滚动位置重置到页面顶部
    - `viewTransition`：为此导航启用 [`document.startViewTransition`](https://developer.mozilla.org/en-US/docs/Web/API/Document/startViewTransition)

```tsx
import { useNavigate } from "react-router";

function SomeComponent() {
  let navigate = useNavigate();
  return (
    <button onClick={() => navigate(-1)}>Go Back</button>
  );
}
```

## 函数签名

```tsx
function useNavigate(): NavigateFunction;
```

## 返回值

用于编程式导航的 navigate 函数。

## 示例

### 导航到另一个路径

```tsx
navigate("/some/route");
navigate("/some/route?search=param");
```

### 使用 [`To`](https://api.reactrouter.com/v7/types/react-router.To.html) 对象导航

所有属性都是可选的。

```tsx
navigate({
  pathname: "/some/route",
  search: "?search=param",
  hash: "#hash",
  state: { some: "state" },
});
```

如果使用了 `state`，它将在下一个页面的 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html) 对象上可用。通过 `useLocation().state` 访问（参见 [`useLocation`](../hooks/useLocation)）。

### 在历史栈中前进或后退

```tsx
// 后退
// 常用于关闭模态框
navigate(-1);

// 前进
// 常用于多步骤向导流程
navigate(1);
```

使用 `navigate(number)` 时要谨慎。如果你的应用可以直接加载到某个路由，而该路由有一个尝试前进/后退导航的按钮，可能没有 [`History`](https://developer.mozilla.org/en-US/docs/Web/API/History) 条目可以前进或后退，或者可能导航到你意想不到的地方（如另一个域名）。

只有在你确定用户在 [`History`](https://developer.mozilla.org/en-US/docs/Web/API/History) 栈中有可导航的条目时才使用此方法。

### 替换历史栈中的当前条目

这将移除 [`History`](https://developer.mozilla.org/en-US/docs/Web/API/History) 栈中的当前条目，用新条目替换它，类似于服务端重定向。

```tsx
navigate("/some/route", { replace: true });
```

### 阻止滚动重置

[MODES: framework, data]

<br/>
<br/>

要阻止 [`<ScrollRestoration>`](../components/ScrollRestoration) 重置滚动位置，请使用 `preventScrollReset` 选项。

```tsx
navigate("?some-tab=1", { preventScrollReset: true });
```

例如，如果你在页面中间有一个与搜索参数关联的标签页界面，当点击标签时，你不希望它滚动到页面顶部。

### 返回类型增强

`useNavigate` 在声明式模式和数据/框架模式下内部使用不同的实现 — 主要区别在于后者能够返回一个在导航之间不会改变引用标识的稳定引用。数据/框架模式的实现还会返回一个在导航完成后解析的 [`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)。这意味着 `useNavigate` 的返回类型是 `void | Promise<void>`。这虽然准确，但可能由于返回值的联合类型导致一些类型错误提示：

- 如果你使用 `typescript-eslint`，可能会看到来自 [`@typescript-eslint/no-floating-promises`](https://typescript-eslint.io/rules/no-floating-promises) 的错误
- 在框架/数据模式下，`React.use(navigate())` 会显示误报的 `Argument of type 'void | Promise<void>' is not assignable to parameter of type 'Usable<void>'` 错误

解决这些问题最简单的方法是根据你使用的路由器增强类型：

```ts
// 如果使用 <BrowserRouter>
declare module "react-router" {
  interface NavigateFunction {
    (to: To, options?: NavigateOptions): void;
    (delta: number): void;
  }
}

// 如果使用 <RouterProvider> 或框架模式
declare module "react-router" {
  interface NavigateFunction {
    (to: To, options?: NavigateOptions): Promise<void>;
    (delta: number): Promise<void>;
  }
}
```
