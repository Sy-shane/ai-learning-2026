# OpenAI Codex 记忆系统设计

> 基于 openai/codex 源码分析，2026-04-17

---

## 核心定位

**OpenAI Codex** 是一个用 Rust 构建的轻量级编程 Agent，跑在终端里。支持 CLI、IDE 插件（VS Code / Cursor / Windsurf）和桌面 App 三种形态。

仓库：https://github.com/openai/codex
Stars：75,873（截至 2026-04-17）
语言：Rust（Bazel + Cargo）

---

## OpenAI Codex vs Claude Code：关键区别

| 维度 | OpenAI Codex | Claude Code |
|---|---|---|
| **语言** | Rust | TypeScript / Node.js |
| **入口文件** | `AGENTS.md` | `CLAUDE.md` |
| **记忆系统** | 两阶段管道（Phase1 → Phase2） | 多层分散式（Auto → CLAUDE.local → CLAUDE.md） |
| **协调模式** | Hierarchical Agents（父子 Agent） | Background Agent + Main Agent 补位 |
| **记忆粒度** | Session 级提取 → 全局合并 | Entry 级（逐条记忆） |
| **模型** | GPT-5 系列（内部） | Claude 系列 |
| **输出格式** | 结构化 Memory Record + Consolidation Agent | 自然语言记忆条目 |

---

## 两阶段记忆管道（Two-Phase Memory Pipeline）

Codex 的记忆系统是全自动的管道，分为 **Phase 1** 和 **Phase 2**：

```
用户结束一个 Session
       │
       ▼
  ┌─────────────┐
  │  Phase 1    │  ← 并行执行，多个 rollout 同时提取
  │  Rollout    │
  │  Extraction  │
  └──────┬──────┘
         │ 结构化 raw_memory + rollout_summary
         ▼
  ┌─────────────┐
  │  Phase 2    │  ← 单例，全局合并（同一时间只有一个 Consolidation）
  │  Consoli-   │
  │  dation     │
  └──────┬──────┘
         │ 最终记忆产物
         ▼
  ~/.codex/memories/
  ├── raw_memories.md          # 所有 raw memories 合并
  └── rollout_summaries/       # 每个 session 的摘要
```

### Phase 1：Rollout Extraction（推出提取）

**目标**：从每个符合条件的 Session 中提取结构化记忆。

**触发条件**（全部满足才运行）：
- Session 不是 ephemeral（临时会话）
- Memory feature 已启用
- 不是 sub-agent session
- State DB 可用

**执行流程**：
1. 从 State DB 获取符合条件的 rollout 列表（按时间/活跃度排序）
2. 对每个 rollout **并行**提取（并发上限 = 8）
3. 每个 rollout 调用模型（`gpt-5.4-mini`，Low reasoning effort）
4. 模型输出结构化内容：
   ```json
   {
     "raw_memory": "详细的记忆内容...",
     "rollout_summary": "简洁摘要（用于 UI）",
     "rollout_slug": "可选的 slug 标识"
   }
   ```
5. 敏感信息自动脱敏
6. 结果写回 State DB（stage-1 output）

**并发控制**：
- 每个 Job 在 State DB 中声明 lease（1 小时）
- 失败后等 1 小时再重试（避免热循环）
- 每次启动最多处理 5000 个 threads

**Job 结果分类**：
- `succeeded`：成功提取
- `succeeded_no_output`：运行了但没产出有用内容
- `failed`：失败（带重试 backoff）

### Phase 2：Consolidation（全局合并）

**目标**：将 Phase 1 的输出合并为统一记忆产物，并运行 Consolidation Sub-Agent 精炼。

**执行流程**：
1. 申请全局 Phase-2 lock（同一时间只有一个 Consolidation 运行）
2. 从 State DB 加载 Phase-1 输出（最多 `max_raw_memories_for_consolidation` 条）
3. 按使用频率和最近使用时间排序
4. 同步本地文件系统：
   - `raw_memories.md`：所有 raw memories，最新的在最前
   - `rollout_summaries/`：每个保留 rollout 的摘要文件
5. 清理过期的 rollout summaries
6. 如果有 Extension 资源，也纳入保留/清理范围
7. **运行 Consolidation Sub-Agent**（`gpt-5.4`，Medium reasoning effort）：
   - 无 approvals、无网络、仅本地写权限
   - 输入：Phase-1 输出 diff（added / retained / removed）
   - 产出：精炼后的高层记忆
8. 更新 State DB 的 watermark（避免重复处理）

**Selection Diff**：
- Phase-2 记录它消费了哪些 stage-1 snapshots
- 下次 Phase-2 对比上次 selection vs 本次 selection
- 标记为：`added` / `retained` / `removed`
- removed 的 evidence 在 consolidation 完成前仍保留在磁盘（供 Agent 参考）

**为什么分成两个 Phase**：
- Phase 1 可以大规模并行（多个 rollout 同时提取）
- Phase 2 必须是单例（共享记忆文件，不能并发写）

---

## AGENTS.md：项目文档入口

Codex 用 `AGENTS.md` 作为项目级文档的默认文件名（Claude Code 用 `CLAUDE.md`）。

### 发现机制

```
1. 从 CWD 向上找 project root（默认找 .git 目录）
2. 从 project root 到 CWD，收集所有 AGENTS.md
3. 路径上更深的 AGENTS.md 覆盖更浅的（就近优先）
4. 不跨 project root
```

### 本地覆盖

- `AGENTS.override.md`：本地开发者私有覆盖（不提交到代码库）
- 适合个人偏好、调试配置等

### 与 Claude Code 的 CLAUDE.md 对比

| 维度 | Codex AGENTS.md | Claude Code CLAUDE.md |
|---|---|---|
| **优先级** | 路径越深优先级越高 | 单一入口 |
| **本地覆盖** | `AGENTS.override.md` | `CLAUDE.local.md` |
| **拼接方式** | 从 root 到 CWD 顺序拼接 | 独立文件 |
| **与 config 关系** | 与 `instructions` 配置拼接 | 独立加载 |

---

## Hierarchical Agents（层级 Agent）

Codex 支持父子层级的 Agent 协调：

```
Root Agent (用户直接对话)
    │
    ├── Child Agent A（执行子任务）
    ├── Child Agent B（执行子任务）
    └── Child Agent C（执行子任务）
```

**关键设计**：
- `child_agents_md` feature flag 控制是否启用层级 Agent
- 启用时，Codex 会自动注入关于 AGENTS.md scope 和优先级的额外引导
- 子 Agent 共享父 Agent 的上下文（受限）
- Sub-agent session 不会触发 Phase-1 提取（避免递归记忆）

---

## Skills 系统

Codex 的 Skills 系统与 OpenClaw 的 Skills 类似，但实现更深：

```rust
// SkillsManager 管理所有 skill 加载、依赖解析、注入
pub use codex_core_skills::{
    SkillMetadata,
    SkillError,
    SkillLoadOutcome,
    SkillPolicy,
    SkillsManager,
    build_skill_injections,
    detect_implicit_skill_invocation_for_command,
    // ...
};
```

**Skill 依赖解析**：
```rust
// Session 启动时，SkillsManager 解析 skill 依赖
// 如果依赖的 env var 未设置，向用户请求
pub(crate) async fn resolve_skill_dependencies_for_turn(
    sess: &Arc<Session>,
    turn_context: &Arc<TurnContext>,
    dependencies: &[SkillDependencyInfo],
) {
    // 从 env var 加载已有值
    // 缺失的向用户请求
}
```

---

## 沙箱与安全模型

Codex 使用多层次沙箱：

```rust
// Sandboxed tool execution
use codex_sandboxing::{linux_sandbox, windows_sandbox};

// 网络策略
NetworkPolicyDecision: 
  - CODEX_SANDBOX_NETWORK_DISABLED=1（沙箱内完全断网）
  - CODEX_SANDBOX=seatbelt（macOS Seatbelt）

// 工具调用需要 approval
pub enum AskForApproval {
    Tool { tool_name, reason },
}
```

**Session 权限控制**：
- Session 可以配置 `exec_policy`（执行策略）
- 支持白名单/黑名单工具列表
- 敏感操作需要用户显式批准

---

## 关键架构文件索引

```
codex-rs/core/src/
├── memories/
│   ├── mod.rs           ← 模块入口，定义常量（gpt-5.4-mini, gpt-5.4 模型选择）
│   ├── start.rs         ← 启动入口：start_memories_startup_task()
│   ├── phase1.rs       ← Phase 1：Rollout extraction
│   ├── phase2.rs       ← Phase 2：Consolidation agent
│   ├── storage.rs      ← 文件系统读写
│   ├── prompts.rs      ← Prompt 模板
│   └── README.md       ← 官方 pipeline 文档（本文档大量参考）
├── agents_md.rs         ← AGENTS.md 发现与拼接
├── skills.rs            ← Skills 加载与依赖解析
├── message_history.rs   ← 历史消息管理
└── context_manager.rs   ← 上下文窗口管理
```

---

## 与 Claude Code 记忆系统的核心差异

| 设计维度 | OpenAI Codex | Claude Code |
|---|---|---|
| **架构** | 两阶段管道（提取→合并） | 多层文件（auto→local→project） |
| **触发方式** | Session 结束时自动后台运行 | Session 结束时自动提取 + remember skill 手动整理 |
| **组织方式** | Session 级 + 全局合并 | Entry 级（逐条记忆） |
| **记忆内容** | 结构化 raw_memory + rollout_summary | 自然语言条目（分类决定去处） |
| **整理方式** | Consolidation Sub-Agent 自动精炼 | remember skill 人工批准 |
| **大小管理** | Phase-1 token limit（150k）+ context window 70% | 200行 / 25KB 硬限制 |
| **并发模型** | Phase-1 并行（8并发），Phase-2 单例 | Main + Background Agent 并行 |

---

## 给我们的教训

1. **两阶段管道是规模化记忆的关键**：Phase-1 可以并行扩展，Phase-2 保证一致性。Claude Code 的 Main+Background Agent 模式在规模化时会遇到一致性问题
2. **Session 是记忆的自然单元**：Codex 用 rollout（session）作为记忆粒度，比 entry-by-entry 更结构化
3. **Consolidation Agent 代替手动整理**：Claude Code 的 remember skill 需要人审批，Codex 用一个 Sub-Agent 自动化了这个过程
4. **AGENTS.md 的层级覆盖**值得借鉴：路径越深优先级越高，更符合"就近配置更精确"的直觉
5. **Rust 实现**：Codex 选择 Rust 说明记忆系统需要高性能（大量并发 I/O + 模型调用）
