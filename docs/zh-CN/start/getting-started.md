---
summary: "安装 OpenClaw 并在几分钟内启动第一次聊天。"
read_when:
  - 从零开始首次设置
  - 你想要最快到达可用聊天的路径
title: "入门指南"
---

# 入门指南

目标：从零到第一个可用聊天，最少配置即可。

<Info>
最快聊天：打开 Control UI（无需渠道设置）。运行 `openclaw dashboard`
并在浏览器中聊天，或在
<Tooltip headline="Gateway 主机" tip="运行 OpenClaw Gateway 服务的机器。">Gateway 主机</Tooltip>
上打开 `http://127.0.0.1:18789/`。
文档：[Dashboard](/web/dashboard) 和 [Control UI](/web/control-ui)。
</Info>

## 前置条件

- Node 22 或更新版本

<Tip>
如果不确定，使用 `node --version` 检查 Node 版本。
</Tip>

## 快速设置 (CLI)

<Steps>
  <Step title="安装 OpenClaw（推荐）">
    <Tabs>
      <Tab title="macOS/Linux">
        ```bash
        curl -fsSL https://openclaw.ai/install.sh | bash
        ```
      </Tab>
      <Tab title="Windows (PowerShell)">
        ```powershell
        iwr -useb https://openclaw.ai/install.ps1 | iex
        ```
      </Tab>
    </Tabs>

    <Note>
    其他安装方式和要求：[安装](/install)。
    </Note>

  </Step>
  <Step title="运行新手引导向导">
    ```bash
    openclaw onboard --install-daemon
    ```

    向导会配置认证、Gateway 设置和可选渠道。
    详情参见[新手引导向导](/start/wizard)。

  </Step>
  <Step title="检查 Gateway">
    如果你安装了服务，它应该已经在运行：

    ```bash
    openclaw gateway status
    ```

  </Step>
  <Step title="打开 Control UI">
    ```bash
    openclaw dashboard
    ```
  </Step>
</Steps>

<Check>
如果 Control UI 加载成功，你的 Gateway 已经就绪。
</Check>

## 可选检查与扩展

<AccordionGroup>
  <Accordion title="前台运行 Gateway">
    适合快速测试或排查问题。

    ```bash
    openclaw gateway --port 18789
    ```

  </Accordion>
  <Accordion title="发送测试消息">
    需要已配置的渠道。

    ```bash
    openclaw message send --target +15555550123 --message "Hello from OpenClaw"
    ```

  </Accordion>
</AccordionGroup>

## 常用环境变量

如果你以服务账户运行 OpenClaw 或需要自定义配置/状态路径：

- `OPENCLAW_HOME` 设置内部路径解析使用的主目录。
- `OPENCLAW_STATE_DIR` 覆盖状态目录。
- `OPENCLAW_CONFIG_PATH` 覆盖配置文件路径。

完整环境变量参考：[环境变量](/help/environment)。

## 深入了解

<Columns>
  <Card title="新手引导向导（详情）" href="/start/wizard">
    完整的 CLI 向导参考和高级选项。
  </Card>
  <Card title="macOS 应用引导" href="/start/onboarding">
    macOS 应用的首次运行流程。
  </Card>
</Columns>

## 你将获得

- 一个运行中的 Gateway
- 已配置的认证
- Control UI 访问或已连接的渠道

## 下一步

- DM 安全和审批：[配对](/channels/pairing)
- 连接更多渠道：[渠道](/channels)
- 高级工作流和源码构建：[设置](/start/setup)
