---
title: NativeRouter
---

# `<NativeRouter>`

<details>
  <summary>类型声明</summary>

```tsx
declare function NativeRouter(
  props: NativeRouterProps,
): React.ReactElement;

interface NativeRouterProps extends MemoryRouterProps {}
```

</details>

`<NativeRouter>` 是在 [React Native][react-native] 应用中运行 React Router 的推荐接口。

- `<NativeRouter initialEntries>` 默认为 `["/"]`（根 `/` URL 处的单个条目）
- `<NativeRouter initialIndex>` 默认为 `initialEntries` 的最后一个索引

```tsx
import * as React from "react";
import { NativeRouter } from "react-router-native";

function App() {
  return (
    <NativeRouter>
      {/* 你的应用的其余部分放在这里 */}
    </NativeRouter>
  );
}
```

[react-native]: https://reactnative.dev
