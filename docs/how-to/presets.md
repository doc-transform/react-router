---
title: 预设
---

# 预设

[MODES: framework]

<br/>
<br/>

[React Router 配置][react-router-config]支持 `presets` 选项，以简化与其他工具和托管提供商的集成。

[预设][preset-type]只能做两件事：

- 代表你配置 React Router 配置选项
- 验证已解析的配置

每个预设返回的配置按照预设定义的顺序合并。直接在你的 React Router 配置中指定的任何配置将最后合并。这意味着你的配置始终优先于任何预设。

## 定义预设配置

作为一个基本示例，让我们创建一个配置[服务端包函数][server-bundles]的预设：

```ts filename=my-cool-preset.ts
import type { Preset } from "@react-router/dev/config";

export function myCoolPreset(): Preset {
  return {
    name: "my-cool-preset",
    reactRouterConfig: () => ({
      serverBundles: ({ branch }) => {
        const isAuthenticatedRoute = branch.some((route) =>
          route.id.split("/").includes("_authenticated"),
        );

        return isAuthenticatedRoute
          ? "authenticated"
          : "unauthenticated";
      },
    }),
  };
}
```

## 验证配置

请记住，其他预设和用户配置仍然可以覆盖你的预设返回的值。

在我们的示例预设中，`serverBundles` 函数可能被不同的、冲突的实现覆盖。如果我们想验证最终解析的配置包含来自我们预设的 `serverBundles` 函数，可以使用 `reactRouterConfigResolved` 钩子：

```ts filename=my-cool-preset.ts lines=[22-27]
import type {
  Preset,
  ServerBundlesFunction,
} from "@react-router/dev/config";

const serverBundles: ServerBundlesFunction = ({
  branch,
}) => {
  const isAuthenticatedRoute = branch.some((route) =>
    route.id.split("/").includes("_authenticated"),
  );

  return isAuthenticatedRoute
    ? "authenticated"
    : "unauthenticated";
};

export function myCoolPreset(): Preset {
  return {
    name: "my-cool-preset",
    reactRouterConfig: () => ({ serverBundles }),
    reactRouterConfigResolved: ({ reactRouterConfig }) => {
      if (
        reactRouterConfig.serverBundles !== serverBundles
      ) {
        throw new Error("`serverBundles` 被覆盖了！");
      }
    },
  };
}
```

`reactRouterConfigResolved` 钩子只应在合并或覆盖你的预设配置会导致错误时使用。

## 使用预设

预设被设计为发布到 npm 并在你的 React Router 配置中使用。

```ts filename=react-router.config.ts lines=[6]
import type { Config } from "@react-router/dev/config";
import { myCoolPreset } from "react-router-preset-cool";

export default {
  // ...
  presets: [myCoolPreset()],
} satisfies Config;
```

[react-router-config]: https://api.reactrouter.com/v7/types/_react-router_dev.config.Config.html
[preset-type]: https://api.reactrouter.com/v7/types/_react-router_dev.config.Preset.html
[server-bundles]: ./server-bundles
