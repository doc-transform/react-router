---
title: Outlet
---

# `<Outlet>`

<details>
  <summary>类型声明</summary>

```tsx
interface OutletProps {
  context?: unknown;
}
declare function Outlet(
  props: OutletProps,
): React.ReactElement | null;
```

</details>

`<Outlet>` 应在父路由元素中使用，用于渲染其子路由元素。这使得嵌套 UI 能够在子路由渲染时显示出来。如果父路由精确匹配，它将渲染子索引路由，如果没有索引路由则不渲染任何内容。

```tsx
function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>

      {/* 此元素将在 URL 为 "/messages" 时渲染 <DashboardMessages>，
          在 "/tasks" 时渲染 <DashboardTasks>，在 "/" 时渲染 null
      */}
      <Outlet />
    </div>
  );
}

function App() {
  return (
    <Routes>
      <Route path="/" element={<Dashboard />}>
        <Route
          path="messages"
          element={<DashboardMessages />}
        />
        <Route path="tasks" element={<DashboardTasks />} />
      </Route>
    </Routes>
  );
}
```
