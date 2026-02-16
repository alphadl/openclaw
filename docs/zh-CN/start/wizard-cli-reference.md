---
summary: "CLI 新手引导流程、认证/模型设置、输出和内部机制的完整参考"
read_when:
  - 需要 openclaw onboard 的详细行为说明
  - 调试引导结果或集成引导客户端
title: "CLI 新手引导参考"
sidebarTitle: "CLI 参考"
---

# CLI 新手引导参考

本页是 `openclaw onboard` 的完整参考。
简明指南请参见[新手引导向导 (CLI)](/start/wizard)。

## 向导的功能

本地模式（默认）引导你完成：

- 模型和认证设置（OpenAI Code 订阅 OAuth、Anthropic API key 或 setup token，以及 MiniMax、GLM、Moonshot 和 AI Gateway 选项）
- 工作区位置和引导文件
- Gateway 设置（端口、绑定、认证、Tailscale）
- 渠道和提供商（Telegram、WhatsApp、Discord、Google Chat、Mattermost 插件、Signal）
- 守护进程安装（LaunchAgent 或 systemd 用户单元）
- 健康检查
- 技能设置

远程模式配置本机连接到其他位置的 Gateway。
它不会在远程主机上安装或修改任何内容。

## 本地流程详情

<Steps>
  <Step title="现有配置检测">
    - 如果 `~/.openclaw/openclaw.json` 存在，选择保留、修改或重置。
    - 重新运行向导不会清除任何内容，除非你明确选择重置（或传入 `--reset`）。
    - 如果配置无效或包含遗留键，向导会停止并要求运行 `openclaw doctor`。
    - 重置使用 `trash` 并提供范围选择：
      - 仅配置
      - 配置 + 凭证 + 会话
      - 完全重置（同时移除工作区）
  </Step>
  <Step title="模型和认证">
    - 完整选项矩阵见[认证和模型选项](#认证和模型选项)。
  </Step>
  <Step title="工作区">
    - 默认 `~/.openclaw/workspace`（可配置）。
    - 创建首次运行引导仪式所需的工作区文件。
    - 工作区布局：[智能体工作区](/concepts/agent-workspace)。
  </Step>
  <Step title="Gateway">
    - 提示输入端口、绑定、认证模式和 Tailscale 暴露。
    - 推荐：即使在 loopback 上也启用 token 认证，以便本地 WS 客户端必须认证。
    - 仅在你完全信任每个本地进程时才禁用认证。
    - 非 loopback 绑定仍然需要认证。
  </Step>
  <Step title="渠道">
    - [WhatsApp](/channels/whatsapp)：可选 QR 登录
    - [Telegram](/channels/telegram)：机器人令牌
    - [Discord](/channels/discord)：机器人令牌
    - [Google Chat](/channels/googlechat)：服务账户 JSON + webhook audience
    - [Mattermost](/channels/mattermost) 插件：机器人令牌 + base URL
    - [Signal](/channels/signal)：可选 `signal-cli` 安装 + 账户配置
    - [BlueBubbles](/channels/bluebubbles)：推荐用于 iMessage；服务器 URL + 密码 + webhook
    - [iMessage](/channels/imessage)：旧版 `imsg` CLI 路径 + 数据库访问
    - DM 安全：默认为配对模式。首次 DM 会发送验证码；通过
      `openclaw pairing approve <channel> <code>` 审批，或使用白名单。
  </Step>
  <Step title="守护进程安装">
    - macOS：LaunchAgent
      - 需要已登录的用户会话；无头模式请使用自定义 LaunchDaemon（未内置）。
    - Linux 和 Windows（通过 WSL2）：systemd 用户单元
      - 向导尝试 `loginctl enable-linger <user>` 以便 Gateway 在登出后保持运行。
      - 可能提示 sudo（写入 `/var/lib/systemd/linger`）；先尝试无 sudo。
    - 运行时选择：Node（推荐；WhatsApp 和 Telegram 必需）。不推荐 Bun。
  </Step>
  <Step title="健康检查">
    - 启动 Gateway（如需要）并运行 `openclaw health`。
    - `openclaw status --deep` 将 Gateway 健康探测添加到状态输出。
  </Step>
  <Step title="技能">
    - 读取可用技能并检查需求。
    - 让你选择 Node 管理器：npm 或 pnpm（不推荐 bun）。
    - 安装可选依赖（部分在 macOS 上使用 Homebrew）。
  </Step>
  <Step title="完成">
    - 摘要和后续步骤，包括 iOS、Android 和 macOS 应用选项。
  </Step>
</Steps>

<Note>
如果未检测到 GUI，向导会打印 SSH 端口转发指令（用于 Control UI）而非打开浏览器。
如果 Control UI 资源缺失，向导会尝试构建；回退方案是 `pnpm ui:build`（自动安装 UI 依赖）。
</Note>

## 远程模式详情

远程模式配置本机连接到其他位置的 Gateway。

<Info>
远程模式不会在远程主机上安装或修改任何内容。
</Info>

你需要设置：

- 远程 Gateway URL（`ws://...`）
- Token（如果远程 Gateway 需要认证，推荐）

<Note>
- 如果 Gateway 仅绑定 loopback，使用 SSH 隧道或 tailnet。
- 发现提示：
  - macOS：Bonjour（`dns-sd`）
  - Linux：Avahi（`avahi-browse`）
</Note>

## 认证和模型选项

<AccordionGroup>
  <Accordion title="Anthropic API key（推荐）">
    使用 `ANTHROPIC_API_KEY`（如果存在）或提示输入密钥，然后为守护进程保存。
  </Accordion>
  <Accordion title="Anthropic OAuth（Claude Code CLI）">
    - macOS：检查 Keychain 项 "Claude Code-credentials"
    - Linux 和 Windows：复用 `~/.claude/.credentials.json`（如果存在）

    在 macOS 上，选择"始终允许"以便 launchd 启动不会阻塞。

  </Accordion>
  <Accordion title="Anthropic token（setup-token 粘贴）">
    在任何机器上运行 `claude setup-token`，然后粘贴 token。
    可以命名；留空使用默认值。
  </Accordion>
  <Accordion title="OpenAI Code 订阅（Codex CLI 复用）">
    如果 `~/.codex/auth.json` 存在，向导可以复用它。
  </Accordion>
  <Accordion title="OpenAI Code 订阅（OAuth）">
    浏览器流程；粘贴 `code#state`。

    当 model 未设置或为 `openai/*` 时，设置 `agents.defaults.model` 为 `openai-codex/gpt-5.3-codex`。

  </Accordion>
  <Accordion title="OpenAI API key">
    使用 `OPENAI_API_KEY`（如果存在）或提示输入密钥，然后保存到
    `~/.openclaw/.env` 以便 launchd 可以读取。

    当 model 未设置、为 `openai/*` 或 `openai-codex/*` 时，设置 `agents.defaults.model` 为 `openai/gpt-5.1-codex`。

  </Accordion>
  <Accordion title="xAI (Grok) API key">
    提示输入 `XAI_API_KEY` 并配置 xAI 为模型提供商。
  </Accordion>
  <Accordion title="自定义提供商">
    支持 OpenAI 兼容和 Anthropic 兼容的端点。

    非交互式标志：
    - `--auth-choice custom-api-key`
    - `--custom-base-url`
    - `--custom-model-id`
    - `--custom-api-key`（可选；回退到 `CUSTOM_API_KEY`）
    - `--custom-provider-id`（可选）
    - `--custom-compatibility <openai|anthropic>`（可选；默认 `openai`）

  </Accordion>
  <Accordion title="跳过">
    不配置认证。
  </Accordion>
</AccordionGroup>

模型行为：

- 从检测到的选项中选择默认模型，或手动输入提供商和模型。
- 向导运行模型检查，如果配置的模型未知或缺少认证会发出警告。

凭证和配置文件路径：

- OAuth 凭证：`~/.openclaw/credentials/oauth.json`
- 认证配置文件（API key + OAuth）：`~/.openclaw/agents/<agentId>/agent/auth-profiles.json`

<Note>
无头/服务器提示：先在有浏览器的机器上完成 OAuth，然后将
`~/.openclaw/credentials/oauth.json`（或 `$OPENCLAW_STATE_DIR/credentials/oauth.json`）
复制到 Gateway 主机。
</Note>

## 输出和内部机制

`~/.openclaw/openclaw.json` 中的典型字段：

- `agents.defaults.workspace`
- `agents.defaults.model` / `models.providers`（如果选择了 Minimax）
- `gateway.*`（mode、bind、auth、tailscale）
- `channels.telegram.botToken`、`channels.discord.token`、`channels.signal.*`、`channels.imessage.*`
- 渠道白名单（Slack、Discord、Matrix、Microsoft Teams）（在提示中选择加入时配置，名称尽可能解析为 ID）
- `skills.install.nodeManager`
- `wizard.lastRunAt`
- `wizard.lastRunVersion`
- `wizard.lastRunCommit`
- `wizard.lastRunCommand`
- `wizard.lastRunMode`

`openclaw agents add` 写入 `agents.list[]` 和可选的 `bindings`。

WhatsApp 凭证位于 `~/.openclaw/credentials/whatsapp/<accountId>/`。
会话存储在 `~/.openclaw/agents/<agentId>/sessions/`。

<Note>
部分渠道以插件形式提供。在引导过程中选择时，向导会提示安装插件（npm 或本地路径），然后再配置渠道。
</Note>

## 相关文档

- 新手引导中心：[新手引导向导 (CLI)](/start/wizard)
- 自动化和脚本：[CLI 自动化](/start/wizard-cli-automation)
- 命令参考：[`openclaw onboard`](/cli/onboard)
