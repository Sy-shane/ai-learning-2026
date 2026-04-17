# CLAUDE.md — AI Agent 上下文

本文件为 AI Agent 提供项目上下文。每次对话开始时，Agent 应阅读此文件。

## 项目概述

`ai-learning-2026` 是一个持续更新的 AI 学习知识库，聚焦三大方向：
1. **Agent 设计理念**：Claw Code、OpenClaw、Claude Code 的设计哲学与架构
2. **2026 前沿方向**：Agent Framework、多智能体、本地 LLM、推理模型
3. **工具生态**：从 Claude Code 源码泄露事件中学习工程实践

---

## ⭐ 核心学习优先级

### 第一优先级：Claude Code 记忆系统设计

这是本仓库**最重要**的研究成果。Claude Code 的记忆系统开创了多层分散式架构，是所有 AI Agent 记忆设计的标杆。

**必读文档**：[docs/claude-code/memory-design.md](docs/claude-code/memory-design.md)

核心要点：
- **四层架构**：Auto-Memory → CLAUDE.local（个人）→ CLAUDE.md（项目）→ Team Memory（团队）
- **remember skill**：主动整理记忆，呈现提案，用户批准后才执行——不是被动积累
- **200 行 / 25KB 硬限制**：科学测量出来的合理上限，防止塞满上下文
- **Main + Background 双主体**：Main Agent 写，Background Agent 补漏
- **Cost Tracker**：记忆的"成本孪生"，记录每次 session 资源消耗

### 第二优先级：Claw Code 协调系统

**文档**：[docs/claw-code/](docs/claw-code/)

核心要点：
- **ClawHIP**：事件路由器，将通知外置到 Agent 上下文之外
- **OmX**：工作流层，将自然语言指令转为结构化执行协议
- **OmO**：多 Agent 协调，解决 Architect/Executor/Reviewer 分歧
- **状态机设计**：Worker 生命周期状态（spawning → ready → running → finished）

### 第三优先级：OpenClaw 架构

**文档**：[docs/openclaw/](docs/openclaw/)

核心要点：
- **Gateway 架构**：Session / Channel / Tools 三层解耦
- **安全模型**：DM pairing 模式，安全默认值
- **Skills 系统**：SKILL.md 驱动的轻量扩展机制

---

## 关键项目参考

### Claude Code (泄露源码)
- **仓库**：https://github.com/jarmuine/claude-code
- 核心模块：`cost-tracker.ts`、`skills/bundled/remember.ts`、`memdir/`
- 核心价值：多层记忆架构、remember skill、cost tracking
- **重点**：`src/memdir/memdir.ts`、`src/skills/memoryTypes.ts`、`src/skills/findRelevantMemories.ts`

### Claw Code (ultraworkers/claw-code)
- **仓库**：https://github.com/ultraworkers/claw-code
- 185k+ stars，Rust 重写版 CLI Agent harness
- 三层协调系统：OmX + ClawHIP + OmO
- **重点**：`PHILOSOPHY.md`、`ROADMAP.md`

### OpenClaw (openclaw/openclaw)
- **仓库**：https://github.com/openclaw/openclaw
- 个人 AI 助手框架，TypeScript 实现
- 支持 20+ 消息渠道，Gateway 本地运行
- **重点**：`VISION.md`、`docs/concepts/architecture.md`

---

## 当前学习进度

- [x] Claude Code 记忆系统设计（**核心成果**）
- [x] Claw Code 哲学、架构、生态、Parity
- [x] OpenClaw 愿景、架构、Skills、安全模型
- [x] 2026 AI 趋势：Agent Framework、多智能体、本地 LLM、推理模型、工具调用
- [ ] 添加 GitHub Actions 每周自动更新 workflow
- [ ] 补充 Claude Code cost-tracker 详细分析

---

## 写作风格指南

1. **结论先行**：首句必须包含核心结论
2. **受众适配**：技术实现 vs 战略洞察内容分层
3. **可扫描性**：长段落用多级标题拆解
4. **引用原始**：关键引述标注来源，不过度解读
5. **⭐ 标记优先级**：重要内容用 ⭐ 标记，方便快速定位

---

## 关于仓库维护

- 核心原则：**记忆设计 > 协调系统 > 框架实现**，权重依次递减
- PR Welcome：发现错误或有补充，直接提 PR
- 讨论：使用 GitHub Issues 进行主题讨论
- 自动化：GitHub Actions 每周更新（待实现，需 workflow scope PAT）
