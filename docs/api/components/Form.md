---
title: Form
---

# Form

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.Form.html)

一个渐进增强的 HTML [`<form>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form) 组件，通过 [`fetch`](https://developer.mozilla.org/en-US/docs/Web/API/fetch) 向 action 提交数据，在 [`useNavigation`](../hooks/useNavigation) 中激活待定状态，实现超越基础 HTML [`<form>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form) 的高级用户界面。表单的 `action` 完成后，页面上的所有数据会自动重新验证，保持 UI 与数据同步。

由于使用了 HTML 表单 API，服务端渲染的页面在 JavaScript 加载前就能在基础级别上进行交互。浏览器会像处理普通 `<form>` 一样管理提交和待定状态（如旋转的 favicon）。JavaScript 加载后，React Router 接管并提供 Web 应用级别的用户体验。

`Form` 最适用于需要更改 URL 或在浏览器历史记录栈中添加新条目的表单提交。对于不需要操作浏览器 [`History`](https://developer.mozilla.org/en-US/docs/Web/API/History) 栈的表单，请使用 [`<fetcher.Form>`](https://api.reactrouter.com/v7/types/react-router.FetcherWithComponents.html#Form)。

```tsx
import { Form } from "react-router";

function NewEvent() {
  return (
    <Form action="/events" method="post">
      <input name="title" type="text" />
      <input name="description" type="text" />
    </Form>
  );
}
```

## Props

### action

表单数据提交的目标 URL。如果为 `undefined`，默认为上下文中最近的路由。

### discover

定义表单的[懒路由发现](../../explanation/lazy-route-discovery)行为。

- **render** — 默认值，在表单渲染时发现路由
- **none** — 不提前发现，仅在表单提交时发现

```tsx
<Form /> // 默认 ("render")
<Form discover="render" />
<Form discover="none" />
```

### encType

表单提交使用的编码类型。

```tsx
<Form encType="application/x-www-form-urlencoded"/>  // 默认
<Form encType="multipart/form-data"/>
<Form encType="text/plain"/>
```

### fetcherKey

指示在使用 `navigate={false}` 时使用特定的 fetcherKey，以便在不同组件中通过 [`useFetcher`](../hooks/useFetcher) 获取 fetcher 的状态。

### method

表单提交时使用的 HTTP 方法。支持 `"delete"`、`"get"`、`"patch"`、`"post"` 和 `"put"`。

原生 [`<form>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form) 仅支持 `"get"` 和 `"post"`，如果你希望支持渐进增强，请避免使用其他方法。

### navigate

当设为 `false` 时，跳过导航并在内部通过 fetcher 提交。这本质上是 [`useFetcher`](../hooks/useFetcher) + `<fetcher.Form>` 的简写形式，适用于你不关心当前组件中返回数据的场景。

### onSubmit

表单提交时调用的函数。如果你调用了 [`event.preventDefault()`](https://developer.mozilla.org/en-US/docs/Web/API/Event/preventDefault)，则该表单不会执行任何操作。

### preventScrollReset

使用 `<ScrollRestoration>` 组件时，防止导航完成后滚动位置重置到视口顶部。

### relative

决定表单 action 是相对于路由层级还是路径名。如果你想退出路由层级导航，转而基于斜杠分隔的 URL 段进行路由，请使用此属性。参见 [`RelativeRoutingType`](https://api.reactrouter.com/v7/types/react-router.RelativeRoutingType.html)。

### reloadDocument

强制使用完整的文档导航，而非客户端路由和数据获取。

### replace

当表单导航时，替换浏览器 [`History`](https://developer.mozilla.org/en-US/docs/Web/API/History) 栈中的当前条目。如果你不希望用户能够点击"后退"回到包含表单的页面，请使用此属性。

### state

添加到此导航的 [`History`](https://developer.mozilla.org/en-US/docs/Web/API/History) 栈条目的状态对象。

### viewTransition

为此导航启用[视图过渡](https://developer.mozilla.org/en-US/docs/Web/API/View_Transitions_API)。要在过渡期间应用特定样式，请参阅 [`useViewTransitionState`](../hooks/useViewTransitionState)。

### unstable_defaultShouldRevalidate

指定此次提交后的默认重新验证行为。

如果当前活跃的路由上没有 `shouldRevalidate` 函数，则直接使用此值。否则会将其传入 `shouldRevalidate`，由路由做最终的重新验证判断。这在更新搜索参数且不想触发重新验证时很有用。

默认情况下（未指定时），loader 会按照路由器的标准重新验证行为进行重新验证。
