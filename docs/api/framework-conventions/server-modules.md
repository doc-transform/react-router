---
title: .server 模块
---

# `.server` 模块

[MODES: framework]

## 概述

仅限服务端的模块，从客户端包中排除，只在服务端运行。

```ts filename=auth.server.ts
// 如果不是从仅服务端模块导出，这将在客户端暴露密钥
export const JWT_SECRET = process.env.JWT_SECRET;

export function validateToken(token: string) {
  // 仅服务端的认证逻辑
}
```

`.server` 模块是将整个模块明确标记为仅限服务端的好方法。如果 `.server` 文件或 `.server` 目录中的任何代码意外出现在客户端模块图中，构建将会失败。

<docs-warning>

路由模块不应被标记为 `.server` 或 `.client`，因为它们有特殊处理，需要在服务端和客户端模块图中都被引用。尝试这样做会导致构建错误。

</docs-warning>

<docs-info>

如果你需要更精细地控制客户端/服务端包中包含的内容，请查看 [`vite-env-only` 插件](https://github.com/pcattori/vite-env-only)。

</docs-info>

## 使用模式

### 单个文件

通过在文件名中添加 `.server` 将单个文件标记为仅服务端：

```txt
app/
├── auth.server.ts         👈 仅服务端文件
├── database.server.ts
├── email.server.ts
└── root.tsx
```

### 服务端目录

通过在目录名中使用 `.server` 将整个目录标记为仅服务端：

```txt
app/
├── .server/               👈 整个目录为仅服务端
│   ├── auth.ts
│   ├── database.ts
│   └── email.ts
├── components/
└── root.tsx
```

## 示例

### 数据库连接

```ts filename=app/utils/db.server.ts
import { PrismaClient } from "@prisma/client";

// 这会在客户端暴露数据库凭据
const db = new PrismaClient({
  datasources: {
    db: {
      url: process.env.DATABASE_URL,
    },
  },
});

export { db };
```

### 认证工具

```ts filename=app/utils/auth.server.ts
import jwt from "jsonwebtoken";
import bcrypt from "bcryptjs";

const JWT_SECRET = process.env.JWT_SECRET!;

export function hashPassword(password: string) {
  return bcrypt.hash(password, 10);
}

export function verifyPassword(
  password: string,
  hash: string,
) {
  return bcrypt.compare(password, hash);
}

export function createToken(userId: string) {
  return jwt.sign({ userId }, JWT_SECRET, {
    expiresIn: "7d",
  });
}

export function verifyToken(token: string) {
  return jwt.verify(token, JWT_SECRET) as {
    userId: string;
  };
}
```

### 使用服务端模块

```tsx filename=app/routes/login.tsx
import type { ActionFunctionArgs } from "react-router";
import { redirect } from "react-router";
import {
  hashPassword,
  createToken,
} from "../utils/auth.server";
import { db } from "../utils/db.server";

export async function action({
  request,
}: ActionFunctionArgs) {
  const formData = await request.formData();
  const email = formData.get("email") as string;
  const password = formData.get("password") as string;

  // 仅服务端操作
  const hashedPassword = await hashPassword(password);
  const user = await db.user.create({
    data: { email, password: hashedPassword },
  });

  const token = createToken(user.id);

  return redirect("/dashboard", {
    headers: {
      "Set-Cookie": `token=${token}; HttpOnly; Secure; SameSite=Strict`,
    },
  });
}

export default function Login() {
  return (
    <form method="post">
      <input name="email" type="email" required />
      <input name="password" type="password" required />
      <button type="submit">Login</button>
    </form>
  );
}
```
