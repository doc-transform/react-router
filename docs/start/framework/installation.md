---
title: 安装
order: 1
---

# 安装

[MODES: framework]

## 简介

大多数项目从模板开始。让我们使用 React Router 官方维护的基础模板：

```shellscript nonumber
npx create-react-router@latest my-react-router-app
```

现在进入新目录并启动应用：

```shellscript nonumber
cd my-react-router-app
npm i
npm run dev
```

现在可以在浏览器中打开 `http://localhost:5173`

你可以[在 GitHub 上查看该模板][default-template]，了解如何手动搭建项目。

我们还提供了许多[可直接部署的模板][react-router-templates]供你使用：

```shellscript nonumber
npx create-react-router@latest --template remix-run/react-router-templates/<template-name>
```

---

下一节：[路由](./routing)

[manual_usage]: ../how-to/manual-usage
[default-template]: https://github.com/remix-run/react-router-templates/tree/main/default
[react-router-templates]: https://github.com/remix-run/react-router-templates
