---
title: useSearchParams
---

# `useSearchParams`

<docs-info>这是 `useSearchParams` 的 Web 版本。React Native 版本请[前往这里][usesearchparams-native]。</docs-info>

<details>
  <summary>类型声明</summary>

```tsx
declare function useSearchParams(
  defaultInit?: URLSearchParamsInit
): [URLSearchParams, SetURLSearchParams];

type ParamKeyValuePair = [string, string];

type URLSearchParamsInit =
  | string
  | ParamKeyValuePair[]
  | Record<string, string | string[]>
  | URLSearchParams;

type SetURLSearchParams = (
  nextInit?:
    | URLSearchParamsInit
    | ((prev: URLSearchParams) => URLSearchParamsInit),
  navigateOpts?: : NavigateOptions
) => void;

interface NavigateOptions {
  replace?: boolean;
  state?: any;
  preventScrollReset?: boolean;
}
```

</details>

`useSearchParams` hook 用于读取和修改当前位置 URL 中的查询字符串。与 React 自身的 [`useState` hook][usestate] 类似，`useSearchParams` 返回一个包含两个值的数组：当前位置的 [search params][searchparams] 和一个可用于更新它们的函数。与 React 的 [`useState` hook][usestate] 一样，`setSearchParams` 也支持[函数式更新][functional-updates]。因此，你可以提供一个接收 `searchParams` 并返回更新版本的函数。

```tsx
import * as React from "react";
import { useSearchParams } from "react-router-dom";

function App() {
  let [searchParams, setSearchParams] = useSearchParams();

  function handleSubmit(event) {
    event.preventDefault();
    // The serialize function here would be responsible for
    // creating an object of { key: value } pairs from the
    // fields in the form that make up the query.
    let params = serializeFormQuery(event.target);
    setSearchParams(params);
  }

  return (
    <div>
      <form onSubmit={handleSubmit}>{/* ... */}</form>
    </div>
  );
}
```

<docs-info>`setSearchParams` 函数的工作方式与 [`navigate`][usenavigate] 类似，但仅适用于 URL 的 [search 部分](https://developer.mozilla.org/en-US/docs/Web/API/Location/search)。另请注意，`setSearchParams` 的第二个参数与 `navigate` 的第二个参数类型相同。</docs-info>

[functional-updates]: https://reactjs.org/docs/hooks-reference.html#functional-updates
[searchparams]: https://developer.mozilla.org/en-US/docs/Web/API/URL/searchParams
[usesearchparams-native]: ./use-search-params-rn
[usestate]: https://react.dev/reference/react/useState
[usenavigate]: ./use-navigate
