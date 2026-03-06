---
title: 文件上传
---

# 文件上传

[MODES: framework]

<br/>
<br/>

_感谢 David Adams [编写了原始指南](https://programmingarehard.com/2024/09/06/remix-file-uploads-updated.html/)，本文档基于该指南。你可以参考它获取更多示例。_

## 基本文件上传

### 1. 设置路由

你可以按照自己喜欢的方式设置路由。本示例使用以下结构：

```ts filename=routes.ts
import {
  type RouteConfig,
  route,
} from "@react-router/dev/routes";

export default [
  // ... 其他路由
  route("user/:id", "pages/user-profile.tsx", [
    route("avatar", "api/avatar.tsx"),
  ]),
] satisfies RouteConfig;
```

### 2. 添加表单数据解析器

`form-data-parser` 是 `request.formData()` 的封装，提供处理文件上传的流式支持。

```shellscript
npm i @remix-run/form-data-parser
```

[查看 `form-data-parser` 文档了解更多信息][form-data-parser]

### 3. 创建带上传 action 的路由

`parseFormData` 函数接受一个 `uploadHandler` 函数作为参数。该函数将在表单中的每个文件上传时被调用。

<docs-warning>

你必须将表单的 `enctype` 设置为 `multipart/form-data` 才能使文件上传生效。

</docs-warning>

```tsx filename=pages/user-profile.tsx
import {
  type FileUpload,
  parseFormData,
} from "@remix-run/form-data-parser";
import type { Route } from "./+types/user-profile";

export async function action({
  request,
}: Route.ActionArgs) {
  const uploadHandler = async (fileUpload: FileUpload) => {
    if (fileUpload.fieldName === "avatar") {
      // 处理上传并返回一个 File
    }
  };

  const formData = await parseFormData(
    request,
    uploadHandler,
  );
  // 此时 'avatar' 已经被处理完毕
  const file = formData.get("avatar");
}

export default function Component() {
  return (
    <form method="post" encType="multipart/form-data">
      <input type="file" name="avatar" />
      <button>提交</button>
    </form>
  );
}
```

## 本地存储实现

### 1. 添加存储包

`file-storage` 是一个用于在 JavaScript 中存储 [File 对象][file]的键值接口。类似于 `localStorage` 允许你在浏览器中存储键值对字符串，file-storage 允许你在服务器上存储键值对文件。

```shellscript
npm i @remix-run/file-storage
```

[查看 `file-storage` 文档了解更多信息][file-storage]

### 2. 创建存储配置

创建一个文件导出 `LocalFileStorage` 实例，供不同路由使用。

```ts filename=avatar-storage.server.ts
import { LocalFileStorage } from "@remix-run/file-storage/local";

export const fileStorage = new LocalFileStorage(
  "./uploads/avatars",
);

export function getStorageKey(userId: string) {
  return `user-${userId}-avatar`;
}
```

### 3. 实现上传处理器

更新表单的 `action` 以将文件存储到 `fileStorage` 实例中。

```tsx filename=pages/user-profile.tsx
import {
  type FileUpload,
  parseFormData,
} from "@remix-run/form-data-parser";
import {
  fileStorage,
  getStorageKey,
} from "~/avatar-storage.server";
import type { Route } from "./+types/user-profile";

export async function action({
  request,
  params,
}: Route.ActionArgs) {
  async function uploadHandler(fileUpload: FileUpload) {
    if (
      fileUpload.fieldName === "avatar" &&
      fileUpload.type.startsWith("image/")
    ) {
      let storageKey = getStorageKey(params.id);

      // FileUpload 对象不应该长时间保留（它们是从 request.body 中流式读取的数据）；
      // 请尽快存储它们。
      await fileStorage.set(storageKey, fileUpload);

      // 返回一个 File 给 FormData 对象。这是一个 LazyFile，
      // 它知道如何在需要时访问文件内容（例如使用 file.stream()），
      // 但会等到被请求时才实际读取任何内容。
      return fileStorage.get(storageKey);
    }
  }

  const formData = await parseFormData(
    request,
    uploadHandler,
  );
}

export default function UserPage({
  actionData,
  params,
}: Route.ComponentProps) {
  return (
    <div>
      <h1>用户 {params.id}</h1>
      <form
        method="post"
        // 表单的 enctype 必须设置为 "multipart/form-data" 才能进行文件上传
        encType="multipart/form-data"
      >
        <input type="file" name="avatar" accept="image/*" />
        <button>提交</button>
      </form>

      <img
        src={`/user/${params.id}/avatar`}
        alt="用户头像"
      />
    </div>
  );
}
```

### 4. 添加路由来提供上传的文件

创建一个[资源路由][resource-route]，将文件作为响应流式传输。

```tsx filename=api/avatar.tsx
import {
  fileStorage,
  getStorageKey,
} from "~/avatar-storage.server";
import type { Route } from "./+types/avatar";

export async function loader({ params }: Route.LoaderArgs) {
  const storageKey = getStorageKey(params.id);
  const file = await fileStorage.get(storageKey);

  if (!file) {
    throw new Response("未找到用户头像", {
      status: 404,
    });
  }

  return new Response(file.stream(), {
    headers: {
      "Content-Type": file.type,
      "Content-Disposition": `attachment; filename=${file.name}`,
    },
  });
}
```

[form-data-parser]: https://www.npmjs.com/package/@remix-run/form-data-parser
[file-storage]: https://www.npmjs.com/package/@remix-run/file-storage
[file]: https://developer.mozilla.org/en-US/docs/Web/API/File
[resource-route]: ../how-to/resource-routes
