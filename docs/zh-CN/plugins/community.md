---
summary: "社区插件：质量标准、托管要求和 PR 提交流程"
read_when:
  - 你想发布第三方 OpenClaw 插件
  - 你想提议将插件收录到文档列表
title: "社区插件"
---

# 社区插件

本页面收录高质量的 **社区维护插件**。

当社区插件达到质量标准时，我们接受将其添加到此页面的 PR。

## 收录要求

- 插件包已发布到 npmjs（可通过 `openclaw plugins install <npm-spec>` 安装）。
- 源代码托管在 GitHub（公开仓库）。
- 仓库包含安装/使用文档和 issue 追踪。
- 插件有明确的维护信号（活跃的维护者、近期有更新、或及时响应 issue）。

## 如何提交

提交一个 PR，将你的插件添加到本页面，需包含：

- 插件名称
- npm 包名
- GitHub 仓库 URL
- 一句话描述
- 安装命令

## 审核标准

我们倾向于收录实用、文档完善且运行安全的插件。
低质量的封装、归属不清或缺乏维护的包可能会被拒绝。

## 条目格式

添加条目时请使用以下格式：

- **插件名称** — 简短描述
  npm: `@scope/package`
  repo: `https://github.com/org/repo`
  install: `openclaw plugins install @scope/package`
