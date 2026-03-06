---
title: routes.ts
order: 2
---

# routes.ts

[MODES: framework]

## 概述

<docs-info>
此文件是必需的
</docs-info>

[参考文档 ↗](https://api.reactrouter.com/v7/interfaces/_react-router_dev.routes.RouteConfigEntry.html)

将 URL 模式映射到应用中路由模块的配置文件。

详情请参阅[路由指南][routing]。

## 示例

### 基本用法

将路由配置为对象数组。

```tsx filename=app/routes.ts
import {
  type RouteConfig,
  route,
} from "@react-router/dev/routes";

export default [
  route("some/path", "./some/file.tsx"),
  // 模式 ^           ^ 模块文件
] satisfies RouteConfig;
```

你可以使用以下辅助函数创建路由配置项：

- [`route`][route] — 创建路由配置项的辅助函数
- [`index`][index] — 创建索引路由配置项的辅助函数
- [`layout`][layout] — 创建布局路由配置项的辅助函数
- [`prefix`][prefix] — 为一组路由添加路径前缀而无需引入父路由的辅助函数
- [`relative`][relative] — 创建一组路由配置辅助函数，相对于给定目录解析文件路径。设计用于支持将路由配置拆分到不同目录的多个文件中

### 文件系统路由

如果你更喜欢通过文件命名约定而非配置来定义路由，`@react-router/fs-routes` 包提供了[文件系统路由约定][file-route-conventions]：

```ts filename=app/routes.ts
import { type RouteConfig } from "@react-router/dev/routes";
import { flatRoutes } from "@react-router/fs-routes";

export default flatRoutes() satisfies RouteConfig;
```

### 路由辅助函数

[routing]: ../../start/framework/routing
[route]: https://api.reactrouter.com/v7/functions/_react-router_dev.routes.route.html
[index]: https://api.reactrouter.com/v7/functions/_react-router_dev.routes.index.html
[layout]: https://api.reactrouter.com/v7/functions/_react-router_dev.routes.layout.html
[prefix]: https://api.reactrouter.com/v7/functions/_react-router_dev.routes.prefix.html
[relative]: https://api.reactrouter.com/v7/functions/_react-router_dev.routes.relative.html
[file-route-conventions]: ../../how-to/file-route-conventions
