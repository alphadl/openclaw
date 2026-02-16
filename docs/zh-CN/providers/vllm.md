---
summary: "使用 vLLM（OpenAI 兼容本地服务器）运行 OpenClaw"
read_when:
  - 你想将 OpenClaw 连接到本地 vLLM 服务器
  - 你需要 OpenAI 兼容的 /v1 端点配合自己的模型
title: "vLLM"
---

# vLLM

vLLM 可通过 **OpenAI 兼容** HTTP API 服务开源模型。OpenClaw 使用 `openai-completions` API 连接 vLLM。

当你设置 `VLLM_API_KEY`（如果服务器不要求认证，任意值即可）且未定义 `models.providers.vllm` 时，OpenClaw 还可以**自动发现** vLLM 上的可用模型。

## 快速开始

1. 启动 vLLM 的 OpenAI 兼容服务器。Base URL 应暴露 `/v1` 端点（如 `/v1/models`、`/v1/chat/completions`）。vLLM 通常运行在 `http://127.0.0.1:8000/v1`。

2. 设置密钥（如果服务器无认证，任意值即可）：

```bash
export VLLM_API_KEY="vllm-local"
```

3. 选择模型（替换为你的 vLLM 模型 ID）：

```json5
{
  agents: {
    defaults: {
      model: { primary: "vllm/your-model-id" },
    },
  },
}
```

## 模型发现（隐式提供商）

当设置了 `VLLM_API_KEY` 且**未**定义 `models.providers.vllm` 时，OpenClaw 会查询 `GET http://127.0.0.1:8000/v1/models` 并将返回的 ID 转换为模型条目。

## 显式配置（手动模型）

当 vLLM 运行在不同主机/端口，或你想固定 `contextWindow`/`maxTokens` 值时使用：

```json5
{
  models: {
    providers: {
      vllm: {
        baseUrl: "http://127.0.0.1:8000/v1",
        apiKey: "${VLLM_API_KEY}",
        api: "openai-completions",
        models: [
          {
            id: "your-model-id",
            name: "Local vLLM Model",
            reasoning: false,
            input: ["text"],
            cost: { input: 0, output: 0, cacheRead: 0, cacheWrite: 0 },
            contextWindow: 128000,
            maxTokens: 8192,
          },
        ],
      },
    },
  },
}
```

## 故障排除

- 检查服务器可达性：`curl http://127.0.0.1:8000/v1/models`
- 如果请求因认证错误失败，设置与服务器配置匹配的 `VLLM_API_KEY`，或在 `models.providers.vllm` 下显式配置提供商。
