---
title: Navigate
---

# Navigate

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.Navigate.html)

[`useNavigate`](../hooks/useNavigate) 的组件版本，用于在 [`React.Component` 类](https://react.dev/reference/react/Component)中使用（因为类组件不能使用 Hook）。

建议尽量使用 [`useNavigate`](../hooks/useNavigate) 代替此组件。

```tsx
<Navigate to="/tasks" />
```

## 函数签名

```tsx
function Navigate({
  to,
  replace,
  state,
  relative,
}: NavigateProps): null;
```

## Props

### relative

如何解释 `to` 属性中的相对路由。参见 [`RelativeRoutingType`](https://api.reactrouter.com/v7/types/react-router.RelativeRoutingType.html)。

### replace

是否替换 [`History`](https://developer.mozilla.org/en-US/docs/Web/API/History) 栈中的当前条目。

### state

传递给新 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html) 并存储在 [`history.state`](https://developer.mozilla.org/en-US/docs/Web/API/History/state) 中的状态。

### to

要导航到的路径。可以是字符串或 [`Path`](https://api.reactrouter.com/v7/interfaces/react-router.Path.html) 对象。
