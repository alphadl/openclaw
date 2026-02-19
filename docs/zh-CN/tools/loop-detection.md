---
title: "工具循环检测"
description: "配置可选的防护机制，防止重复或停滞的工具调用循环"
read_when:
  - 用户反馈 Agent 陷入重复工具调用
  - 需要调整重复调用保护策略
  - 正在编辑 Agent 工具/运行时策略
---

# 工具循环检测

OpenClaw 可以防止 Agent 陷入重复的工具调用模式。
该防护机制**默认关闭**。

仅在需要时启用，因为过严的设置可能会阻止合法的重复调用。

## 为什么需要这个功能

- 检测无进展的重复调用序列。
- 检测高频无结果循环（相同工具、相同输入、反复报错）。
- 检测已知轮询工具的特定重复调用模式。

## 配置项

全局默认值：

```json5
{
  tools: {
    loopDetection: {
      enabled: false,
      historySize: 20,
      detectorCooldownMs: 12000,
      repeatThreshold: 3,
      criticalThreshold: 6,
      detectors: {
        repeatedFailure: true,
        knownPollLoop: true,
        repeatingNoProgress: true,
      },
    },
  },
}
```

按 Agent 单独覆盖（可选）：

```json5
{
  agents: {
    list: [
      {
        id: "safe-runner",
        tools: {
          loopDetection: {
            enabled: true,
            repeatThreshold: 2,
            criticalThreshold: 5,
          },
        },
      },
    ],
  },
}
```

### 字段说明

- `enabled`：总开关。设为 `false` 则不执行任何循环检测。
- `historySize`：用于分析的最近工具调用记录数。
- `detectorCooldownMs`：无进展检测器使用的时间窗口。
- `repeatThreshold`：触发警告/阻止前所需的最小重复次数。
- `criticalThreshold`：更严格的阈值，可触发更强的处理。
- `detectors.repeatedFailure`：检测同一调用路径上的重复失败尝试。
- `detectors.knownPollLoop`：检测已知的轮询类循环。
- `detectors.repeatingNoProgress`：检测无状态变化的高频重复调用。

## 推荐配置

- 先以 `enabled: true` 启用，使用默认值。
- 如果出现误报：
  - 提高 `repeatThreshold` 和/或 `criticalThreshold`
  - 仅禁用引起问题的检测器
  - 减小 `historySize` 以降低历史上下文的严格程度

## 日志与预期行为

当检测到循环时，OpenClaw 会报告循环事件，并根据严重程度阻止或抑制下一个工具调用周期。
这可以保护用户免受失控的 token 消耗和死锁，同时保留正常的工具访问能力。

- 优先发出警告和临时抑制。
- 仅在积累了足够的重复证据后才升级处理。

## 注意事项

- `tools.loopDetection` 会与 Agent 级别的覆盖配置合并。
- Agent 级配置会完全覆盖或扩展全局值。
- 如果没有配置，防护机制保持关闭。
