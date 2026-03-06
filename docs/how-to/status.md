---
title: 状态码
---

# 状态码

[MODES: framework ,data]

<br/>
<br/>

使用 `data` 在 loader 和 action 中设置状态码。

```tsx filename=app/project.tsx lines=[3,12-15,20,23]
// route('/projects/:projectId', './project.tsx')
import type { Route } from "./+types/project";
import { data } from "react-router";
import { fakeDb } from "../db";

export async function action({
  request,
}: Route.ActionArgs) {
  let formData = await request.formData();
  let title = formData.get("title");
  if (!title) {
    return data({ message: "标题无效" }, { status: 400 });
  }

  if (!projectExists(title)) {
    let project = await fakeDb.createProject({ title });
    return data(project, { status: 201 });
  } else {
    let project = await fakeDb.updateProject({ title });
    // 默认状态码是 200，不需要 `data`
    return project;
  }
}
```

请参阅[表单验证](./form-validation)了解更多关于渲染此类表单错误的信息。

另一个常见的状态码是 404：

```tsx
// route('/projects/:projectId', './project.tsx')
import type { Route } from "./+types/project";
import { data } from "react-router";
import { fakeDb } from "../db";

export async function loader({ params }: Route.ActionArgs) {
  let project = await fakeDb.getProject(params.id);
  if (!project) {
    // 抛出到 ErrorBoundary
    throw data(null, { status: 404 });
  }
  return project;
}
```

请参阅[错误边界](./error-boundary)了解更多关于抛出 `data` 的信息。
