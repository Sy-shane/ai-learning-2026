# OpenClaw 架构

> 基于 OpenClaw 源码结构和文档分析，2026-04-17

## 核心架构：Gateway 模式

OpenClaw 的核心是 **Gateway**——一个单一控制平面，管理 Sessions、Channels、Tools 和 Events。

```
┌─────────────────────────────────────────────┐
│              OpenClaw Gateway               │
│  ┌─────────┐  ┌──────────┐  ┌───────────┐ │
│  │ Sessions│  │ Channels │  │   Tools   │ │
│  │ Manager │  │  Router  │  │  Registry │ │
│  └─────────┘  └──────────┘  └───────────┘ │
│  ┌─────────┐  ┌──────────┐  ┌───────────┐ │
│  │  Skills │  │   Cron   │  │  Memory   │ │
│  │ Registry│  │ Scheduler│  │  Plugin   │ │
│  └─────────┘  └──────────┘  └───────────┘ │
└──────────────────┬──────────────────────────┘
                   │
     ┌─────────────┼─────────────┐
     ▼             ▼             ▼
  Feishu        Telegram      Discord
   ...            ...           ...
```

## Session 模型

Session 是 OpenClaw 的基本工作单元。每个 Session 有：
- **独立上下文窗口**：对话历史、记忆、状态
- **工具权限边界**：可以配置允许/禁止的工具
- **模型配置**：可以使用不同模型
- **Sandbox 模式**：非 main session 可选在 Docker 容器中运行

### Session 类型

| 类型 | 说明 |
|---|---|
| `main` | 主会话，完整工具权限，在 host 上运行 |
| `subagent` | 隔离子会话，用于并行任务 |
| `acp` | Agent Coding Protocol，用于 Zed/编辑器集成 |

## Channel 系统

Channel 是 OpenClaw 连接真实世界的接口。支持 20+ 渠道：

**即时通讯**：WhatsApp, Telegram, Signal, iMessage, BlueBubbles, IRC, Microsoft Teams, Matrix, Feishu, LINE, Mattermost, Nextcloud Talk, Nostr, Synology Chat, Zalo, WeChat, QQ, WebChat

**社交**：Discord, Slack, Google Chat

**平台**：macOS (menu bar app), iOS/Android (companion apps)

**其他**：Signal Arena, Twitch

### DM 安全策略

OpenClaw 的 DM 安全默认是 **pairing 模式**：
- 未知发送者收到配对码，不处理消息
- 操作员用 `openclaw pairing approve <channel> <code>` 审批
- 想要 open DM 需要显式设置 `dmPolicy="open"`

## 工具系统

OpenClaw 的工具分为几类：

### 内置工具
- `exec` — 在 host 上执行 shell 命令
- `read/write/edit` — 文件系统操作
- `browser` — 浏览器自动化
- `canvas` — macOS Canvas 控制
- `sessions_*` — Session 管理
- `cron` — 定时任务调度

### Skill 工具
通过 Skills 系统扩展。Skills 是带有 `SKILL.md` 的独立功能包。

### MCP 工具
通过 `mcporter` 桥接 MCP 服务器，支持 MCP 生态。

## 多 Agent 路由

OpenClaw 支持将入站渠道/账户/对等体路由到隔离 Agent（工作区 + per-agent sessions）。

```json
{
  "agents": {
    "defaults": {
      "sandbox": { "mode": "non-main" }
    }
  }
}
```

## Memory 系统

Memory 是一个特殊插件槽，每次只能有一个 memory 插件活跃。

内置选项：
- `MEMORY.md` — 文件基础的长期记忆
- `memory/*.md` — 每日笔记
- 其他插件化 memory 后端

## 关键设计决策

### 1. 本地执行优先
工具默认在 host 上运行，main session 有完全访问权。这是有意的安全权衡。

### 2. Gateway 作为控制平面
Gateway 负责协调，不直接运行工具（工具在 host 上运行）。这使得 Gateway 可以是轻量级服务。

### 3. 伴侣应用作为节点
iOS/Android 伴侣应用作为"节点"连接到 Gateway WebSocket，而不是在设备上运行完整 Gateway。

### 4. Skills 优先于核心功能
新功能首选作为 Skills 发布到 ClawHub，而不是添加到核心。这保持了核心精简。

## 安装要求

- **Node 24**（推荐）或 Node 22.16+
- **Runtime**：macOS / Linux / Windows (WSL2)
- **Gateway**：通过 `openclaw onboard --install-daemon` 安装为用户服务
