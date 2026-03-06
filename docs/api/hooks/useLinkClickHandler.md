---
title: useLinkClickHandler
---

# useLinkClickHandler

[MODES: framework, data, declarative]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useLinkClickHandler.html)

处理路由 [`<Link>`](../components/Link) 组件的点击行为。当你需要创建具有与我们导出的 [`<Link>`](../components/Link) 相同点击行为的自定义 [`<Link>`](../components/Link) 组件时，这非常有用。

## 函数签名

```tsx
function useLinkClickHandler<E extends Element = HTMLAnchorElement>(
  to: To,
  {
    target,
    replace: replaceProp,
    unstable_mask,
    state,
    preventScrollReset,
    relative,
    viewTransition,
    unstable_defaultShouldRevalidate,
    unstable_useTransitions,
  }: {
    target?: React.HTMLAttributeAnchorTarget;
    replace?: boolean;
    unstable_mask?: To;
    state?: any;
    preventScrollReset?: boolean;
    relative?: RelativeRoutingType;
    viewTransition?: boolean;
    unstable_defaultShouldRevalidate?: boolean;
    unstable_useTransitions?: boolean;
  } = ,
): (event: React.MouseEvent<E, MouseEvent>) => void {}
```

## 参数

### to

要导航到的 URL，可以是字符串或部分 [`Path`](https://api.reactrouter.com/v7/interfaces/react-router.Path.html)。

### options.preventScrollReset

在使用 [`ScrollRestoration`](../components/ScrollRestoration) 组件时，是否阻止导航完成后滚动位置重置到视口顶部。默认为 `false`。

### options.relative

链接使用的[相对路由类型](https://api.reactrouter.com/v7/types/react-router.RelativeRoutingType.html)。默认为 `"route"`。

### options.replace

是否替换当前的 [`History`](https://developer.mozilla.org/en-US/docs/Web/API/History) 条目而不是推入新条目。默认为 `false`。

### options.state

要添加到此导航的 [`History`](https://developer.mozilla.org/en-US/docs/Web/API/History) 条目的状态。默认为 `undefined`。

### options.target

链接的 target 属性。默认为 `undefined`。

### options.viewTransition

为此导航启用[视图过渡](https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API)。要在过渡期间应用特定样式，请参阅 [`useViewTransitionState`](../hooks/useViewTransitionState)。默认为 `false`。

### options.unstable_defaultShouldRevalidate

指定导航的默认重新验证行为。默认为 `true`。

### options.unstable_mask

在浏览器中显示的遮罩位置，替代路由器位置。默认为 `undefined`。

### options.unstable_useTransitions

将导航包裹在 [`React.startTransition`](https://react.dev/reference/react/startTransition) 中以支持并发渲染。默认为 `false`。

## 返回值

可在自定义 [`Link`](../components/Link) 组件中使用的点击处理函数。
