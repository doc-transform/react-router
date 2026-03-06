---
title: 文件路由约定
---

# 文件路由约定

[MODES: framework]

<br/>
<br/>

`@react-router/fs-routes` 包启用基于文件约定的路由配置。

## 设置

首先安装 `@react-router/fs-routes` 包：

```shellscript nonumber
npm i @react-router/fs-routes
```

然后在 `app/routes.ts` 文件中使用它来提供路由配置：

```tsx filename=app/routes.ts
import { type RouteConfig } from "@react-router/dev/routes";
import { flatRoutes } from "@react-router/fs-routes";

export default flatRoutes() satisfies RouteConfig;
```

默认情况下，`app/routes` 目录中的所有模块都将成为你应用中的路由。`ignoredRouteFiles` 选项允许你指定不应作为路由包含的文件：

```tsx filename=app/routes.ts
import { type RouteConfig } from "@react-router/dev/routes";
import { flatRoutes } from "@react-router/fs-routes";

export default flatRoutes({
  ignoredRouteFiles: ["home.tsx"],
}) satisfies RouteConfig;
```

默认情况下会在 `app/routes` 目录中查找路由，但这可以通过 `rootDirectory` 选项进行配置，该选项相对于你的 app 目录：

```tsx filename=app/routes.ts
import { type RouteConfig } from "@react-router/dev/routes";
import { flatRoutes } from "@react-router/fs-routes";

export default flatRoutes({
  rootDirectory: "file-routes",
}) satisfies RouteConfig;
```

本指南的其余部分将假设你使用默认的 `app/routes` 目录。

## 基本路由

文件名映射到路由的 URL 路径名，除了 `_index.tsx` 是[根路由][root_route]的[索引路由][index_route]。你可以使用 `.js`、`.jsx`、`.ts` 或 `.tsx` 文件扩展名。

```text lines=[3-4]
app/
├── routes/
│   ├── _index.tsx
│   └── about.tsx
└── root.tsx
```

| URL      | 匹配的路由              |
| -------- | ----------------------- |
| `/`      | `app/routes/_index.tsx` |
| `/about` | `app/routes/about.tsx`  |

注意，由于[嵌套路由][nested_routing]，这些路由将在 `app/root.tsx` 的 outlet 中渲染。

## 点分隔符

在路由文件名中添加 `.` 会在 URL 中创建 `/`。

```text lines=[5-7]
 app/
├── routes/
│   ├── _index.tsx
│   ├── about.tsx
│   ├── concerts.trending.tsx
│   ├── concerts.salt-lake-city.tsx
│   └── concerts.san-diego.tsx
└── root.tsx
```

| URL                        | 匹配的路由                               |
| -------------------------- | ---------------------------------------- |
| `/`                        | `app/routes/_index.tsx`                  |
| `/about`                   | `app/routes/about.tsx`                   |
| `/concerts/trending`       | `app/routes/concerts.trending.tsx`       |
| `/concerts/salt-lake-city` | `app/routes/concerts.salt-lake-city.tsx` |
| `/concerts/san-diego`      | `app/routes/concerts.san-diego.tsx`      |

点分隔符也会创建嵌套，更多信息请参阅[嵌套部分][nested_routes]。

## 动态段

通常你的 URL 不是静态的，而是数据驱动的。动态段允许你匹配 URL 的某些段并在代码中使用该值。你可以用 `$` 前缀来创建它们。

```text lines=[5]
 app/
├── routes/
│   ├── _index.tsx
│   ├── about.tsx
│   ├── concerts.$city.tsx
│   └── concerts.trending.tsx
└── root.tsx
```

| URL                        | 匹配的路由                         |
| -------------------------- | ---------------------------------- |
| `/`                        | `app/routes/_index.tsx`            |
| `/about`                   | `app/routes/about.tsx`             |
| `/concerts/trending`       | `app/routes/concerts.trending.tsx` |
| `/concerts/salt-lake-city` | `app/routes/concerts.$city.tsx`    |
| `/concerts/san-diego`      | `app/routes/concerts.$city.tsx`    |

该值将从 URL 中解析并传递给各种 API。我们称这些值为"URL 参数"。访问 URL 参数最有用的地方是在 [loader][loaders] 和 [action][actions] 中。

```tsx
export async function loader({ params }) {
  return fakeDb.getAllConcertsForCity(params.city);
}
```

你会注意到 `params` 对象上的属性名直接映射到你的文件名：`$city.tsx` 变成 `params.city`。

路由可以有多个动态段，如 `concerts.$city.$date`，两者都通过名称在 params 对象上访问：

```tsx
export async function loader({ params }) {
  return fake.db.getConcerts({
    date: params.date,
    city: params.city,
  });
}
```

更多信息请参阅[路由指南][routing_guide]。

## 嵌套路由

嵌套路由是将 URL 段与组件层级和数据耦合的一般概念。你可以在[路由指南][nested_routing]中了解更多。

你可以使用[点分隔符][dot_delimiters]创建嵌套路由。如果 `.` 之前的文件名与另一个路由文件名匹配，它会自动成为匹配父路由的子路由。考虑这些路由：

```text lines=[5-8]
 app/
├── routes/
│   ├── _index.tsx
│   ├── about.tsx
│   ├── concerts._index.tsx
│   ├── concerts.$city.tsx
│   ├── concerts.trending.tsx
│   └── concerts.tsx
└── root.tsx
```

所有以 `app/routes/concerts.` 开头的路由将成为 `app/routes/concerts.tsx` 的子路由，并在[父路由的 outlet][nested_routing] 中渲染。

| URL                        | 匹配的路由                         | 布局                      |
| -------------------------- | ---------------------------------- | ------------------------- |
| `/`                        | `app/routes/_index.tsx`            | `app/root.tsx`            |
| `/about`                   | `app/routes/about.tsx`             | `app/root.tsx`            |
| `/concerts`                | `app/routes/concerts._index.tsx`   | `app/routes/concerts.tsx` |
| `/concerts/trending`       | `app/routes/concerts.trending.tsx` | `app/routes/concerts.tsx` |
| `/concerts/salt-lake-city` | `app/routes/concerts.$city.tsx`    | `app/routes/concerts.tsx` |

注意，当添加嵌套路由时，你通常需要添加一个索引路由，这样当用户直接访问父 URL 时，父路由的 outlet 中会渲染一些内容。

例如，如果 URL 是 `/concerts/salt-lake-city`，UI 层级将如下所示：

```tsx
<Root>
  <Concerts>
    <City />
  </Concerts>
</Root>
```

## 无布局嵌套的嵌套 URL

有时你希望 URL 是嵌套的，但不想要自动的布局嵌套。你可以在父段末尾添加下划线来退出嵌套：

```text lines=[8]
 app/
├── routes/
│   ├── _index.tsx
│   ├── about.tsx
│   ├── concerts.$city.tsx
│   ├── concerts.trending.tsx
│   ├── concerts.tsx
│   └── concerts_.mine.tsx
└── root.tsx
```

| URL                        | 匹配的路由                         | 布局                      |
| -------------------------- | ---------------------------------- | ------------------------- |
| `/`                        | `app/routes/_index.tsx`            | `app/root.tsx`            |
| `/about`                   | `app/routes/about.tsx`             | `app/root.tsx`            |
| `/concerts/mine`           | `app/routes/concerts_.mine.tsx`    | `app/root.tsx`            |
| `/concerts/trending`       | `app/routes/concerts.trending.tsx` | `app/routes/concerts.tsx` |
| `/concerts/salt-lake-city` | `app/routes/concerts.$city.tsx`    | `app/routes/concerts.tsx` |

注意 `/concerts/mine` 不再嵌套在 `app/routes/concerts.tsx` 中，而是嵌套在 `app/root.tsx` 中。`trailing_` 下划线创建了一个路径段，但不会创建布局嵌套。

可以把 `trailing_` 下划线想象成你父母签名末尾的长笔画，把你从遗嘱中除名，将后面的段从布局嵌套中移除。

## 无嵌套 URL 的嵌套布局

我们称这些为 <a name="pathless-routes"><b>无路径路由</b></a>

有时你想让一组路由共享一个布局，但不想在 URL 中添加任何路径段。一个常见的例子是一组认证路由，它们有与公共页面或登录后应用体验不同的页眉/页脚。你可以使用 `_leading` 下划线来实现这一点。

```text lines=[3-5]
 app/
├── routes/
│   ├── _auth.login.tsx
│   ├── _auth.register.tsx
│   ├── _auth.tsx
│   ├── _index.tsx
│   ├── concerts.$city.tsx
│   └── concerts.tsx
└── root.tsx
```

| URL                        | 匹配的路由                      | 布局                      |
| -------------------------- | ------------------------------- | ------------------------- |
| `/`                        | `app/routes/_index.tsx`         | `app/root.tsx`            |
| `/login`                   | `app/routes/_auth.login.tsx`    | `app/routes/_auth.tsx`    |
| `/register`                | `app/routes/_auth.register.tsx` | `app/routes/_auth.tsx`    |
| `/concerts`                | `app/routes/concerts.tsx`       | `app/routes/concerts.tsx` |
| `/concerts/salt-lake-city` | `app/routes/concerts.$city.tsx` | `app/routes/concerts.tsx` |

可以把 `_leading` 下划线想象成你拉在文件名上的一条毯子，将文件名从 URL 中隐藏起来。

## 可选段

用括号包裹路由段会使该段变为可选的。

```text lines=[3-5]
 app/
├── routes/
│   ├── ($lang)._index.tsx
│   ├── ($lang).$productId.tsx
│   └── ($lang).categories.tsx
└── root.tsx
```

| URL                        | 匹配的路由                          |
| -------------------------- | ----------------------------------- |
| `/`                        | `app/routes/($lang)._index.tsx`     |
| `/categories`              | `app/routes/($lang).categories.tsx` |
| `/en/categories`           | `app/routes/($lang).categories.tsx` |
| `/fr/categories`           | `app/routes/($lang).categories.tsx` |
| `/american-flag-speedo`    | `app/routes/($lang)._index.tsx`     |
| `/en/american-flag-speedo` | `app/routes/($lang).$productId.tsx` |
| `/fr/american-flag-speedo` | `app/routes/($lang).$productId.tsx` |

你可能会疑惑为什么 `/american-flag-speedo` 匹配了 `($lang)._index.tsx` 路由而不是 `($lang).$productId.tsx`。这是因为当你有一个可选动态参数段后面跟着另一个动态参数时，无法可靠地确定像 `/american-flag-speedo` 这样的单段 URL 应该匹配 `/:lang` 还是 `/:productId`。可选段会贪婪匹配，因此它将匹配 `/:lang`。如果你有这种设置，建议在 `($lang)._index.tsx` 的 loader 中检查 `params.lang`，如果 `params.lang` 不是有效的语言代码，则重定向到 `/:lang/american-flag-speedo`（使用当前/默认语言）。

## 通配路由

[动态段][dynamic_segments]匹配 URL 中两个 `/` 之间的单个路径段，而通配路由将匹配 URL 的其余部分，包括斜杠。

```text lines=[4,6]
 app/
├── routes/
│   ├── _index.tsx
│   ├── $.tsx
│   ├── about.tsx
│   └── files.$.tsx
└── root.tsx
```

| URL                                          | 匹配的路由               |
| -------------------------------------------- | ------------------------ |
| `/`                                          | `app/routes/_index.tsx`  |
| `/about`                                     | `app/routes/about.tsx`   |
| `/beef/and/cheese`                           | `app/routes/$.tsx`       |
| `/files`                                     | `app/routes/files.$.tsx` |
| `/files/talks/react-conf_old.pdf`            | `app/routes/files.$.tsx` |
| `/files/talks/react-conf_final.pdf`          | `app/routes/files.$.tsx` |
| `/files/talks/react-conf-FINAL-MAY_2024.pdf` | `app/routes/files.$.tsx` |

类似于动态路由参数，你可以通过通配路由 `params` 上的 `"*"` 键访问匹配路径的值。

```tsx filename=app/routes/files.$.tsx
export async function loader({ params }) {
  const filePath = params["*"];
  return fake.getFileInfo(filePath);
}
```

## 兜底路由

要创建一个匹配所有不匹配其他已定义路由请求的路由（如 404 页面），在 routes 目录中创建名为 `$.tsx` 的文件：

| URL                            | 匹配的路由              |
| ------------------------------ | ----------------------- |
| `/`                            | `app/routes/_index.tsx` |
| `/about`                       | `app/routes/about.tsx`  |
| `/any-invalid-path-will-match` | `app/routes/$.tsx`      |

默认情况下匹配的路由会返回 200 响应，因此请确保修改你的兜底路由以返回 404：

```tsx filename=app/routes/$.tsx
export async function loader() {
  return data({}, 404);
}
```

## 转义特殊字符

如果你想让这些路由约定使用的特殊字符实际上成为 URL 的一部分，可以用 `[]` 字符来转义约定。这对于在 URL 中包含扩展名的[资源路由][resource_routes]特别有用。

| 文件名                              | URL                 |
| ----------------------------------- | ------------------- |
| `app/routes/sitemap[.]xml.tsx`      | `/sitemap.xml`      |
| `app/routes/[sitemap.xml].tsx`      | `/sitemap.xml`      |
| `app/routes/weird-url.[_index].tsx` | `/weird-url/_index` |
| `app/routes/dolla-bills-[$].tsx`    | `/dolla-bills-$`    |
| `app/routes/[[so-weird]].tsx`       | `/[so-weird]`       |
| `app/routes/reports.$id[.pdf].ts`   | `/reports/123.pdf`  |

## 用文件夹组织

路由也可以是包含 `route.tsx` 文件的文件夹，该文件定义路由模块。文件夹中的其他文件不会成为路由。这允许你将代码组织在使用它们的路由附近，而不是在其他文件夹中重复功能名称。

文件夹内的文件对路由路径没有意义，路由路径完全由文件夹名称决定。

考虑这些路由：

```text
 app/
├── routes/
│   ├── _landing._index.tsx
│   ├── _landing.about.tsx
│   ├── _landing.tsx
│   ├── app._index.tsx
│   ├── app.projects.tsx
│   ├── app.tsx
│   └── app_.projects.$id.roadmap.tsx
└── root.tsx
```

其中一些或全部可以是包含自己 `route` 模块的文件夹。

```text
app/
├── routes/
│   ├── _landing._index/
│   │   ├── route.tsx
│   │   └── scroll-experience.tsx
│   ├── _landing.about/
│   │   ├── employee-profile-card.tsx
│   │   ├── get-employee-data.server.ts
│   │   ├── route.tsx
│   │   └── team-photo.jpg
│   ├── _landing/
│   │   ├── footer.tsx
│   │   ├── header.tsx
│   │   └── route.tsx
│   ├── app._index/
│   │   ├── route.tsx
│   │   └── stats.tsx
│   ├── app.projects/
│   │   ├── get-projects.server.ts
│   │   ├── project-buttons.tsx
│   │   ├── project-card.tsx
│   │   └── route.tsx
│   ├── app/
│   │   ├── footer.tsx
│   │   ├── primary-nav.tsx
│   │   └── route.tsx
│   ├── app_.projects.$id.roadmap/
│   │   ├── chart.tsx
│   │   ├── route.tsx
│   │   └── update-timeline.server.ts
│   └── contact-us.tsx
└── root.tsx
```

注意，当你将路由模块转换为文件夹时，路由模块变为 `folder/route.tsx`，文件夹中的所有其他模块不会成为路由。例如：

```
# 以下是相同的路由：
app/routes/app.tsx
app/routes/app/route.tsx

# 以下也是：
app/routes/app._index.tsx
app/routes/app._index/route.tsx
```

[route-config-file]: ../start/framework/routing#configuring-routes
[loaders]: ../start/framework/data-loading
[actions]: ../start/framework/actions
[routing_guide]: ../start/framework/routing
[root_route]: ../start/framework/route-module
[index_route]: ../start/framework/routing#index-routes
[nested_routing]: ../start/framework/routing#nested-routes
[nested_routes]: #nested-routes
[dot_delimiters]: #dot-delimiters
[dynamic_segments]: #dynamic-segments
[resource_routes]: ../how-to/resource-routes
