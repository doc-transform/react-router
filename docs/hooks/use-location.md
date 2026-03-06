---
title: useLocation
---

# `useLocation`

<details>
  <summary>类型声明</summary>

```tsx
declare function useLocation(): Location;

interface Location<State = any> extends Path {
  state: State;
  key: string;
}

interface Path {
  pathname: string;
  search: string;
  hash: string;
}
```

</details>

此 hook 返回当前的 [`location`][location] 对象。如果你想在当前位置发生变化时执行某些副作用，这会很有用。

```tsx
import * as React from 'react';
import { useLocation } from 'react-router-dom';

function App() {
  let location = useLocation();

  React.useEffect(() => {
    // Google Analytics
    ga('send', 'pageview');
  }, [location]);

  return (
    // ...
  );
}
```

## 属性

### `location.hash`

当前 URL 的哈希部分。

### `location.key`

此位置的唯一键。

### `location.pathname`

当前 URL 的路径。

### `location.search`

当前 URL 的查询字符串。

### `location.state`

由 [`<Link state>`][link-state] 或 [`navigate`][navigate] 创建的位置的 state 值。

[link-state]: ../components/link#state
[location]: ../utils/location
[navigate]: ./use-navigate
