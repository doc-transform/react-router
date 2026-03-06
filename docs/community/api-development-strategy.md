---
title: API 开发策略
---

# API 开发策略

React Router 是你应用的基础。我们希望确保升级到新的主版本尽可能平滑，同时仍允许我们随着 React 生态系统的发展调整和增强行为和 API。

我们的策略和动机在 [Future Flags][future-flags-blog-post] 博客文章和[开放治理模型][governance]中有更详细的讨论。

## Future Flags

当 API 以破坏性方式更改时，它会通过 future flag 引入。这允许你在它成为下一个主版本的默认行为之前，一次选择加入一个变更。

- 不启用 future flag，你的应用不会有任何变化
- 启用该 flag 会改变该功能的行为

所有当前的 future flags 都记录在 [Future Flags 指南](../upgrading/future)中，帮助你保持最新。

## 不稳定标志

不稳定标志（Unstable Flags）用于仍在设计和开发中的功能，提供给我们的用户以帮助我们做出正确的决定。

不建议在生产环境中使用不稳定标志：

- 它们会在没有警告和升级路径的情况下更改
- 它们会有 bug
- 它们没有文档
- 它们可能会被完全废弃

当你选择加入不稳定标志时，你实际上成为了项目的贡献者，而非用户。我们感谢你的帮助，但请注意你的新角色！

因为不稳定标志是实验性的，不保证会保留，我们在语义版本 (SemVer) 补丁版本中发布它们，因为它们不是新的*稳定*/_有文档的_ API。当不稳定标志稳定为 Future Flag 时，将在 SemVer 次要版本中发布，并会被正式记录并添加到 [Future Flags 指南](../upgrading/future)中。

要了解当前的不稳定标志，请关注[变更日志](../start/changelog)。

### 新功能流程示例

新功能的决策流程大致如下：

<img width="400" src="https://reactrouter.com/_docs/feature-flowchart.png" alt="新功能引入决策流程图" />

[future-flags-blog-post]: https://remix.run/blog/future-flags
[governance]: https://github.com/remix-run/react-router/blob/main/GOVERNANCE.md#new-feature-process
