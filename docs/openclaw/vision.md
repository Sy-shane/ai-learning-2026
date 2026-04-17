# OpenClaw 愿景

> 翻译自 [openclaw/openclaw VISION.md](https://github.com/openclaw/openclaw/blob/main/VISION.md)，2026-04-17

## OpenClaw 是什么

OpenClaw 是一个**个人 AI 助手**，运行在你自己的设备上。它在你已经使用的渠道上回复你。它能在 macOS/iOS/Android 上说话和倾听，能渲染你控制的 Live Canvas。Gateway 只是控制平面——**产品就是助手本身**。

## 项目起源

OpenClaw 起源于一个个人实验：学习 AI 并构建真正有用的东西——一个能在真实计算机上执行真实任务的助手。

它经历了好几个名字和 shell：Warelay → Clawdbot → Moltbot → OpenClaw。

## 核心目标

**一个容易使用、支持广泛平台、尊重隐私和安全的个人助手。**

当前优先级：

1. **安全和安全默认值**
2. **Bug 修复和稳定性**
3. **设置可靠性和首次用户体验**

下一优先级：
- 支持所有主流模型提供商
- 改善主流消息渠道支持（并添加一些高需求的新渠道）
- 性能和测试基础设施
- 更好的计算机使用和 Agent harness 能力
- CLI 和 Web 前端的 ergonomics
- macOS、iOS、Android、Windows、Linux 上的伴侣应用

## 技术选型：为什么是 TypeScript

OpenClaw 主要是一个**编排系统**：prompt、工具、协议和集成。

选择 TypeScript 是为了保持可 hack 性。它：
- 被广泛使用
- 迭代速度快
- 易于阅读、修改和扩展

## 我们不会合并的内容（目前）

这是一份路线图护栏，不是物理定律：

- 当技能可以放在 ClawHub 时，不加入新的核心技能
- 全文档翻译套件（推迟；计划后续用 AI 生成翻译）
- 不适合模型提供商类别的商业服务集成
- 没有明确能力或安全差距的已有渠道的包装渠道
- 当 `mcporter` 已经提供集成路径时，在核心中构建一等 MCP 运行时
- Agent 层级框架（manager-of-managers / 嵌套 planner trees）作为默认架构
- 重复现有 Agent 和工具基础设施的重型编排层

**这个列表可以因为强烈的用户需求和强烈的技术理由而改变。**

## 安全模型

OpenClaw 连接到真实消息平台。将入站 DM 视为**不可信输入**。

安全策略：https://docs.openclaw.ai/gateway/security

我们的目标是在保持真正工作的强大功能和让危险路径显式化并由操作员控制之间取得平衡。

## 插件 & 记忆

OpenClaw 有广泛的插件 API。核心保持精简；可选能力应该作为插件发布。

首选插件路径：**npm 包分发 + 本地扩展加载**。

如果你构建了一个插件，在你自己的仓库中托管和维护它。向核心添加可选插件的门槛是故意很高的。

### 关于 Skills

我们仍然为基准用户体验提供一些捆绑技能。
新技能应该首先发布到 ClawHub（clawhub.ai），而不是默认添加到核心。
核心技能添加应该是罕见的，需要强烈的产品或安全理由。

### MCP 支持

OpenClaw 通过 `mcporter` 支持 MCP：https://github.com/steipete/mcporter

这保持了 MCP 集成的灵活性和解耦：
- 无需重启 Gateway 即可添加或更改 MCP 服务器
- 保持核心工具/上下文表面精简
- 减少 MCP 变动对核心稳定性和安全性的影响

## 为什么是"Terminal-first"设计

OpenClaw 目前是 terminal-first 设计。这样保持设置显式：用户提前看到文档、认证、权限和安全姿态。

长期来看，随着 hardening 成熟，我们想要更容易的 onboarding 流程。
我们不想要隐藏用户关键安全决策的便利包装器。
