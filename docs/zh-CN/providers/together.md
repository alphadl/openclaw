---
summary: "Together AI 设置（认证 + 模型选择）"
read_when:
  - 你想在 OpenClaw 中使用 Together AI
  - 你需要 API key 环境变量或 CLI 认证选项
---

# Together AI

[Together AI](https://together.ai) 通过统一 API 提供对领先开源模型的访问，包括 Llama、DeepSeek、Kimi 等。

- 提供商：`together`
- 认证：`TOGETHER_API_KEY`
- API：OpenAI 兼容

## 快速开始

1. 设置 API key（推荐：为 Gateway 存储）：

```bash
openclaw onboard --auth-choice together-api-key
```

2. 设置默认模型：

```json5
{
  agents: {
    defaults: {
      model: { primary: "together/moonshotai/Kimi-K2.5" },
    },
  },
}
```

## 非交互式示例

```bash
openclaw onboard --non-interactive \
  --mode local \
  --auth-choice together-api-key \
  --together-api-key "$TOGETHER_API_KEY"
```

## 环境说明

如果 Gateway 作为守护进程运行（launchd/systemd），确保 `TOGETHER_API_KEY` 对该进程可用（例如放在 `~/.openclaw/.env` 中或通过 `env.shellEnv`）。

## 可用模型

- **GLM 4.7 Fp8** — 默认模型，200K 上下文窗口
- **Llama 3.3 70B Instruct Turbo** — 快速高效的指令遵循
- **Llama 4 Scout** — 支持图像理解的视觉模型
- **DeepSeek V3.1** — 强大的编码和推理模型
- **DeepSeek R1** — 高级推理模型
- **Kimi K2 Instruct** — 高性能模型，262K 上下文窗口

所有模型都支持标准 chat completions，兼容 OpenAI API。
