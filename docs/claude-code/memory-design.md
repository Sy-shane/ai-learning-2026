# Claude Code 记忆系统设计

> 基于 jarmuine/claude-code 泄露源码分析，2026-04-17

---

## 核心设计理念

Claude Code 的记忆系统不是单一文件，而是一个**多层分散式**架构：

```
┌──────────────────────────────────────────────┐
│            Claude Code Memory System          │
├──────────────────────────────────────────────┤
│  Layer 1: Auto-Memory (自动记忆)             │
│    自动捕获 · Session 中生成 · 后台提取       │
│                                              │
│  Layer 2: CLAUDE.md (项目规范)              │
│    显式沉淀 · 项目通用 · 所有贡献者可见       │
│                                              │
│  Layer 3: CLAUDE.local.md (个人偏好)         │
│    用户私有 · 仅自己可见 · 不进入项目代码库    │
│                                              │
│  Layer 4: Team Memory (团队共享)            │
│    可选配置 · Org 级知识 · 跨仓库复用         │
└──────────────────────────────────────────────┘
```

---

## Auto-Memory：自动捕获机制

### 触发时机

Claude Code 在每次 Session 结束时，自动提取记忆。触发条件：

```typescript
// 判断是否启用自动记忆
export function isAutoMemoryEnabled(): boolean {
  // 1. 环境变量显式关闭
  if (CLAUDE_CODE_DISABLE_AUTO_MEMORY === '1') return false
  // 2. --bare / SIMPLE 模式关闭
  if (CLAUDE_CODE_SIMPLE) return false
  // 3. 无持久化存储时关闭
  if (CCR without CLAUDE_CODE_REMOTE_MEMORY_DIR) return false
  // 4. settings.json 项目级配置
  // 5. 默认：开启
  return true
}
```

### 提取时机

Claude Code 在每次工具调用结束（turn-end fork）时，有两个并行的记忆活动：

1. **Main Agent**：负责将重要信息写入 CLAUDE.md / CLAUDE.local.md
2. **Background Agent**：负责将 session 中学到的内容写入 auto-memory（catch missed entries）

Main Agent 和 Background Agent 互相补位：
- Main Agent 写了 → Background Agent 跳过该段
- Main Agent 没写 → Background Agent 补漏

### 提取内容：memoryTypes.ts

```typescript
// 四类记忆类型
TYPES_SECTION_INDIVIDUAL: [
  "user preferences for communication or task execution",
  "how the user likes to receive information (detailed vs concise, etc.)",
  "things the user has explicitly asked you to remember or avoid",
  "things that worked or didn't work in previous interactions",
]
```

**What NOT to save**：
- 外部工具偏好（编辑器主题、IDE 快捷键）
- 工作流实践（属于项目规范还是个人习惯需确认）

---

## 记忆分流策略（remember skill）

Claude Code 提供了一个 `remember` skill，专门负责记忆整理：

### Skill Prompt 核心逻辑

```
1. 收集所有记忆层
   → 读取 CLAUDE.md, CLAUDE.local.md
   → 从 system prompt 获取 auto-memory 内容

2. 对每个 auto-memory entry 分类

   | 目的地        | 内容类型                    |
   |---------------|-----------------------------|
   | CLAUDE.md     | 项目规范，所有人可见          |
   | CLAUDE.local  | 个人偏好，仅自己可见          |
   | Team Memory   | Org 级知识，跨仓库           |
   | 留在 auto-memory | 临时笔记、不确定的内容    |

3. 识别清理机会
   - 重复：auto-memory 已存在于 CLAUDE.md → 提议删除
   - 过时：CLAUDE.md 条目被新内容推翻 → 提议更新
   - 冲突：两个 layer 矛盾 → 提议解决方案

4. 呈现报告
   - Promotions：待提升的条目
   - Cleanup：重复/过时/冲突
   - Ambiguous：需要用户确认
   - No action needed：保持原样
```

### 关键规则

```
- 呈现所有提案后再执行
- 不经用户批准不修改文件
- 不确定时询问，不要猜测
```

---

## MEMORY.md 设计约束

### 大小限制

```typescript
MAX_ENTRYPOINT_LINES = 200      // 最多 200 行
MAX_ENTRYPOINT_BYTES = 25_000   // 最多 25KB (~125 chars/line)

function truncateEntrypointContent(raw: string) {
  // 先按行截断（自然边界）
  // 再按字节截断（在最后一个换行处截断，避免切断半行）
}
```

**为什么限制 25KB**：
- 上下文窗口是有限资源
- MEMORY.md 会被加载到 system prompt 中
- 25KB 大约是 p97 大小，p100 观察到的是 197KB（异常长行）

### 截断策略

```
原文 → 去除首尾空白 → 统计行数和字节数
     ↓
如果超过 200 行：截取前 200 行
如果超过 25KB：在最后一个换行处截断
     ↓
追加截断警告，说明哪个限制触发了
```

---

## Cost Tracker：记忆的孪生系统

Claude Code 的 cost-tracker 是记忆系统的"成本孪生"——记录每次 session 的资源消耗：

### 追踪维度

```typescript
interface ModelUsage {
  inputTokens: number
  outputTokens: number
  cacheReadInputTokens: number   // 缓存命中节省
  cacheCreationInputTokens: number
  duration: number              // API 耗时
  costUSD: number               // 实际费用
}

// 每个 Model 的使用量
modelUsage: { [modelName: string]: ModelUsage }

// 全局统计
totalCostUSD: number
totalInputTokens: number
totalOutputTokens: number
totalLinesAdded: number
totalLinesRemoved: number
totalWebSearchRequests: number
totalAPIDuration: number        // 含重试
totalAPIDurationWithoutRetries: number  // 不含重试
totalToolDuration: number
```

### 成本计算

```typescript
function calculateUSDCost(model, tokens, cacheHits) {
  // 根据模型定价计算
  // 支持已知模型和未知模型（标记 hasUnknownModelCost）
}
```

### Session 级成本保存

```typescript
// 保存当前 session 的成本到项目配置
// 跨 session 恢复时读取历史成本
function getStoredSessionCosts(sessionId: string): StoredCostState | undefined {
  if (projectConfig.lastSessionId !== sessionId) {
    return undefined  // 不同 session 不混用
  }
  return projectConfig.lastModelUsage
}
```

---

## Team Memory：跨仓库知识共享

### 架构

```typescript
// Feature flag 控制
const teamMemPaths = feature('TEAMMEM')
  ? require('./teamMemPaths.js')
  : null
```

### 路径结构

```
~/.claude/
├── memory/
│   ├── MEMORY.md              # 个人入口
│   └── projects/
│       └── <project>/         # 项目级记忆
└── team-memory/               # 团队共享（可选）
    └── <org>/
        └── <team>/
```

### Team Memory 的适用场景

- Org 级规范（"#deploy-queue 频道发部署"）
- Staging 环境地址
- 平台团队负责基础设施
- **不是个人偏好，不是项目规范**

---

## 三层记忆的边界判断

这是 Claude Code 记忆系统最复杂的部分：

```
问题：这个记忆该放哪层？

判断流程：

1. 是"给 Claude 的指令"吗？
   → 不是：可能不是记忆，是工具配置

2. 是用户个人偏好（沟通风格、响应方式）？
   → 是 → CLAUDE.local.md

3. 是项目规范（测试命令、代码风格、API 路由命名）？
   → 是 → CLAUDE.md

4. 是 Org 级知识（跨仓库适用）？
   → 是 → Team Memory

5. 都不符合？
   → 留在 auto-memory
```

### 模糊场景

| 场景 | 判断 |
|---|---|
| PR 规范 | 个人？→ CLAUDE.local / 团队？→ Team Memory / 项目？→ CLAUDE.md |
| 编辑器主题 | **不要**写入任何记忆文件 |
| 测试命令 | 项目统一 → CLAUDE.md |
| 合并策略 | 团队共识 → Team Memory |

---

## 与 OpenClaw 记忆系统的对比

| 维度 | Claude Code | OpenClaw |
|---|---|---|
| **入口文件** | MEMORY.md | MEMORY.md + memory/*.md |
| **个人偏好** | CLAUDE.local.md | MEMORY.md 的 "About Your Human" |
| **项目规范** | CLAUDE.md | CLAUDE.md (通用) |
| **自动提取** | Background Agent turn-end fork | 记忆中无自动提取（Session 触发） |
| **记忆整理** | remember skill (显式触发) | 无对应功能 |
| **团队记忆** | Team Memory (可选) | 无 |
| **成本追踪** | 完整 (token/$/duration) | 无内置 |
| **大小限制** | 200行 / 25KB 硬限制 | 无 |

---

## 给我们的教训

1. **分层比集中更好**：把不同性质的记住内容分散到不同文件，比一个大文件更容易维护
2. **自动 + 手动互补**：Main Agent 写 + Background Agent 补漏，比单纯靠 LLM 自己记住更可靠
3. **记忆需要整理入口**：`remember` skill 的设计很好——不是被动积累，而是主动整理
4. **大小需要硬限制**：MEMORY.md 不设限最终会塞满上下文窗口，25KB + 200 行是科学测量出来的合理值
5. **Cost Tracking 是记忆的好搭档**：知道每次 session 花了多少，能帮助判断记忆使用的效率
