---
title: useFetchers
new: true
---

# `useFetchers`

返回所有进行中的 [fetcher][usefetcher] 的数组，不包含它们的 `load`、`submit` 或 `Form` 属性（不能让父组件试图控制子组件的行为！我们从现实经验中知道这是徒劳的。）

<docs-warning>此功能仅在使用数据路由器时有效，参见[选择路由器][pickingarouter]</docs-warning>

```tsx
import { useFetchers } from "react-router-dom";

function SomeComp() {
  const fetchers = useFetchers();
  // array of inflight fetchers
}
```

这对于应用中没有创建 fetcher 但想使用其提交来参与乐观 UI 的组件很有用。

例如，想象一个 UI，侧边栏列出项目，主视图显示当前项目的复选框列表。侧边栏可以显示每个项目的已完成和总任务数。

```
+-----------------+----------------------------+
|                 |                            |
|   Soccer  (8/9) | [x] Do the dishes          |
|                 |                            |
| > Home    (2/4) | [x] Fold laundry           |
|                 |                            |
|                 | [ ] Replace battery in the |
|                 |     smoke alarm            |
|                 |                            |
|                 | [ ] Change lights in kids  |
|                 |     bathroom               |
|                 |                            |
+-----------------+----------------------------┘
```

当用户点击复选框时，提交会发送到 action 以更改任务的状态。我们不想创建“加载状态”，而是想创建“乐观 UI”，它会**立即**更新复选框显示为已选中，即使服务器尚未处理它。在复选框组件中，我们可以使用 `fetcher.formData`：

```tsx
function Task({ task }) {
  const { projectId, id } = task;
  const toggle = useFetcher();
  const checked = toggle.formData
    ? toggle.formData.get("complete") === "on"
    : task.complete;

  return (
    <toggle.Form
      method="put"
      action={`/projects/${projectId}/tasks/${id}`}
    >
      <input name="id" type="hidden" defaultValue={id} />
      <label>
        <input
          name="complete"
          type="checkbox"
          checked={checked}
          onChange={(e) => toggle.submit(e.target.form)}
        />
      </label>
    </toggle.Form>
  );
}
```

这对于复选框很棒，但当用户点击其中一个时，侧边栏会显示 2/4，而复选框显示 3/4！

```
+-----------------+----------------------------+
|                 |                            |
|   Soccer  (8/9) | [x] Do the dishes          |
|                 |                            |
| > Home    (2/4) | [x] Fold laundry           |
|     WRONG! ^    |                            |
|          CLICK!-->[x] Replace battery in the |
|                 |     smoke alarm            |
|                 |                            |
|                 | [ ] Change lights in kids  |
|                 |     bathroom               |
|                 |                            |
+-----------------+----------------------------┘
```

因为路由会自动重新验证，侧边栏会很快更新并变得正确。但有一刻，它会感觉有点奇怪。

这就是 `useFetchers` 的用武之地。在侧边栏中，我们可以访问复选框的所有进行中的 fetcher 状态——即使不是创建它们的组件。

策略有三个步骤：

1. 查找特定项目中任务的提交
2. 使用 `fetcher.formData` 立即更新计数
3. 如果任务不在进行中，则使用正常任务状态

```tsx
function ProjectTaskCount({ project }) {
  let completedTasks = 0;
  const fetchers = useFetchers();

  // Find this project's fetchers
  const relevantFetchers = fetchers.filter((fetcher) => {
    return fetcher.formAction?.startsWith(
      `/projects/${project.id}/tasks/`,
    );
  });

  // Store in a map for easy lookup
  const myFetchers = new Map(
    relevantFetchers.map(({ formData }) => [
      formData.get("id"),
      formData.get("complete") === "on",
    ]),
  );

  // Increment the count
  for (const task of project.tasks) {
    if (myFetchers.has(task.id)) {
      if (myFetchers.get(task.id)) {
        // if it's being submitted, increment optimistically
        completedTasks++;
      }
    } else if (task.complete) {
      // otherwise use the real task's data
      completedTasks++;
    }
  }

  return (
    <small>
      {completedTasks}/{project.tasks.length}
    </small>
  );
}
```

需要做一点工作，但主要是向 React Router 询问它正在跟踪的状态，并基于此进行乐观计算。

[usefetcher]: ./use-fetcher
[pickingarouter]: ../routers/picking-a-router
