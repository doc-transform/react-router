---
title: 参与贡献
---

# 为 React Router 做贡献

感谢你的贡献，你真棒！

说到开源，有许多不同类型的贡献可以做出，所有这些都很有价值。以下是一些指南，应该能在你准备贡献时帮助到你。

## 开放治理模型

在继续之前，请阅读开放治理[博客文章](https://remix.run/blog/rr-governance)和[文档](https://github.com/remix-run/react-router/blob/main/GOVERNANCE.md)，了解我们如何处理 React Router 中的 bug/issue/功能提案。

## 环境设置

在你能为代码库做贡献之前，你需要 fork 该仓库。根据你做出的贡献类型，操作会有所不同：

- 所有新功能、bug 修复或**任何涉及 `react-router` 代码的更改**应该从 `dev` 分支创建并合并到 `dev` 分支
- 仅涉及文档的更改可以从 `main` 分支创建并合并到 `main` 分支

以下步骤将帮助你设置好环境以贡献代码：

1. Fork 该仓库（点击[此页面](https://github.com/remix-run/react-router)右上角的 <kbd>Fork</kbd> 按钮）
2. 在本地克隆你的 fork

   ```bash
   # 在终端中，cd 到你想放置克隆的父目录，然后
   git clone https://github.com/<your_github_username>/react-router.git
   cd react-router

   # 如果你要做*任何*代码更改，请确保切换到 dev 分支
   git checkout dev
   ```

3. 安装依赖并构建。React Router 使用 [pnpm](https://pnpm.io)，所以你也应该使用。如果使用 `npm` 安装，会生成不必要的 `package-lock.json` 文件。

## 认为发现了 Bug？

请遵循 issue 模板并提供**最小化**且**可运行**的复现。最好是提交一个包含[失败测试](https://github.com/remix-run/react-router/blob/dev/integration/bug-report-test.ts)的 pull request。次好的是提供一个说明该 bug 的 [StackBlitz](https://reactrouter.com/new)、CodeSandbox 或 GitHub 仓库链接。

## Issue 没有得到关注？

如果你需要修复一个 bug 而没有人在修复它，最好的办法是自己提供修复并创建一个 [pull request](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request)。开源代码属于我们所有人，推动它前进是我们所有人的责任。

## 提议新的或更改的 API？

⚠️ _请不要直接用 PR 来提出新功能。_

新功能需要经过[开放治理模型](https://github.com/remix-run/react-router/blob/main/GOVERNANCE.md#new-feature-process)中概述的流程，可以通过在 GitHub 上开启一个[提案讨论](https://github.com/remix-run/react-router/discussions/new?category=proposals)来启动。请提供深思熟虑的评论和一些示例代码，展示你想用 React Router 在你的应用中做什么。如果你能先展示你受当前 API 限制的地方，然后再跳到需要更改和/或添加什么的结论，这有助于对话。

我们从经验中学到，小型 API 通常更好，所以我们可能会对添加新东西有些不情愿，除非当前 API 存在明显的局限性。话虽如此，我们总是渴望听到我们之前没有考虑到的用例，所以请不要害羞！:)

## 添加示例？

示例可以直接添加到 `main` 分支。从你本地的 `main` 克隆创建一个分支。完成后，创建一个 pull request 并概述你的示例。

## 提交 Pull Request？

Pull request 只需要两个或更多协作者的批准即可合并；当 PR 作者是协作者时，这算作一个。

<docs-warning>在 GitHub 中创建 PR 时，请确保将 base 设置为正确的分支。如果你提交的 PR 涉及任何代码，应该是 `dev` 分支。你在 GitHub 中创建 PR 时通过"Compare changes"标题下的下拉菜单设置 base：<img src="https://raw.githubusercontent.com/remix-run/react-router/main/static/base-branch.png" alt="" width="460" height="350" /></docs-warning>

### 测试

所有修复 bug 或添加功能的提交都需要一个或多个测试。

<docs-error>不要合并没有测试的代码！</docs-error>

### 文档 + 示例

所有更改或添加 API 的提交都必须在同时更新所有相关示例和文档的 pull request 中完成。

文档位于 `docs` 目录中。一旦更改进入 `main` 分支，它们将自动发布到文档站点。

如果你想预览更改在文档站点上的效果，请克隆 [`react-router-website` 仓库](https://github.com/remix-run/react-router-website)并按照 `README.md` 中的说明在本地查看你的更改。

## 开发

### 包

React Router 使用 monorepo 来托管多个包的代码。这些包位于 `packages` 目录中。

我们使用 [pnpm workspaces](https://pnpm.io/workspaces/) 来管理依赖安装和运行各种脚本。要安装所有内容，请确保你已[安装 pnpm](https://pnpm.io/installation)，然后从仓库根目录运行 `pnpm install`。

### 构建

从根目录调用 `pnpm build` 将运行构建，这应该只需要几秒钟。将所有包一起构建很重要，因为各个包之间存在依赖关系。

### 测试

在运行测试之前，你需要先运行构建。构建完成后，从根目录运行 `pnpm test` 将运行**每个**包的测试。如果你想运行特定包的测试，请使用 `pnpm test packages/<package-name>/`：

```bash
# 测试所有包
pnpm test

# 仅测试 @react-router/dev
pnpm test packages/react-router-dev/
```

## 仓库分支

此仓库为不同目的维护独立的分支。它们大致如下：

```
- main   > 最新发布版本和当前文档
- dev    > 稳定版本之间的活跃开发代码
- v6     > 特定主版本的最新代码
```

可能还有其他用于各种功能和实验的分支，但所有魔法都发生在这些分支上。

## 发布

请参阅 [DEVELOPMENT.md](https://github.com/remix-run/react-router/blob/main/DEVELOPMENT.md) 了解发布流程的概述。
