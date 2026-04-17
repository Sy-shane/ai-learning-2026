# CLAUDE.md — AI Agent 上下文

本文件为 AI Agent 提供项目上下文。每次对话开始时，Agent 应阅读此文件。

## 项目概述

`ai-learning-2026` 是一个持续更新的 AI 学习知识库，聚焦三大方向：
1. **Agent 设计理念**：OpenAI Codex、Claude Code、Claw Code、OpenClaw 的设计哲学与架构
2. **2026 前沿方向**：Agent Framework、多智能体、本地 LLM、推理模型
3. **工具生态**：从开源 Agent 源码中学习工程实践

---

## ⭐ 核心学习优先级

### 第一优先级：OpenAI Codex 记忆系统

**这是本仓库最重要的研究成果。**

OpenAI Codex（openai/codex）和 Claude Code（Anthropic 官方）是**两个完全不同的系统**，不要混淆。

**必读文档**：[docs/codex/memory-design.md](docs/codex/memory-design.md)

核心要点（两阶段管道）：
- **Phase 1（Rollout Extraction）**：Session 结束时后台并行提取记忆（gpt-5.4-mini，Low reasoning）
- **Phase 2（Consolidation）**：单例 Sub-Agent 将 Phase-1 输出合并精炼（gpt-5.4，Medium reasoning）
- **Session 是记忆的自然单元**：每个 rollout 提取一条 raw_memory + rollout_summary
- **Consolidation Agent**：自动精炼，不需要人工审批

仓库：https://github.com/openai/codex

### 第二优先级：Claude Code 记忆系统（对比参考）

**Anthropic 官方的 CLI 工具，与 OpenAI Codex 是两个不同系统。**

**必读文档**：[docs/claude-code/memory-design.md](docs/claude-code/memory-design.md)

核心要点（四层分散式）：
- **四层架构**：Auto-Memory → CLAUDE.local（个人）→ CLAUDE.md（项目）→ Team Memory（团队）
- **remember skill**：主动整理记忆，呈现提案，用户批准后才执行
- **200 行 / 25KB 硬限制**：防止 MEMORY.md 塞满上下文
- **Main + Background 双主体**：Main Agent 写，Background Agent 补漏

仓库：https://github.com/anthropics/claude-code（已撤回，泄露版：https://github.com/jarmuine/claude-code）

### 第三优先级：Claw Code 协调系统

**文档**：[docs/claw-code/](docs/claw-code/)

核心要点：
- **ClawHIP**：事件路由器，将通知外置到 Agent 上下文之外
- **OmX**：工作流层，将自然语言指令转为结构化执行协议
- **OmO**：多 Agent 协调，解决 Architect/Executor/Reviewer 分歧
- **状态机设计**：Worker 生命周期状态（spawning → ready → running → finished）

仓库：https://github.com/ultraworkers/claw-code

### 第四优先级：OpenClaw 架构

**文档**：[docs/openclaw/](docs/openclaw/)

核心要点：
- **Gateway 架构**：Session / Channel / Tools 三层解耦
- **安全模型**：DM pairing 模式，安全默认值
- **Skills 系统**：SKILL.md 驱动的轻量扩展机制

仓库：https://github.com/openclaw/openclaw

---

## 四大系统关键区分

| 系统 | 公司 | 入口文件 | 记忆设计 | 语言 |
|---|---|---|---|---|
| **OpenAI Codex** | OpenAI | `AGENTS.md` | 两阶段管道（Phase1→Phase2） | Rust |
| **Claude Code** | Anthropic | `CLAUDE.md` | 四层分散式 | TypeScript |
| **Claw Code** | 社区 | `PHILOSOPHY.md` | 无内置（Skill 扩展） | Rust |
| **OpenClaw** | OpenClaw | `SOUL.md` | 文件级（MEMORY.md） | TypeScript |

---

## 当前学习进度

- [x] **OpenAI Codex 记忆系统（两阶段管道）** — 核心成果
- [x] **Claude Code 记忆系统（四层架构）** — 对比参考
- [x] Claw Code 哲学、架构、生态、Parity
- [x] OpenClaw 愿景、架构、Skills、安全模型
- [x] 2026 AI 趋势：Agent Framework、多智能体、本地 LLM、推理模型、工具调用
- [ ] 添加 GitHub Actions 每周自动更新 workflow
- [ ] 补充 Codex Skills 系统详细分析
- [ ] 补充 Codex Sandbox 安全模型分析

---

## 写作风格指南

1. **结论先行**：首句必须包含核心结论
2. **受众适配**：技术实现 vs 战略洞察内容分层
3. **可扫描性**：长段落用多级标题拆解
4. **引用原始**：关键引述标注来源，不过度解读
5. **⭐ 标记优先级**：重要内容用 ⭐ 标记，方便快速定位
6. **区分系统**：OpenAI Codex 和 Claude Code 是不同系统，不得混用

---

## 关于仓库维护

- 核心原则：**OpenAI Codex 记忆设计 > Claude Code 记忆设计 > 协调系统 > 框架实现**
- PR Welcome：发现错误或有补充，直接提 PR
- 讨论：使用 GitHub Issues 进行主题讨论
- 自动化：GitHub Actions 每周更新（待实现，需 workflow scope PAT）
