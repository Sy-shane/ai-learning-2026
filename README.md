# AI Learning Knowledge Base 2026

持续更新的 AI 学习仓库，聚焦 Agent 设计理念、2026 前沿方向与工具生态。

---

## 文档地图

```
ai-learning-2026/
|-- CLAUDE.md                        <- AI Agent 上下文指南（必读）
|-- docs/
|   |-- codex/                      <- OpenAI Codex（核心）
|   |   |-- memory-design.md         <- 两阶段记忆管道（最重要）
|   |-- claude-code/                 <- Claude Code 参考（Anthropic 官方）
|   |   |-- memory-design.md         <- 多层分散式记忆架构
|   |-- claw-code/                   <- Claw Code 完整解析
|   |   |-- philosophy.md             <- 核心理念：人机协作哲学
|   |   |-- architecture.md          <- Rust 架构、状态机、工具链
|   |   |-- ecosystem.md             <- ClawHIP / OmX / OmO 生态
|   |   |-- parity-status.md        <- 与 Claude Code 的 Parity 追踪
|   |-- openclaw/                    <- OpenClaw 设计与实现
|   |   |-- vision.md                <- 项目愿景与定位
|   |   |-- architecture.md          <- Gateway / Session / Channel 架构
|   |   |-- skills-system.md         <- Skill 系统设计
|   |   |-- security-model.md        <- 安全模型与 DM 策略
|   |-- 2026-ai-trends/             <- 2026 AI 学习方向
|       |-- agent-frameworks.md      <- Agent Framework 演进
|       |-- multi-agent-systems.md   <- 多智能体协作系统
|       |-- local-llm.md            <- 本地 LLM 与端侧推理
|       |-- reasoning-models.md      <- 推理模型前沿
|       |-- tool-use-evolution.md    <- 工具调用能力演进
|-- resources/
    |-- links.md                     <- 关键项目与参考链接
```

---

## 核心主题

### OpenAI Codex 记忆系统（第一优先）

OpenAI Codex（openai/codex，Rust 实现，轻量级编程 Agent）和 Claude Code（Anthropic 官方 CLI）是两个完全不同的系统，不要混淆。

核心设计：两阶段记忆管道
- Phase 1（Rollout Extraction）：Session 结束时后台并行提取记忆（gpt-5.4-mini，Low reasoning）
- Phase 2（Consolidation）：单例 Sub-Agent 将 Phase-1 输出合并精炼（gpt-5.4，Medium reasoning）

必读：docs/codex/memory-design.md

### Claude Code 记忆系统（对比参考）

核心设计：四层分散式架构
- Auto-Memory：Session 中自动捕获
- CLAUDE.local.md：个人偏好，仅自己可见
- CLAUDE.md：项目规范，所有贡献者可见
- Team Memory：Org 级知识，跨仓库复用

必读：docs/claude-code/memory-design.md

### Claw Code 哲学

Claw Code 证明了"人设定方向，Claw 执行"模式的可行性：
- Discord 是真正的 UX：人从手机发一句话，Claw 读完分解任务、并行执行、测试恢复、最后推送
- 瓶颈已转移：Agent 能快速生成代码后，真正的稀缺资源变成架构清晰度、任务分解能力、判断力
- 协调层即产品：代码只是产物，真正的 lesson 是协调系统本身

### OpenClaw 理念

- 本地优先：Gateway 运行在用户自己的设备上
- 渠道即入口：支持 20+ 消息渠道
- 安全即默认：DM 默认 pairing 模式
- TypeScript-first：保持可 hack 性

### 2026 Agent 技术方向

- 多智能体协作（Planning -> Execution -> Review 循环）
- 本地/端侧 LLM 部署（隐私 + 成本）
- 工具调用从"能用"到"好用"
- 推理时计算扩展（Long thinking）

---

## 四大 Agent 系统对比

```
OpenAI Codex          Claude Code             Claw Code              OpenClaw
(Rust CLI)            (Anthropic CLI)        (Rust 重写)            (TypeScript)

记忆: 两阶段管道       记忆: 四层分散式         记忆: 无内置            记忆: 文件级
     Phase1->Phase2      auto->local->proj      (Skill 扩展)           MEMORY.md+每日

协调: 层级Agent        协调: BG+Main补位        协调: OmO三层           协调: subagent
     Hierarchical         Main+Background         ClawHIP事件            sessions_spawn

入口: AGENTS.md         入口: CLAUDE.md          入口: PHILOSOPHY.md     入口: SOUL.md
     (路径层级覆盖)       (单一文件)               (哲学文档)              (人格文档)
```

---

## 持续更新

本仓库随学习持续更新。当前版本：2026-04-17
