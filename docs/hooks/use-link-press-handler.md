---
title: useLinkPressHandler
---

# `useLinkPressHandler`

<details>
  <summary>类型声明</summary>

```tsx
declare function useLinkPressHandler(
  to: To,
  options?: {
    replace?: boolean;
    state?: any;
  },
): (event: GestureResponderEvent) => void;
```

</details>

`react-router-native` 中与 `useLinkClickHandler` 对应的 hook，`useLinkPressHandler` 返回一个用于自定义 `<Link>` 导航的按压事件处理器。

```tsx
import { TouchableHighlight } from "react-native";
import { useLinkPressHandler } from "react-router-native";

function Link({
  onPress,
  replace = false,
  state,
  to,
  ...rest
}) {
  let handlePress = useLinkPressHandler(to, {
    replace,
    state,
  });

  return (
    <TouchableHighlight
      {...rest}
      onPress={(event) => {
        onPress?.(event);
        if (!event.defaultPrevented) {
          handlePress(event);
        }
      }}
    />
  );
}
```
