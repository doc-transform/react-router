---
title: useSearchParams (RN)
---

# `useSearchParams` (React Native)

<docs-info>这是 `useSearchParams` 的 React Native 版本。Web 版本请[前往这里][usesearchparams]。</docs-info>

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
import { View, SearchForm, TextInput } from "react-native";
import { useSearchParams } from "react-router-native";

function App() {
  let [searchParams, setSearchParams] = useSearchParams();
  let [query, setQuery] = React.useState(
    searchParams.get("query"),
  );

  function handleSubmit() {
    setSearchParams({ query });
  }

  return (
    <View>
      <SearchForm onSubmit={handleSubmit}>
        <TextInput value={query} onChangeText={setQuery} />
      </SearchForm>
    </View>
  );
}
```

[functional-updates]: https://reactjs.org/docs/hooks-reference.html#functional-updates
[searchparams]: https://developer.mozilla.org/en-US/docs/Web/API/URL/searchParams
[usesearchparams]: ./use-search-params
[usestate]: https://reactjs.org/docs/hooks-reference.html#usestate
