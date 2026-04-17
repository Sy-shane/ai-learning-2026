# 多智能体协作系统

> 从单 Agent 到多 Agent 协作的设计模式与挑战，2026-04-17

## 为什么需要多智能体

单 Agent 的瓶颈：
- **上下文饱和**：复杂任务需要太多信息塞进一个上下文
- **能力单一**：一个 Agent 很难同时是代码专家 + 产品经理 + 测试工程师
- **单点失败**：一个 Agent 失败导致整个任务失败

多 Agent 解决思路：**分工 + 协作**。

## 协作模式

### 模式一：Sequential Pipeline

```
User → Planner → Executor → Reviewer → Output
         ↓
    （如果 Reviewer 发现问题，回退到 Planner）
```

**适用场景**：需要多步骤验证的复杂任务
**代表项目**：Claw Code 的 Architect → Executor → Reviewer 循环

### 模式二：Parallel Execution

```
              → Task A (Executor 1) ──┐
User → Router ──→ Task B (Executor 2) ──┼──→ Aggregator → Output
              → Task C (Executor 3) ──┘
```

**适用场景**：独立子任务，可以并行处理
**代表项目**：CrewAI 的 agents 并行执行

### 模式三：Hierarchical Management

```
           Manager Agent
           /      |      \
    Sub-Agent  Sub-Agent  Sub-Agent
```

**适用场景**：大型项目，需要管理层分解任务
**警告**：OpenClaw 明确表示不采用 nested planner tree 作为默认架构

### 模式四：Event-Driven Collaboration

```
Agent A ──(event)──→ ClawHIP ──(notify)──→ Agent B
```

**适用场景**：需要跨 Agent 通知但不占用上下文
**代表项目**：Claw Code 的 ClawHIP 事件系统

## 关键挑战

### 1. 通信协议

Agent 之间如何传递信息？
- **共享上下文**：共享一个上下文窗口（简单但贵）
- **消息传递**：Agent 间显式发送消息（复杂但可追踪）
- **事件总线**：ClawHIP 模式，事件外置（最优）

### 2. 分歧解决

当多个 Agent 对方案有分歧时怎么办？

Claw Code 的 OmO 框架：
1. 各自陈述理由（Architect A 方案 vs Executor B 方案）
2. 评估约束（时间 / 质量 / 复杂度）
3. Coordinator 做最终决定
4. 落选方接受结果，继续执行

### 3. 上下文管理

每个 Agent 有自己的上下文，如何共享知识？

**早期方案**：把所有信息塞给每个 Agent（上下文爆炸）
**2026 方案**：
- 外部化知识到共享存储
- Agent 按需查询
- ClawHIP 负责通知，不传完整上下文

### 4. 信任与权限

- Agent A 能代表用户执行什么操作？
- Executor 能修改哪些文件？
- 谁有最终审批权？

## 设计反模式

### 反模式一：把所有 Agent 塞进一个 LLM 调用

让一个超级 Agent 同时扮演多个角色 → 成本爆炸 + 输出不稳定

### 反模式二：过度架构

为了多 Agent 而多 Agent → 增加复杂度但不解决问题

### 反模式三：共享同一个上下文

让 5 个 Agent 共享同一个 200k token 上下文 → 疯狂的成本

## 2026 最佳实践

1. **先做单 Agent**：多 Agent 是优化，不是起点
2. **事件外置**：ClawHIP 模式是正确方向
3. **强类型通信**：Agent 间不用自然语言，用结构化消息
4. **独立可测试**：每个 Agent 应该能独立运行测试
5. **Coordinator 做最终决定**：不要民主投票，要有人负责

## 给 OpenClaw 的启示

OpenClaw 的 subagent 系统目前支持：
- `sessions_spawn`：创建隔离子会话
- `sessions_send`：向子会话发送消息
- `subagents`：列出、引导、终止子 Agent

这是一个好的起点，但还缺少事件外置机制。ClawHIP 的设计值得借鉴。
