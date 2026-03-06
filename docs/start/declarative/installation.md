---
title: 安装
order: 1
---

# 安装

[MODES: declarative]

## 简介

你可以从 Vite 的 React 模板开始，选择"React"，或者按照你喜欢的方式搭建应用。

```shellscript nonumber
npx create-vite@latest
```

接下来通过 npm 安装 React Router：

```shellscript nonumber
npm i react-router
```

最后，在你的应用外层渲染一个 `<BrowserRouter>`：

```tsx lines=[3,9-11]
import React from "react";
import ReactDOM from "react-dom/client";
import { BrowserRouter } from "react-router";
import App from "./app";

const root = document.getElementById("root");

ReactDOM.createRoot(root).render(
  <BrowserRouter>
    <App />
  </BrowserRouter>,
);
```

---

下一节：[路由](./routing)
