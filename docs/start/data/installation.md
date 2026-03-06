---
title: 安装
order: 1
---

# 安装

[MODES: data]

## 使用打包器模板创建项目

你可以从 Vite 的 React 模板开始，选择"React"，或者按照你喜欢的方式搭建应用（Parcel、Webpack 等）。

```shellscript nonumber
npx create-vite@latest
```

## 安装 React Router

接下来通过 npm 安装 React Router：

```shellscript nonumber
npm i react-router
```

## 创建路由并渲染

创建一个路由并将其传给 `RouterProvider`：

```tsx lines=[3-4,6-11,16]
import React from "react";
import ReactDOM from "react-dom/client";
import { createBrowserRouter } from "react-router";
import { RouterProvider } from "react-router/dom";

const router = createBrowserRouter([
  {
    path: "/",
    element: <div>Hello World</div>,
  },
]);

const root = document.getElementById("root");

ReactDOM.createRoot(root).render(
  <RouterProvider router={router} />,
);
```

---

下一节：[路由](./routing)
