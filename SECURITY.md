# 安全策略

## 支持的版本

以下版本目前正在接受安全更新支持：

| 版本  | 是否支持           |
| ----- | ------------------ |
| 7.x   | :white_check_mark: |
| 6.x   | :white_check_mark: |
| < 6.0 | :x:                |

## 报告漏洞

我们非常重视 React Router 中的安全漏洞。感谢您负责任地披露您的发现，我们将尽一切努力确认您的贡献。

要报告安全问题，请使用 GitHub Security Advisory 的[报告漏洞](https://github.com/remix-run/react-router/security/advisories/new)功能。

React Router 团队将发送回复，说明处理您报告的后续步骤。在对您的报告做出初步回复后，我们的团队将随时通知您修复和完整公告的进展，并可能要求提供额外的信息或指导。

通常，当我们通过 GitHub 收到新的安全通告时，完整的流程大致如下：

- 如果通告有效，我们将在开始调查时将其移至 `Draft`（草稿）状态
- 我们将通知常见的托管平台该漏洞信息，以便他们在漏洞修复/发布之前就能在其端做出预防性更改
  - 如果您是托管服务提供商，希望立即收到通知，请发送邮件至 [hello@remix.run](mailto:hello@remix.run)，我们会将您加入通知列表
- 我们将发布包含修复的 React Router 新版本
- 我们将使用新版本更新我们自己的网站
- 经过一段时间（可能长达一个月左右），我们将发布安全通告
  - 这为应用开发者提供了在我们公开通告详情之前将其应用更新到最新版本的时间

请将第三方模块中的安全漏洞报告给维护该模块的个人或团队。您也可以通过 [npm 联系表单](https://www.npmjs.com/support)选择"I'm reporting a security vulnerability"来报告漏洞。
