---
title: useNavigation
---

# useNavigation

[MODES: framework, data]

## 概述

[参考文档 ↗](https://api.reactrouter.com/v7/functions/react-router.useNavigation.html)

返回当前的 [`Navigation`](https://api.reactrouter.com/v7/types/react-router.Navigation.html)，当没有导航进行时默认为"idle"状态。你可以用它来渲染待定 UI（如全局加载指示器）或从表单导航中读取 [`FormData`](https://developer.mozilla.org/en-US/docs/Web/API/FormData)。

```tsx
import { useNavigation } from "react-router";

function SomeComponent() {
  let navigation = useNavigation();
  navigation.state;
  navigation.formData;
  // 等等
}
```

## 函数签名

```tsx
function useNavigation(): Navigation;
```

## 返回值

当前的 [`Navigation`](https://api.reactrouter.com/v7/types/react-router.Navigation.html) 对象。
