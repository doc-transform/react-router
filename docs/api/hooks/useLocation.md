---
title: useLocation
---

# useLocation

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useLocation.html)

返回当前的 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html)。当你需要在位置变化时执行某些副作用时，这非常有用。

```tsx
import * as React from 'react'
import { useLocation } from 'react-router'

function SomeComponent() {
  let location = useLocation()

  React.useEffect(() => {
    // Google Analytics
    ga('send', 'pageview')
  }, [location]);

  return (
    // ...
  );
}
```

## 函数签名

```tsx
function useLocation(): Location;
```

## 返回值

当前的 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html) 对象。
