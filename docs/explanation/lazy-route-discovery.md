---
title: 延迟路由发现
---

# 延迟路由发现

[MODES: framework]

<br/>
<br/>

延迟路由发现是一种性能优化，在用户浏览应用时渐进式地加载路由信息，而不是在初始时加载完整的路由清单。

启用延迟路由发现后（默认启用），React Router 在清单中只发送初始服务端渲染所需的路由。当用户导航到应用的新部分时，额外的路由信息会被动态获取并添加到客户端清单中。

路由清单包含关于路由的元数据（JavaScript/CSS 导入、路由是否有 `loader`/`action` 等），但不包含实际的路由模块实现。这允许 React Router 理解应用结构而无需下载不必要的路由信息。

## 路由发现过程

当用户导航到当前清单中不存在的新路由时：

1. **路由发现请求** - React Router 向内部 `/__manifest` 端点发出请求
2. **清单补丁** - 服务器响应所需的路由信息
3. **路由加载** - React Router 加载必要的路由模块和数据
4. **导航** - 用户导航到新路由

## 预发现优化

为了防止导航瀑布流，React Router 实现了预发现机制。当前页面上渲染的所有 [`<Link>`](../api/components/Link) 和 [`<NavLink>`](../api/components/NavLink) 组件会通过一个批量请求自动向服务器发现。

此发现请求通常在用户点击任何链接之前就已完成，使得后续导航即使启用了延迟路由发现也感觉是同步的。

```tsx
// 链接默认自动被发现
<Link to="/dashboard">仪表盘</Link>

// 为特定链接选择退出发现
<Link to="/admin" discover="none">管理员</Link>
```

## 性能优势

延迟路由发现提供了多项性能改进：

- **更快的初始加载** - 通过排除未使用的路由元数据减小初始包大小
- **减少内存使用** - 路由信息仅在需要时加载
- **可扩展性** - 拥有数百个路由的应用会看到更显著的改善

## 配置

你可以在 `react-router.config.ts` 中配置路由发现行为：

```tsx filename=react-router.config.ts
export default {
  // 默认：使用 /__manifest 端点的延迟发现
  routeDiscovery: {
    mode: "lazy",
    manifestPath: "/__manifest",
  },

  // 自定义清单路径（适用于同一域名上的多个应用）
  routeDiscovery: {
    mode: "lazy",
    manifestPath: "/my-app-manifest",
  },

  // 禁用延迟发现（初始包含所有路由）
  routeDiscovery: { mode: "initial" },
} satisfies Config;
```

## 部署注意事项

使用延迟路由发现时，确保你的部署配置正确处理清单请求：

- **路由处理** - 确保 `/__manifest` 请求能到达你的 React Router 处理器
- **CDN 缓存** - 如果使用 CDN/边缘缓存，在清单端点的缓存键中包含 `version` 和 `paths` 查询参数
- **多应用** - 如果在同一域名上运行多个 React Router 应用，请使用自定义 `manifestPath`
