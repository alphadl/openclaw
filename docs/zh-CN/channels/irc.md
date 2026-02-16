---
title: IRC
description: 将 OpenClaw 连接到 IRC 频道和私信。
---

使用 IRC 将 OpenClaw 接入经典频道（`#room`）和私信。
IRC 以扩展插件形式提供，但在主配置 `channels.irc` 下配置。

## 快速开始

1. 在 `~/.openclaw/openclaw.json` 中启用 IRC 配置。
2. 至少设置：

```json
{
  "channels": {
    "irc": {
      "enabled": true,
      "host": "irc.libera.chat",
      "port": 6697,
      "tls": true,
      "nick": "openclaw-bot",
      "channels": ["#openclaw"]
    }
  }
}
```

3. 启动/重启 Gateway：

```bash
openclaw gateway run
```

## 安全默认值

- `channels.irc.dmPolicy` 默认为 `"pairing"`。
- `channels.irc.groupPolicy` 默认为 `"allowlist"`。
- 使用 `groupPolicy="allowlist"` 时，设置 `channels.irc.groups` 定义允许的频道。
- 使用 TLS（`channels.irc.tls=true`），除非你有意接受明文传输。

## 访问控制

IRC 频道有两个独立的"门"：

1. **频道访问**（`groupPolicy` + `groups`）：机器人是否接受来自某个频道的消息。
2. **发送者访问**（`groupAllowFrom` / 每频道 `groups["#channel"].allowFrom`）：频道内谁可以触发机器人。

### 常见问题：`allowFrom` 用于私信，不是频道

如果日志中出现 `irc: drop group sender alice!ident@host (policy=allowlist)`，表示发送者不被允许发送**群组/频道**消息。修复方法：

- 设置 `channels.irc.groupAllowFrom`（全局适用所有频道），或
- 设置每频道发送者白名单：`channels.irc.groups["#channel"].allowFrom`

## 回复触发（提及）

即使频道和发送者都被允许，OpenClaw 默认在群组上下文中启用**提及门控**。

要让机器人在 IRC 频道中**无需提及即可回复**，为该频道禁用提及门控：

```json5
{
  channels: {
    irc: {
      groupPolicy: "allowlist",
      groups: {
        "#tuirc-dev": {
          requireMention: false,
          allowFrom: ["*"],
        },
      },
    },
  },
}
```

## NickServ

连接后向 NickServ 认证：

```json
{
  "channels": {
    "irc": {
      "nickserv": {
        "enabled": true,
        "service": "NickServ",
        "password": "your-nickserv-password"
      }
    }
  }
}
```

## 环境变量

默认账户支持：

- `IRC_HOST`
- `IRC_PORT`
- `IRC_TLS`
- `IRC_NICK`
- `IRC_USERNAME`
- `IRC_REALNAME`
- `IRC_PASSWORD`
- `IRC_CHANNELS`（逗号分隔）
- `IRC_NICKSERV_PASSWORD`

## 故障排除

- 如果机器人连接但从不在频道中回复，验证 `channels.irc.groups` 以及提及门控是否丢弃了消息（`missing-mention`）。如果想让它无需 ping 即可回复，为该频道设置 `requireMention:false`。
- 如果登录失败，验证昵称可用性和服务器密码。
- 如果 TLS 在自定义网络上失败，验证主机/端口和证书设置。
