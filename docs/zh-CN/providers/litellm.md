---
summary: "通过 LiteLLM Proxy 运行 OpenClaw，实现统一模型访问和费用追踪"
read_when:
  - 你想通过 LiteLLM proxy 路由 OpenClaw
  - 你需要费用追踪、日志或通过 LiteLLM 的模型路由
---

# LiteLLM

[LiteLLM](https://litellm.ai) 是一个开源 LLM 网关，为 100+ 模型提供商提供统一 API。通过 LiteLLM 路由 OpenClaw 可获得集中化的费用追踪、日志记录和灵活切换后端的能力。

## 为什么将 LiteLLM 与 OpenClaw 配合使用？

- **费用追踪** — 精确查看 OpenClaw 在所有模型上的花费
- **模型路由** — 在 Claude、GPT-4、Gemini、Bedrock 之间切换无需更改配置
- **虚拟密钥** — 为 OpenClaw 创建带消费限额的密钥
- **日志** — 完整的请求/响应日志用于调试
- **回退** — 主提供商宕机时自动故障转移

## 快速开始

### 通过引导

```bash
openclaw onboard --auth-choice litellm-api-key
```

### 手动设置

1. 启动 LiteLLM Proxy：

```bash
pip install 'litellm[proxy]'
litellm --model claude-opus-4-6
```

2. 将 OpenClaw 指向 LiteLLM：

```bash
export LITELLM_API_KEY="your-litellm-key"
openclaw
```

完成。OpenClaw 现在通过 LiteLLM 路由。

## 配置

### 环境变量

```bash
export LITELLM_API_KEY="sk-litellm-key"
```

### 配置文件

```json5
{
  models: {
    providers: {
      litellm: {
        baseUrl: "http://localhost:4000",
        apiKey: "${LITELLM_API_KEY}",
        api: "openai-completions",
        models: [
          {
            id: "claude-opus-4-6",
            name: "Claude Opus 4.6",
            reasoning: true,
            input: ["text", "image"],
            contextWindow: 200000,
            maxTokens: 64000,
          },
        ],
      },
    },
  },
  agents: {
    defaults: {
      model: { primary: "litellm/claude-opus-4-6" },
    },
  },
}
```

## 虚拟密钥

为 OpenClaw 创建带消费限额的专用密钥：

```bash
curl -X POST "http://localhost:4000/key/generate" \
  -H "Authorization: Bearer $LITELLM_MASTER_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "key_alias": "openclaw",
    "max_budget": 50.00,
    "budget_duration": "monthly"
  }'
```

使用生成的密钥作为 `LITELLM_API_KEY`。

## 注意

- LiteLLM 默认运行在 `http://localhost:4000`
- OpenClaw 通过 OpenAI 兼容的 `/v1/chat/completions` 端点连接
- 所有 OpenClaw 功能都可以通过 LiteLLM 使用

## 另请参阅

- [LiteLLM 文档](https://docs.litellm.ai)
- [模型提供商](/concepts/model-providers)
