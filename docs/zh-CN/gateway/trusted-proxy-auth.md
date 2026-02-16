---
summary: "将 Gateway 认证委托给可信反向代理（Pomerium、Caddy、nginx + OAuth）"
read_when:
  - 在身份感知代理后运行 OpenClaw
  - 设置 Pomerium、Caddy 或 nginx + OAuth 作为 OpenClaw 前端
  - 修复反向代理设置中的 WebSocket 1008 unauthorized 错误
---

# 可信代理认证

> ⚠️ **安全敏感功能。** 此模式将认证完全委托给你的反向代理。配置错误可能导致 Gateway 暴露给未授权访问。启用前请仔细阅读本页。

## 何时使用

当以下情况时使用 `trusted-proxy` 认证模式：

- 你在**身份感知代理**后运行 OpenClaw（Pomerium、Caddy + OAuth、nginx + oauth2-proxy、Traefik + forward auth）
- 你的代理处理所有认证并通过请求头传递用户身份
- 你在 Kubernetes 或容器环境中，代理是到达 Gateway 的唯一路径
- 你遇到 WebSocket `1008 unauthorized` 错误（因为浏览器无法在 WS payload 中传递 token）

## 何时不使用

- 如果你的代理不认证用户（仅做 TLS 终止或负载均衡）
- 如果有任何绕过代理到达 Gateway 的路径（防火墙漏洞、内网访问）
- 如果你不确定代理是否正确剥离/覆盖转发头
- 如果你只需要个人单用户访问（考虑 Tailscale Serve + loopback 作为更简单的方案）

## 工作原理

1. 反向代理认证用户（OAuth、OIDC、SAML 等）
2. 代理添加包含认证用户身份的请求头（如 `x-forwarded-user: nick@example.com`）
3. OpenClaw 检查请求是否来自**可信代理 IP**（在 `gateway.trustedProxies` 中配置）
4. OpenClaw 从配置的请求头中提取用户身份
5. 如果一切正常，请求被授权

## 配置

```json5
{
  gateway: {
    bind: "lan",
    trustedProxies: ["10.0.0.1", "172.17.0.1"],
    auth: {
      mode: "trusted-proxy",
      trustedProxy: {
        userHeader: "x-forwarded-user",
        requiredHeaders: ["x-forwarded-proto", "x-forwarded-host"],
        allowUsers: ["nick@example.com", "admin@company.org"],
      },
    },
  },
}
```

### 配置参考

| 字段                                        | 必需 | 描述                                              |
| ------------------------------------------- | ---- | ------------------------------------------------- |
| `gateway.trustedProxies`                    | 是   | 要信任的代理 IP 地址数组。来自其他 IP 的请求被拒绝。 |
| `gateway.auth.mode`                         | 是   | 必须为 `"trusted-proxy"`                           |
| `gateway.auth.trustedProxy.userHeader`      | 是   | 包含认证用户身份的请求头名称                         |
| `gateway.auth.trustedProxy.requiredHeaders` | 否   | 请求被信任时必须存在的额外请求头                     |
| `gateway.auth.trustedProxy.allowUsers`      | 否   | 用户身份白名单。为空表示允许所有认证用户。             |

## 代理设置示例

### Pomerium

```json5
{
  gateway: {
    bind: "lan",
    trustedProxies: ["10.0.0.1"],
    auth: {
      mode: "trusted-proxy",
      trustedProxy: {
        userHeader: "x-pomerium-claim-email",
        requiredHeaders: ["x-pomerium-jwt-assertion"],
      },
    },
  },
}
```

### Caddy + OAuth

```json5
{
  gateway: {
    bind: "lan",
    trustedProxies: ["127.0.0.1"],
    auth: {
      mode: "trusted-proxy",
      trustedProxy: {
        userHeader: "x-forwarded-user",
      },
    },
  },
}
```

### nginx + oauth2-proxy

```json5
{
  gateway: {
    bind: "lan",
    trustedProxies: ["10.0.0.1"],
    auth: {
      mode: "trusted-proxy",
      trustedProxy: {
        userHeader: "x-auth-request-email",
      },
    },
  },
}
```

## 安全检查清单

启用 trusted-proxy 认证前，请验证：

- [ ] **代理是唯一路径**：Gateway 端口对除代理外的一切都被防火墙阻止
- [ ] **trustedProxies 最小化**：仅你实际的代理 IP，不要使用整个子网
- [ ] **代理剥离请求头**：你的代理覆写（而非追加）来自客户端的 `x-forwarded-*` 头
- [ ] **TLS 终止**：你的代理处理 TLS；用户通过 HTTPS 连接
- [ ] **设置了 allowUsers**（推荐）：限制为已知用户而非允许所有认证用户

## 故障排除

### "trusted_proxy_untrusted_source"

请求不是来自 `gateway.trustedProxies` 中的 IP。检查代理 IP 是否正确（Docker 容器 IP 可能会变化）。

### "trusted_proxy_user_missing"

用户请求头为空或缺失。检查代理是否配置为传递身份头。

### "trusted_proxy_user_not_allowed"

用户已认证但不在 `allowUsers` 中。添加该用户或移除白名单。

### WebSocket 仍然失败

确保你的代理支持 WebSocket 升级，并在 WebSocket 升级请求（不仅仅是 HTTP）上也传递身份头。

## 相关

- [安全](/gateway/security) — 完整安全指南
- [配置](/gateway/configuration) — 配置参考
- [远程访问](/gateway/remote) — 其他远程访问方式
- [Tailscale](/gateway/tailscale) — tailnet 专用访问的更简单替代方案
