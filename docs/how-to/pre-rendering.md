---
title: 预渲染
---

# 预渲染

[MODES: framework]

<br/>
<br/>

预渲染允许你通过在构建时而非运行时渲染页面来加速静态内容的页面加载。

## 配置

预渲染通过 `react-router.config.ts` 中的 `prerender` 配置启用。

最简单的配置是布尔值 `true`，它将根据 `routes.ts` 预渲染应用的所有静态路径：

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

export default {
  prerender: true,
} satisfies Config;
```

布尔值 `true` 不会包含任何动态路径（即 `/blog/:slug`），因为参数值是未知的。

要配置包含动态值的特定路径，可以指定路径数组：

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

let slugs = getPostSlugs();

export default {
  prerender: [
    "/",
    "/blog",
    ...slugs.map((s) => `/blog/${s}`),
  ],
} satisfies Config;
```

如果你需要执行更复杂和/或异步的逻辑来确定路径，也可以提供一个返回路径数组的函数。此函数提供了一个 `getStaticPaths` 方法，帮助你避免手动添加应用中的所有静态路径：

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

export default {
  async prerender({ getStaticPaths }) {
    let slugs = await getPostSlugsFromCMS();
    return [
      ...getStaticPaths(), // "/" 和 "/blog"
      ...slugs.map((s) => `/blog/${s}`),
    ];
  },
} satisfies Config;
```

### 并发（实验性）

<docs-warning>此 API 是实验性的，可能在次要/补丁版本中发生破坏性变更。请谨慎使用，并**密切**关注发布说明中的相关变更。</docs-warning>

默认情况下，页面一次预渲染一个路径。你可以启用并发以并行预渲染多个路径，这在很多情况下可以加速构建时间。你应该尝试找到为你的应用提供最佳性能的值。

要指定并发数，将 `prerender` 配置移到 `prerender.paths` 字段，并在 `prerender.unstable_concurrency` 中指定并发数：

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

let slugs = getPostSlugs();

export default {
  prerender: {
    paths: [
      "/",
      "/blog",
      ...slugs.map((s) => `/blog/${s}`),
    ],
    unstable_concurrency: 4,
  },
} satisfies Config;
```

## 有/无运行时服务器的预渲染

预渲染可以根据 `ssr` 配置值以两种方式使用：

- 配合运行时 SSR 服务器使用 `ssr:true`（默认值）
- 部署到静态文件服务器使用 `ssr:false`

### 使用 `ssr:true` 预渲染

使用 `ssr:true` 预渲染时，表示你仍然有运行时服务器，但选择预渲染某些路径以获得更快的响应时间。

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

export default {
  // 可以省略 - 默认为 true
  ssr: true,
  prerender: ["/", "/blog", "/blog/popular-post"],
} satisfies Config;
```

#### 数据加载和预渲染

预渲染没有额外的应用 API。被预渲染的路由使用与服务端渲染相同的路由 `loader` 函数：

```tsx
export async function loader({ request, params }) {
  let post = await getPost(params.slug);
  return post;
}

export function Post({ loaderData }) {
  return <div>{loaderData.title}</div>;
}
```

不同于请求到达已部署服务器上的路由，构建过程会创建一个 `new Request()` 并像服务器一样运行它通过你的应用。

服务端渲染时，对未预渲染路径的请求将像往常一样进行服务端渲染。

#### 静态文件输出

渲染结果将写入你的 `build/client` 目录。你会注意到每个路径有两个文件：

- `[url].html` HTML 文件用于初始文档请求
- `[url].data` 文件用于客户端导航浏览器请求

构建的输出会显示哪些文件被预渲染了：

```sh
> react-router build
vite v5.2.11 building for production...
...
vite v5.2.11 building SSR bundle for production...
...
Prerender: Generated build/client/index.html
Prerender: Generated build/client/blog.data
Prerender: Generated build/client/blog/index.html
Prerender: Generated build/client/blog/my-first-post.data
Prerender: Generated build/client/blog/my-first-post/index.html
...
```

在开发环境中，预渲染不会将渲染结果保存到 public 目录，这只在 `react-router build` 时发生。

### 使用 `ssr:false` 预渲染

上面的示例假设你部署了运行时服务器，但预渲染了一些静态页面以避免命中服务器，从而实现更快的加载。

要禁用运行时 SSR 并配置预渲染从静态文件服务器提供服务，你可以设置 `ssr:false` 配置标志：

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

export default {
  ssr: false, // 禁用运行时服务端渲染
  prerender: true, // 预渲染所有静态路由
} satisfies Config;
```

如果你指定了 `ssr:false` 但没有 `prerender` 配置，React Router 将其称为 [SPA 模式](./spa)。在 SPA 模式中，我们渲染一个能够为应用中 _任何_ 路径注水的 HTML 文件。它之所以能这样做，是因为它只将 `root` 路由渲染到 HTML 文件中，然后在注水期间根据浏览器 URL 确定要加载哪些子路由。这意味着你可以在根路由上使用 `loader`，但不能在其他任何路由上使用，因为我们在浏览器注水之前不知道要加载哪些路由。

如果你想使用 `ssr:false` 预渲染路径，那些匹配的路由 _可以_ 有 loader，因为我们会预渲染这些路径的所有匹配路由，不仅仅是根路由。当设置 `ssr:false` 时，任何路由都不能包含 `actions` 或 `headers` 函数，因为没有运行时服务器来运行它们。

#### 使用 SPA 回退的预渲染

如果你想要 `ssr:false` 但不想预渲染 _所有_ 路由——那也没问题！你可能有些路径需要预渲染的性能/SEO 优势，但其他页面用 SPA 就行。

你可以使用配置选项的组合来实现——只需将 `prerender` 配置限制为你想预渲染的路径，React Router 还会输出一个"SPA 回退"HTML 文件，可以用来注水其他任何路径（使用与 [SPA 模式](./spa)相同的方法）。

该文件将写入以下路径之一：

- `build/client/index.html` - 如果 `/` 路径未被预渲染
- `build/client/__spa-fallback.html` - 如果 `/` 路径已被预渲染

```ts filename=react-router.config.ts
import type { Config } from "@react-router/dev/config";

export default {
  ssr: false,

  // SPA 回退将写入 build/client/index.html
  prerender: ["/about-us"],

  // SPA 回退将写入 build/client/__spa-fallback.html
  prerender: ["/", "/about-us"],
} satisfies Config;
```

你可以配置部署服务器为任何否则会 404 的路径提供此文件。有些主机默认这样做，但有些不是。例如，主机可能支持 `_redirects` 文件来实现这一点：

```
# 如果你没有预渲染 `/` 路由
/*    /index.html   200

# 如果你预渲染了 `/` 路由
/*    /__spa-fallback.html   200
```

如果你在应用的有效路由上收到 404，很可能需要配置你的主机。

这是另一个使用 [`sirv-cli`](https://www.npmjs.com/package/sirv-cli#user-content-single-page-applications) 工具的示例：

```sh
# 如果你没有预渲染 `/` 路由
sirv-cli build/client --single index.html

# 如果你预渲染了 `/` 路由
sirv-cli build/client --single __spa-fallback.html
```

#### 无效导出

使用 `ssr:false` 预渲染时，React Router 会在构建时对无效导出报错，以帮助防止一些容易被忽视的错误。

- 所有路由中都禁止 `headers`/`action` 函数，因为没有运行时服务器来运行它们
- 使用 `ssr:false` 但没有 `prerender` 配置时（SPA 模式），只允许在根路由上使用 `loader`
- 使用 `ssr:false` 并带有 `prerender` 配置时，任何被 `prerender` 路径匹配的路由都允许使用 `loader`
  - 如果你在有子路由的预渲染路由上使用 `loader`，你需要确保父 `loaderData` 在运行时能够被正确确定，通过以下方式之一：
    - 预渲染所有子路由，这样父 `loader` 可以在构建时为每个子路由路径调用并渲染到 `.data` 文件中，或者
    - 在父路由上使用 `clientLoader`，可以在运行时为未预渲染的子路径调用
