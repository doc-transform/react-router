---
title: hydrateFallbackElement
new: true
---

# `hydrateFallbackElement`

如果你使用[服务端渲染][ssr]并利用了[部分注水][partialhydration]，那么你可以为应用初始注水期间未注水的路由指定一个要渲染的 Element/Component。

<docs-info>如果你不想指定 React 元素（即 `hydrateFallbackElement={<MyFallback />}`），你可以改为指定一个 `HydrateFallback` 组件（即 `HydrateFallback={MyFallback}`），React Router 将在内部为你调用 `createElement`。</docs-info>

<docs-warning>此功能仅在使用数据路由器时有效，参见[选择路由器][pickingarouter]</docs-warning>

```tsx
let router = createBrowserRouter(
  [
    {
      id: "root",
      path: "/",
      loader: rootLoader,
      Component: Root,
      children: [
        {
          id: "invoice",
          path: "invoices/:id",
          loader: loadInvoice,
          Component: Invoice,
          HydrateFallback: InvoiceSkeleton,
        },
      ],
    },
  ],
  {
    future: {
      v7_partialHydration: true,
    },
    hydrationData: {
      root: {
        /*...*/
      },
      // 没有为 `invoice` 路由提供注水数据
    },
  },
);
```

<docs-warning>没有默认的后备组件，该路由层级将只渲染 `null`，因此建议你始终提供自己的后备元素。</docs-warning>

[pickingarouter]: ../routers/picking-a-router
[ssr]: ../guides/ssr
[partialhydration]: ../routers/create-browser-router#partial-hydration-data
