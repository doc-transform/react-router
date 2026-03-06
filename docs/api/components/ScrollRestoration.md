---
title: ScrollRestoration
---

# ScrollRestoration

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.ScrollRestoration.html)

在位置变化时模拟浏览器的滚动恢复行为。应用应只渲染一个此组件，紧接在 [`Scripts`](../components/Scripts) 组件之前。

```tsx
import { ScrollRestoration } from "react-router";

export default function Root() {
  return (
    <html>
      <body>
        <ScrollRestoration />
        <Scripts />
      </body>
    </html>
  );
}
```

此组件会渲染一个内联 `<script>` 来防止滚动闪烁。`nonce` 属性会传递给 script 标签，以允许 CSP nonce 使用。

```tsx
<ScrollRestoration nonce={cspNonce} />
```

## 函数签名

```tsx
function ScrollRestoration({
  getKey,
  storageKey,
  ...props
}: ScrollRestorationProps);
```

## Props

### getKey

返回用于滚动恢复的 key 的函数。这对于自定义滚动恢复逻辑很有用，例如仅使用 pathname，这样后续导航到先前路径时会恢复滚动位置。默认为 `location.key`。参见 [`GetScrollRestorationKeyFunction`](https://api.reactrouter.com/v7/interfaces/react-router.GetScrollRestorationKeyFunction.html)。

```tsx
<ScrollRestoration
  getKey={(location, matches) => {
    // 基于唯一的 location key 恢复（默认行为）
    return location.key;

    // 基于 pathname 恢复
    return location.pathname;
  }}
/>
```

### nonce

渲染在 [`<script>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script) 元素上的 [`nonce`](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Global_attributes/nonce) 属性。

### storageKey

用于在 [`sessionStorage`](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) 中存储滚动位置的 key。默认为 `"react-router-scroll-positions"`。
