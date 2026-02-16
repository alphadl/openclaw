---
title: "Cloudflare AI Gateway"
summary: "Cloudflare AI Gateway 设置（认证 + 模型选择）"
read_when:
  - 你想在 OpenClaw 中使用 Cloudflare AI Gateway
  - 你需要 account ID、gateway ID 或 API key 环境变量
---

# Cloudflare AI Gateway

Cloudflare AI Gateway 位于提供商 API 之前，允许你添加分析、缓存和控制。对于 Anthropic，OpenClaw 通过你的 Gateway 端点使用 Anthropic Messages API。

- 提供商：`cloudflare-ai-gateway`
- Base URL：`https://gateway.ai.cloudflare.com/v1/<account_id>/<gateway_id>/anthropic`
- 默认模型：`cloudflare-ai-gateway/claude-sonnet-4-5`
- API key：`CLOUDFLARE_AI_GATEWAY_API_KEY`（通过 Gateway 请求时使用的提供商 API key）

对于 Anthropic 模型，使用你的 Anthropic API key。

## 快速开始

1. 设置提供商 API key 和 Gateway 详情：

```bash
openclaw onboard --auth-choice cloudflare-ai-gateway-api-key
```

2. 设置默认模型：

```json5
{
  agents: {
    defaults: {
      model: { primary: "cloudflare-ai-gateway/claude-sonnet-4-5" },
    },
  },
}
```

## 非交互式示例

```bash
openclaw onboard --non-interactive \
  --mode local \
  --auth-choice cloudflare-ai-gateway-api-key \
  --cloudflare-ai-gateway-account-id "your-account-id" \
  --cloudflare-ai-gateway-gateway-id "your-gateway-id" \
  --cloudflare-ai-gateway-api-key "$CLOUDFLARE_AI_GATEWAY_API_KEY"
```

## 需要认证的 Gateway

如果你在 Cloudflare 中启用了 Gateway 认证，添加 `cf-aig-authorization` 头（这是在你的提供商 API key 之外的）。

```json5
{
  models: {
    providers: {
      "cloudflare-ai-gateway": {
        headers: {
          "cf-aig-authorization": "Bearer <cloudflare-ai-gateway-token>",
        },
      },
    },
  },
}
```

## 环境说明

如果 Gateway 作为守护进程运行（launchd/systemd），确保 `CLOUDFLARE_AI_GATEWAY_API_KEY` 对该进程可用（例如放在 `~/.openclaw/.env` 中或通过 `env.shellEnv`）。
