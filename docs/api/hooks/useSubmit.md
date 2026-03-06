---
title: useSubmit
---

# useSubmit

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useSubmit.html)

[`<Form>`](../components/Form) 的命令式版本，允许你从代码中提交表单，而无需用户交互。

```tsx
import { useSubmit } from "react-router";

function SomeComponent() {
  const submit = useSubmit();
  return (
    <Form
      onChange={(event) => submit(event.currentTarget)}
    />
  );
}
```

## 函数签名

```tsx
function useSubmit(): SubmitFunction;
```

## 返回值

可调用的函数，用于以命令式方式提交 [`Form`](../components/Form)。
