---
title: useNavigationType
---

# `useNavigationType`

<details>
  <summary>类型声明</summary>

```tsx
declare function useNavigationType(): NavigationType;

type NavigationType = "POP" | "PUSH" | "REPLACE";
```

</details>

此 hook 返回当前的导航类型，即用户如何到达当前页面；通过历史栈上的 pop、push 或 replace 操作。
