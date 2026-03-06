---
title: useResolvedPath
---

# `useResolvedPath`

<details>
  <summary>类型声明</summary>

```tsx
declare function useResolvedPath(
  to: To,
  options?: { relative?: RelativeRoutingType },
): Path;
```

</details>

此 hook 将给定 `to` 值中的位置 `pathname` 相对于当前位置的 pathname 进行解析。

这在从相对值构建链接时很有用。例如，查看 [`<NavLink>`][navlink] 的源码，它在内部调用 `useResolvedPath` 来解析被链接页面的完整路径名。

更多信息请参见 [resolvePath][resolvepath]。

## 通配符路径

`useResolvedPath` 的原始逻辑对通配符路径的处理与其他路径不同，回头看这是不正确的/有缺陷的行为。这在 [`6.19.0`][release-6.19.0] 中被修复，但发现大量现有应用[依赖了此行为][revert-comment]，因此修复在 [`6.20.1`][release-6.20.1] 中被撤销，并在 [`6.21.0`][release-6.21.0] 中通过 `future.v7_relativeSplatPath` [future flag][future-flag] 重新引入。这将成为 React Router v7 的默认行为，因此建议在方便时更新你的应用，以便更好地为最终的 v7 升级做准备。

需要注意的是，这是 React Router 中所有相对路由的基础，因此这也适用于以下相对路径代码流：

- `<Link to>`
- `useNavigate()`
- `useHref()`
- `<Form action>`
- `useSubmit()`
- 从 loader 和 action 返回的相对路径 `redirect` 响应

### 未启用 flag 时的行为

当未启用此 flag 时，默认行为是在解析[通配符路由（`*`）][splat]内部的相对路径时，通配符部分的路径会被忽略。所以，给定如下路由树：

```jsx
<BrowserRouter>
  <Routes>
    <Route path="/dashboard/*" element={<Dashboard />} />
  </Routes>
</BrowserRouter>
```

如果你当前在 URL `/dashboard/teams`，`Dashboard` 组件内的 `useResolvedPath("projects")` 会解析为 `/dashboard/projects`，因为我们相对的“当前”位置被认为是 `/dashboard`，_不包含 "teams" 通配符值_。

这对于在“同级”通配符路由之间（`/dashboard/teams`、`/dashboard/projects` 等）导航提供了一些便利，但会导致其他不一致性，例如：

- `useResolvedPath(".")` 不再解析为该路由的当前位置，它实际上会从 `/dashboard/teams` “向上”解析到 `/dashboard`
- 如果你将路由定义更改为使用动态参数（`<Route path="/dashboard/:widget">`），那么 `Dashboard` 组件内的任何解析路径都会中断，因为动态参数值不会像通配符值那样被忽略

如果你将通配符路由定义为子路由，情况会更糟糕：

```jsx
<BrowserRouter>
  <Routes>
    <Route path="/dashboard">
      <Route path="*" element={<Dashboard />} />
    </Route>
  </Routes>
</BrowserRouter>
```

- 现在，`useResolvedPath(".")` 和 `useResolvedPath("..")` 在 `<Dashboard />` 内部解析为完全相同的路径
- 如果你使用数据路由器并在通配符路由上定义了 `action`，在 `<Dashboard>` 内部的 `<Form>` 提交会得到 405 错误，因为它们（默认情况下）提交到 `"."`，这会解析到父路由 `/dashboard`，而该路由没有 `action`。

### 启用 flag 后的行为

当你启用此 flag 时，这个“缺陷”会被修复，使得路径解析在所有路由类型之间保持一致，`useResolvedPath(".")` 始终解析为上下文路由的当前路径名。这包括任何动态参数或通配符参数值。

如果你想在通配符路由内的“同级”路由之间导航，建议将通配符路由移到其自己的子路由，并使用 `useResolvedPath("../teams")` 和 `useResolvedPath("../projects")` 父级相对路径来导航到同级的 `/dashboard` 路由。注意，这里我们还使用了 `index`，这样 URL `/dashboard` 也会渲染 `<Dashboard>` 组件。

```jsx
<BrowserRouter>
  <Routes>
    <Route path="/dashboard">
      <Route index path="*" element={<Dashboard />} />
    </Route>
  </Routes>
</BrowserRouter>
```

[navlink]: ../components/nav-link
[resolvepath]: ../utils/resolve-path
[release-6.19.0]: https://github.com/remix-run/react-router/blob/main/CHANGELOG.md#v6190
[release-6.20.1]: https://github.com/remix-run/react-router/blob/main/CHANGELOG.md#v6201
[release-6.21.0]: https://github.com/remix-run/react-router/blob/main/CHANGELOG.md#v6210
[revert-comment]: https://github.com/remix-run/react-router/issues/11052#issuecomment-1836589329
[future-flag]: ../guides/api-development-strategy
[splat]: ../route/route#splats
