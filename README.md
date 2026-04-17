# AI Learning Knowledge Base 2026

> 持续更新的 AI 学习仓库，聚焦 Agent 设计理念、2026 前沿方向与工具生态。

---

## 📚 文档地图

```
ai-learning-2026/
├── CLAUDE.md                        ← AI Agent 上下文指南（必读）
├── docs/
│   ├── claude-code/                 # Claude Code 设计参考 ⭐核心
│   │   └── memory-design.md         # 记忆系统设计（最重要参考）
│   ├── claw-code/                   # Claw Code 完整解析
│   │   ├── philosophy.md            # 核心理念：人机协作哲学
│   │   ├── architecture.md          # Rust 架构、状态机、工具链
│   │   ├── ecosystem.md             # ClawHIP / OmX / OmO 生态
│   │   └── parity-status.md         # 与 Claude Code 的 Parity 追踪
│   ├── openclaw/                    # OpenClaw 设计与实现
│   │   ├── vision.md                # 项目愿景与定位
│   │   ├── architecture.md          # Gateway / Session / Channel 架构
│   │   ├── skills-system.md         # Skill 系统设计
│   │   └── security-model.md        # 安全模型与 DM 策略
│   └── 2026-ai-trends/             # 2026 AI 学习方向
│       ├── agent-frameworks.md      # Agent Framework 演进
│       ├── multi-agent-systems.md   # 多智能体协作系统
│       ├── local-llm.md             # 本地 LLM 与端侧推理
│       ├── reasoning-models.md      # 推理模型前沿
│       └── tool-use-evolution.md    # 工具调用能力演进
└── resources/
    └── links.md                     # 关键项目与参考链接
```

---

## 🎯 核心主题

### ⭐ Claude Code 记忆系统（第一优先学习）
Claude Code 的记忆系统是最值得深入研究的模块，开创了多层分散式架构：

- **四层记忆**：Auto-Memory → CLAUDE.local（个人）→ CLAUDE.md（项目）→ Team Memory（团队）
- **remember skill**：不是被动积累，而是主动整理——呈现提案，用户批准后才执行
- **200 行 / 25KB 硬限制**：防止 MEMORY.md 塞满上下文窗口
- **Main + Background 双主体**：Main Agent 写，Background Agent 补漏，互相补位
- **Cost Tracker**：记录每次 session 的 token 消耗，是记忆系统的"成本孪生"

👉 **必读**：[docs/claude-code/memory-design.md](docs/claude-code/memory-design.md)

### Claw Code 哲学
Claw Code 证明了"人设定方向，Claw 执行"模式的可行性：

- **Discord 是真正的 UX**：人从手机发一句话，Claw 读完分解任务、并行执行、测试恢复、最后推送
- **瓶颈已转移**：Agent 能快速生成代码后，真正的稀缺资源变成：架构清晰度、任务分解能力、判断力
- **协调层即产品**：代码只是产物，真正的 lesson 是协调系统本身

### OpenClaw 理念
- **本地优先**：Gateway 运行在用户自己的设备上，工具访问不经过第三方
- **渠道即入口**：支持 20+ 消息渠道，不需要改变用户习惯
- **安全即默认**：DM 默认 pairing 模式，未知发送者需要 explicit approval
- **TypeScript-first**：保持可 hack 性，降低 contribution 门槛

### 2026 Agent 技术方向
- 多智能体协作（Planning → Execution → Review 循环）
- 本地/端侧 LLM 部署（隐私 + 成本）
- 工具调用从"能用"到"好用"（结构化输出、工具纠错）
- 推理时计算扩展（Long thinking / compute at inference time）

---

## 🏗️ 核心设计模式（三大体系）

```
本仓库研究的三大 AI Agent 设计体系：

  Claude Code              Claw Code                OpenClaw
  (Anthropic 官方)        (社区 Rust 重写)          (个人助手框架)

  记忆系统 ──────────────▶ ClawHIP ──────────────▶  Event System
  Cost Tracker             事件路由器               通知路由

  remember skill ────────▶ OmO ─────────────────▶  subagent
  主动整理                 多 Agent 协调             任务分发

  Auto-memory ───────────▶ Background Agent ─────▶  cron skill
  后台提取                 后台任务                  定时任务
```

---

## 🔄 持续更新

本仓库随学习持续更新。当前版本：2026-04-17
