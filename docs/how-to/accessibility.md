---
title: 无障碍
---

# 无障碍

React Router 应用中的无障碍与 Web 无障碍总体上非常相似。使用正确的语义化标记并遵循 [Web 内容无障碍指南 (WCAG)][wcag] 能帮你完成大部分工作。

React Router 在可能的情况下将某些无障碍实践设为默认，并在无法自动处理的地方提供 API 来辅助你。

## 链接

[MODES: framework, data, declarative]

<br/>
<br/>

[`<Link>` 组件][link]渲染标准的锚点标签，这意味着你可以免费获得浏览器提供的无障碍行为！

React Router 还提供了 [`<NavLink/>`][navlink]，它的行为与 `<Link>` 相同，但当链接指向当前页面时，会为辅助技术提供上下文信息。这对于构建导航菜单或面包屑导航非常有用。

## 路由

[MODES: framework]

<br/>
<br/>

如果你在应用中渲染了 [`<Scripts>`][scripts]，有一些重要的事项需要考虑，以便为用户提供更好的客户端路由无障碍体验。

对于传统的多页面网站，我们不需要过多考虑路由切换的问题。你的应用渲染锚点标签，浏览器会处理其余的工作。如果用户禁用了 JavaScript，你的 React Router 应用默认应该已经可以这样工作了！

当 React Router 中的客户端脚本加载后，React Router 接管路由并阻止浏览器的默认行为。React Router 不会对路由切换时的 UI 做任何假设。因此有一些重要的功能你需要考虑，包括：

- **焦点管理：** 当路由切换时，哪个元素获得焦点？这对键盘用户很重要，对屏幕阅读器用户也很有帮助。
- **实时区域公告：** 屏幕阅读器用户也需要在路由切换时获得通知。根据变更的性质和预期的加载时间，你可能还想在某些过渡状态期间通知他们。

2019 年，[Marcy Sutton 领导并发布了用户研究成果][marcy-sutton-led-and-published-findings-from-user-research]，帮助开发者构建无障碍的客户端路由体验。

[link]: ../api/components/Link
[navlink]: ../api/components/NavLink
[scripts]: ../api/components/Scripts
[wcag]: https://www.w3.org/WAI/standards-guidelines/wcag/
[marcy-sutton-led-and-published-findings-from-user-research]: https://www.gatsbyjs.com/blog/2019-07-11-user-testing-accessible-client-routing
