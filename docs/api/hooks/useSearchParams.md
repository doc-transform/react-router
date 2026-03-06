---
title: useSearchParams
---

# useSearchParams

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useSearchParams.html)

返回一个元组，包含当前 URL 的 [`URLSearchParams`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams) 和一个用于更新它们的函数。设置搜索参数会触发导航。

```tsx
import { useSearchParams } from "react-router";

export function SomeComponent() {
  const [searchParams, setSearchParams] = useSearchParams();
  // ...
}
```

### `setSearchParams` 函数

元组的第二个元素是一个可以用于更新搜索参数的函数。它接受与 `defaultInit` 相同的类型，并会触发导航到新 URL。

```tsx
let [searchParams, setSearchParams] = useSearchParams();

// 搜索参数字符串
setSearchParams("?tab=1");

// 简写对象
setSearchParams({ tab: "1" });

// 对象的 key 可以是数组，用于同一个 key 的多个值
setSearchParams({ brand: ["nike", "reebok"] });

// 元组数组
setSearchParams([["tab", "1"]]);

// `URLSearchParams` 对象
setSearchParams(new URLSearchParams("?tab=1"));
```

它还支持类似 React [`setState`](https://react.dev/reference/react/useState#setstate) 的函数回调：

```tsx
setSearchParams((searchParams) => {
  searchParams.set("tab", "2");
  return searchParams;
});
```

<docs-warning>`setSearchParams` 的函数回调版本不支持 React `setState` 实现的[队列化](https://react.dev/reference/react/useState#setstate-parameters)逻辑。在同一个 tick 内多次调用 `setSearchParams` 不会基于上一个值进行构建。如果你需要此行为，可以手动使用 `setState`。</docs-warning>

### 注意事项

`searchParams` 是一个稳定引用，因此你可以放心地将其作为 React [`useEffect`](https://react.dev/reference/react/useEffect) Hook 的依赖项。

```tsx
useEffect(() => {
  console.log(searchParams.get("tab"));
}, [searchParams]);
```

但这也意味着它是可变的。如果你在不调用 `setSearchParams` 的情况下修改了对象，当其他状态导致组件重新渲染时，它的值会在渲染之间发生变化，且 URL 不会反映这些值。

## 函数签名

```tsx
function useSearchParams(
  defaultInit?: URLSearchParamsInit,
): [URLSearchParams, SetURLSearchParams];
```

## 参数

### defaultInit

你可以用默认值初始化搜索参数，但这**不会**在首次渲染时更改 URL。

```tsx
// 搜索参数字符串
useSearchParams("?tab=1");

// 简写对象
useSearchParams({ tab: "1" });

// 对象的 key 可以是数组，用于同一个 key 的多个值
useSearchParams({ brand: ["nike", "reebok"] });

// 元组数组
useSearchParams([["tab", "1"]]);

// `URLSearchParams` 对象
useSearchParams(new URLSearchParams("?tab=1"));
```

## 返回值

包含当前 [`URLSearchParams`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams) 和一个更新函数的元组。
