# Claw Code 生态

> ClawHIP / OmX / OmO 生态详解，2026-04-17

## 生态全景

```
         Human (Discord / 手机)
                │
                ▼
         ┌─────────────┐
         │  ClawHIP    │  ← 事件 & 通知路由层
         │ (事件中枢)   │
         └──────┬──────┘
                │
    ┌───────────┼───────────┐
    ▼           ▼           ▼
 ┌──────┐  ┌────────┐  ┌────────┐
 │ OmX  │  │ OmO    │  │ Claw   │  ← 编码 Agent
 │工作流│  │多Agent │  │ Code   │
 └──────┘  └────────┘  └────────┘
```

## ClawHIP — 事件路由器

**定位**：将监控和通知推送到 Agent 上下文窗口之外。

### 它监控什么

- **Git**：commits、branches、PRs、releases
- **终端**：tmux sessions、shell 状态
- **平台**：GitHub Issues、PRs、discussions
- **Agent 生命周期**：spawn、running、finished、failed
- **消息投递**：Discord、邮件等通知

### 为什么这很重要

传统 Agent 的问题是：Agent 需要不断检查"任务完成了吗？""有没有新通知？"——这些轮询操作占用了宝贵的上下文窗口。

ClawHIP 的解决方案：**事件驱动推送**。Agent 订阅它关心的事件，事件发生时 ClawHIP 直接推送通知给订阅者，Agent 不需要主动轮询。

### 核心设计

```
Agent 订阅: [commit:*:main, pr:opened, agent:finished:*]
              ↓
ClawHIP 监控所有这些源
              ↓
事件匹配 → 推送通知到 Agent
```

## OmX — 工作流层

**定位**：将人类的一句话指令转化为结构化执行协议。

### 工作流关键词

OmX 解析的自然语言 Planning 关键词：

| 关键词 | 含义 |
|---|---|
| `PLAN` | 生成执行计划但不执行 |
| `EXECUTE` | 立即开始执行 |
| `REVIEW` | 等待 Reviewer Agent 确认 |
| `PARALLEL` | 多任务并行执行 |
| `RETRY` | 失败后重试指定次数 |
| `VERIFY` | 执行后验证结果正确性 |

### 执行模式

- **Standard**：顺序执行，REPL 交互
- **Scripted**：从文件加载任务列表，自动化执行
- **Daemon**：持续运行，监听 Discord 频道新消息
- **Test**：在隔离环境中运行测试套件

## OmO — 多 Agent 协调

**定位**：当多个 Agent 需要协作时（比如 Architect + Executor + Reviewer），OmO 提供协调结构。

### Agent 角色

| 角色 | 职责 |
|---|---|
| **Architect** | 系统设计、架构决策、技术选型 |
| **Executor** | 写代码、实现功能、执行任务 |
| **Reviewer** | Code Review、验证正确性、检查质量 |
| **Coordinator** | 任务分配、进度跟踪、冲突仲裁 |

### 分歧解决流程

```
Architect 说 A 方案
Executor 说 B 方案
         ↓
OmO 进入仲裁流程
         ↓
1. 各自陈述理由
2. 评估约束条件（时间/质量/复杂度）
3. Coordinator 做最终决定
         ↓
决定：选 A 方案，Executor 执行
```

## 周边工具生态

### clawhip
- GitHub: https://github.com/Yeachan-Heo/clawhip
- 事件路由核心实现

### oh-my-codex
- GitHub: https://github.com/Yeachan-Heo/oh-my-codex
- OmX 工作流引擎

### oh-my-openagent
- GitHub: https://github.com/code-yeongyu/oh-my-openagent
- 多 Agent 协调框架

### oh-my-claudecode
- GitHub: https://github.com/Yeachan-Heo/oh-my-claudecode
- Claude Code 增强工具集

### oh-my-codex
- GitHub: https://github.com/Yeachan-Heo/oh-my-codex
- Codex 增强工具集

## Claw Code 自身统计

- **Stars**: 185,500+
- **Forks**: 108,550+
- **仓库诞生**: 2026-03-31
- **10 万 stars 达成速度**: 史上最快
- **语言**: Rust
- **Discord**: https://discord.gg/5TUQKqFWd

## 给我们的教训

1. **协调层比执行层更有价值**：Claw Code 的核心不是 Rust 写得多好，而是那个协调系统
2. **通知必须外置**：不要让 Agent 轮询，把通知推给 Agent
3. **多 Agent 需要结构**：没有协调结构，多 Agent 会变成多 Agent 吵架
4. **Discord as UX 验证**：聊天接口作为人机交互验证了真实需求
