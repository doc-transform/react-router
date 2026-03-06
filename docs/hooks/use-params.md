---
title: useParams
---

# `useParams`

<details>
  <summary>类型声明</summary>

```tsx
declare function useParams<
  K extends string = string,
>(): Readonly<Params<K>>;
```

</details>

`useParams` hook 返回一个由当前 URL 中与 `<Route path>` 匹配的动态参数组成的键/值对对象。子路由会继承其父路由的所有参数。

```tsx
import * as React from 'react';
import { Routes, Route, useParams } from 'react-router-dom';

function ProfilePage() {
  // Get the userId param from the URL.
  let { userId } = useParams();
  // ...
}

function App() {
  return (
    <Routes>
      <Route path="users">
        <Route path=":userId" element={<ProfilePage />} />
        <Route path="me" element={...} />
      </Route>
    </Routes>
  );
}
```
