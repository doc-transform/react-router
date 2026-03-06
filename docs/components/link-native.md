---
title: Link (RN)
---

# `<Link>` (React Native)

<docs-info>这是 `<Link>` 的 React Native 版本。Web 版本请[点击这里][link]。</docs-info>

<details>
  <summary>类型声明</summary>

```tsx
declare function Link(props: LinkProps): React.ReactElement;

interface LinkProps extends TouchableHighlightProps {
  children?: React.ReactNode;
  onPress?(event: GestureResponderEvent): void;
  replace?: boolean;
  state?: any;
  to: To;
}
```

</details>

`<Link>` 是一个让用户通过点击导航到另一个视图的元素，类似于 Web 应用中 `<a>` 元素的工作方式。在 `react-router-native` 中，`<Link>` 渲染一个 `TouchableHighlight`。要覆盖默认样式和行为，请参阅 [`TouchableHighlight` 的 Props 参考文档](https://reactnative.dev/docs/touchablehighlight#props)。

```tsx
import * as React from "react";
import { View, Text } from "react-native";
import { Link } from "react-router-native";

function Home() {
  return (
    <View>
      <Text>Welcome!</Text>
      <Link to="/profile">
        <Text>Visit your profile</Text>
      </Link>
    </View>
  );
}
```

[link]: ./link
