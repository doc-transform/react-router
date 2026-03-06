---
title: RouterContextProvider
---

# RouterContextProvider

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/classes/react-router.RouterContextProvider.html)

提供以类型安全的方式在应用上下文中读写值的方法。主要用于[中间件](../../how-to/middleware)。

```tsx
import {
  createContext,
  RouterContextProvider,
} from "react-router";

const userContext = createContext<User | null>(null);
const contextProvider = new RouterContextProvider();
contextProvider.set(userContext, getUser());
//                               ^ 类型安全
const user = contextProvider.get(userContext);
//    ^ User
```
