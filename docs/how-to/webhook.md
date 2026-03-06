---
title: Webhooks
# 可以写一个快速的 webhook 操作指南，此内容是从另一个文档复制粘贴的，需要先审核
hidden: true
---

# Webhooks

资源路由可以用来处理 webhook。例如，你可以创建一个 webhook 来接收 GitHub 在新提交推送到仓库时的通知：

```tsx
import type { Route } from "./+types/github";

import crypto from "node:crypto";

export const action = async ({
  request,
}: Route.ActionArgs) => {
  if (request.method !== "POST") {
    return Response.json(
      { message: "方法不允许" },
      {
        status: 405,
      },
    );
  }
  const payload = await request.json();

  /* 验证 webhook */
  const signature = request.headers.get(
    "X-Hub-Signature-256",
  );
  const generatedSignature = `sha256=${crypto
    .createHmac("sha256", process.env.GITHUB_WEBHOOK_SECRET)
    .update(JSON.stringify(payload))
    .digest("hex")}`;
  if (signature !== generatedSignature) {
    return Response.json(
      { message: "签名不匹配" },
      {
        status: 401,
      },
    );
  }

  /* 处理 webhook（例如将后台任务加入队列） */

  return Response.json({ success: true });
};
```
