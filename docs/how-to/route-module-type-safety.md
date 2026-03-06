---
title: 路由模块类型安全
---

# 路由模块类型安全

[MODES: framework]

<br/>
<br/>

React Router 生成特定于路由的类型，以支持 URL 参数、loader 数据等的类型推断。
本指南将帮助你在未使用模板创建项目时进行设置。

要了解更多关于 React Router 中类型安全工作原理的信息，请查看[类型安全说明](../explanation/type-safety)。

## 1. 将 `.react-router/` 添加到 `.gitignore`

React Router 将类型生成到应用根目录下的 `.react-router/` 目录中。该目录完全由 React Router 管理，应该加入 gitignore。

```txt
.react-router/
```

## 2. 在 tsconfig 中包含生成的类型

编辑 tsconfig 让 TypeScript 使用生成的类型。此外，需要配置 `rootDirs`，以便这些类型可以作为路由模块的相对兄弟模块进行导入。

```json filename=tsconfig.json
{
  "include": [".react-router/types/**/*"],
  "compilerOptions": {
    "rootDirs": [".", "./.react-router/types"]
  }
}
```

如果你的应用使用了多个 `tsconfig` 文件，需要在包含你的 app 目录的那个文件中进行这些更改。
例如，[`node-custom-server` 模板](https://github.com/remix-run/react-router-templates/tree/390fcec476dd336c810280479688fe893da38713/node-custom-server)包含 `tsconfig.json`、`tsconfig.node.json` 和 `tsconfig.vite.json`。由于 `tsconfig.vite.json` 是[包含 app 目录](https://github.com/remix-run/react-router-templates/blob/390fcec476dd336c810280479688fe893da38713/node-custom-server/tsconfig.vite.json#L4-L6)的那个，所以应该在它里面设置 `.react-router/types` 以实现路由模块类型安全。

## 3. 在类型检查前生成类型

如果你想将类型检查作为独立命令运行——例如作为持续集成管道的一部分——你需要确保在运行类型检查 _之前_ 生成类型：

```json
{
  "scripts": {
    "typecheck": "react-router typegen && tsc"
  }
}
```

## 4. 定义 `AppLoadContext` 类型

## 扩展应用 `Context` 类型

要定义应用的 `context` 类型，请在项目中的 `.ts` 或 `.d.ts` 文件中添加以下内容：

```typescript
import "react-router";
declare module "react-router" {
  interface AppLoadContext {
    // 在这里添加 context 属性
  }
}
```

## 5. 仅类型自动导入（可选）

当自动导入 `Route` 类型辅助器时，TypeScript 会生成：

```ts filename=app/routes/my-route.tsx
import { Route } from "./+types/my-route";
```

但如果你启用了 [verbatimModuleSyntax](https://www.typescriptlang.org/tsconfig/#verbatimModuleSyntax)：

```json filename=tsconfig.json
{
  "compilerOptions": {
    "verbatimModuleSyntax": true
  }
}
```

那么导入会自动添加 `type` 修饰符：

```ts filename=app/routes/my-route.tsx
import type { Route } from "./+types/my-route";
//     ^^^^
```

这有助于打包工具等工具检测可以安全排除在包外的仅类型模块。

## 总结

React Router 的 Vite 插件应该会在你编辑路由配置（`routes.ts`）时自动将类型生成到 `.react-router/types/` 目录。
这意味着你只需要运行 `react-router dev`（或你的自定义开发服务器）就能获取路由中最新的类型。

查看我们的[类型安全说明](../explanation/type-safety)了解如何将这些类型引入到路由中的示例。
