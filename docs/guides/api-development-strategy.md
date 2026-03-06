---
title: API 开发策略
new: true
---

# API 开发策略

React Router 是你应用的基础。我们希望确保升级到新的主版本尽可能顺畅，同时仍允许我们随着 React 生态的发展调整和增强行为和 API。

我们的策略和动机在我们的 [Future Flags][future-flags-blog-post] 博客文章中有更详细的讨论。

## Future Flags

当 API 以破坏性的方式变更时，会通过 future flag 引入。这允许你在它成为下一个主版本的默认行为之前逐个选择启用。

- 不启用 future flag 时，你的应用不会有任何变化
- 启用 flag 会改变该功能的行为

所有当前的 future flag 都记录在 [Future Flags 指南](../upgrading/future) 中，帮助你保持更新。

## Unstable Flags

Unstable flag 用于仍在设计和开发中的功能，并提供给我们的用户来帮助我们做得更好。

不建议在生产环境中使用 Unstable flag：

- 它们会在没有警告和升级路径的情况下变更
- 它们会有 bug
- 它们没有文档
- 它们可能会被完全废弃

当你启用一个 unstable flag 时，你就成为了项目的贡献者，而不仅仅是用户。我们感谢你的帮助，但请注意你的新角色！

要了解当前的 unstable flag，请关注 [CHANGELOG](../start/changelog)。

### 新功能流程示例

新功能的决策流程大致如下（注意此图是关于 Remix v1/v2 的，但也适用于 React Router v6/v7）：

![新功能引入决策流程图][feature-flowchart]

[future-flags-blog-post]: https://remix.run/blog/future-flags
[feature-flowchart]: https://remix.run/docs-images/feature-flowchart.png
[picking-a-router]: ../routers/picking-a-router
