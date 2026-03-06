---
title: useViewTransitionState
---

# useViewTransitionState

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useViewTransitionState.html)

当存在到指定位置的活跃[视图过渡](https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API)时，此 Hook 返回 `true`。这可用于为元素应用更精细的样式，以进一步自定义视图过渡。这要求通过 [`LinkProps.viewTransition`](https://api.reactrouter.com/v7/interfaces/react-router.LinkProps.html#viewTransition)（或 `Form`、`submit` 或 `navigate` 调用）为给定导航启用了视图过渡。

## 函数签名

```tsx
function useViewTransitionState(
  to: To,
  {
    relative,
  }: {
    relative?: RelativeRoutingType;
  } = ,
) {}
```

## 参数

### to

要检查活跃[视图过渡](https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API)的 [`To`](https://api.reactrouter.com/v7/types/react-router.To.html) 位置。

### options.relative

解析 `to` 位置时使用的相对路由类型，默认为 `"route"`。详见 [`RelativeRoutingType`](https://api.reactrouter.com/v7/types/react-router.RelativeRoutingType.html)。

## 返回值

如果存在到指定 [`Location`](https://api.reactrouter.com/v7/interfaces/react-router.Location.html) 的活跃[视图过渡](https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API)则返回 `true`，否则返回 `false`。
