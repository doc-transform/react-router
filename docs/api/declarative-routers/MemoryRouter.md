---
title: MemoryRouter
---

# MemoryRouter

[MODES: declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.MemoryRouter.html)

将所有条目存储在内存中的声明式 [`<Router>`](../declarative-routers/Router)。

## 函数签名

```tsx
function MemoryRouter({
  basename,
  children,
  initialEntries,
  initialIndex,
  unstable_useTransitions,
}: MemoryRouterProps): React.ReactElement;
```

## Props

### basename

应用的基础路径。

### children

描述路由树的嵌套 [`Route`](../components/Route) 元素。

### initialEntries

内存历史栈中的初始条目。

### initialIndex

应用应初始化到的 `initialEntries` 索引。

### unstable_useTransitions

控制路由器状态更新是否在内部包裹在 [`React.startTransition`](https://react.dev/reference/react/startTransition) 中。

更多信息请参阅[文档](https://reactrouter.com/explanation/react-transitions)。
