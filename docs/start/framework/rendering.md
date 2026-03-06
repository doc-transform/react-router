---
title: 渲染策略
order: 4
---

# 渲染策略

[MODES: framework]

## 简介

React Router 提供三种渲染策略：

- 客户端渲染
- 服务端渲染
- 静态预渲染

## 客户端渲染

当用户在应用中导航时，路由始终以客户端渲染方式呈现。如果你想构建单页应用（SPA），可以禁用服务端渲染：

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

export default {
  ssr: false,
} satisfies Config;
```

## 服务端渲染

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

export default {
  ssr: true,
} satisfies Config;
```

服务端渲染需要支持它的部署环境。虽然这是全局设置，但单独的路由仍然可以进行静态预渲染。路由还可以使用 `clientLoader` 进行客户端数据加载，以避免其 UI 部分的服务端渲染/请求。

## 静态预渲染

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

export default {
  // 返回要在构建时预渲染的 URL 列表
  async prerender() {
    return ["/", "/about", "/contact"];
  },
} satisfies Config;
```

预渲染是一种构建时操作，为一组 URL 生成静态 HTML 和客户端导航数据。这对 SEO 和性能很有帮助，尤其适用于没有服务端渲染的部署。预渲染时，路由模块的 loader 用于在构建时获取数据。

---

下一节：[数据加载](./data-loading)
