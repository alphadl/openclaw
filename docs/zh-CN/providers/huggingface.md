---
summary: "Hugging Face Inference 设置（认证 + 模型选择）"
read_when:
  - 你想在 OpenClaw 中使用 Hugging Face Inference
  - 你需要 HF token 环境变量或 CLI 认证选项
title: "Hugging Face (Inference)"
---

# Hugging Face (Inference)

[Hugging Face Inference Providers](https://huggingface.co/docs/inference-providers) 通过单个路由 API 提供 OpenAI 兼容的 chat completions。一个 token 即可访问众多模型（DeepSeek、Llama 等）。

- 提供商：`huggingface`
- 认证：`HUGGINGFACE_HUB_TOKEN` 或 `HF_TOKEN`（需要 **Make calls to Inference Providers** 权限的细粒度 token）
- API：OpenAI 兼容（`https://router.huggingface.co/v1`）
- 计费：单个 HF token；[定价](https://huggingface.co/docs/inference-providers/pricing) 遵循提供商费率，含免费层。

## 快速开始

1. 在 [Hugging Face → Settings → Tokens](https://huggingface.co/settings/tokens/new?ownUserPermissions=inference.serverless.write&tokenType=fineGrained) 创建细粒度 token，勾选 **Make calls to Inference Providers** 权限。
2. 运行引导并在提供商下拉菜单中选择 **Hugging Face**：

```bash
openclaw onboard --auth-choice huggingface-api-key
```

3. 在 **Default Hugging Face model** 下拉菜单中选择模型。
4. 也可以稍后在配置中设置或更改默认模型：

```json5
{
  agents: {
    defaults: {
      model: { primary: "huggingface/deepseek-ai/DeepSeek-R1" },
    },
  },
}
```

## 非交互式示例

```bash
openclaw onboard --non-interactive \
  --mode local \
  --auth-choice huggingface-api-key \
  --huggingface-api-key "$HF_TOKEN"
```

## 模型 ID 和配置示例

模型引用使用 `huggingface/<org>/<model>` 格式（Hub 风格 ID）。

| 模型                    | 引用（前缀 `huggingface/`）         |
| ---------------------- | ----------------------------------- |
| DeepSeek R1            | `deepseek-ai/DeepSeek-R1`           |
| DeepSeek V3.2          | `deepseek-ai/DeepSeek-V3.2`         |
| Qwen3 8B               | `Qwen/Qwen3-8B`                     |
| Llama 3.3 70B Instruct | `meta-llama/Llama-3.3-70B-Instruct` |
| GPT-OSS 120B           | `openai/gpt-oss-120b`               |

可以在模型 ID 后追加 `:fastest`、`:cheapest` 或 `:provider`（如 `:together`、`:sambanova`）。
