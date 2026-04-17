# 工具调用能力演进

> 从 Function Calling 到 MCP：2026 年工具调用生态，2026-04-17

## 工具调用的发展阶段

### Stage 1：字符串匹配 (2022 以前)

```
用户：帮我查天气
系统：识别"天气"关键词 → 调用 weather API → 返回结果
```

问题：脆弱、无法处理复杂请求、无法组合工具。

### Stage 2：Function Calling / Tool Use (2023-2024)

LLM 输出结构化 JSON，明确指定调用哪个工具和参数：

```json
{
  "tool_calls": [{
    "name": "get_weather",
    "arguments": {"location": "上海", "unit": "celsius"}
  }]
}
```

**代表**：
- OpenAI Function Calling (2023)
- Anthropic Tool Use (2024)
- Google Function Declarations

**改进**：类型安全、可组合、可验证。

### Stage 3：工具链与纠错 (2025)

单步工具调用 → 多步工具链：

```
用户：帮我买下这只股票
Agent：
  1. get_stock_price("AAPL") → $178
  2. check_account_balance() → $10,000 ✓
  3. buy_stock("AAPL", 10) → 订单确认
  4. send_confirmation_email() → 完成
```

**改进**：
- **Parallel calling**：多个独立工具同时调用
- **Error recovery**：工具失败时自动重试或换方案
- **Conditional calling**：根据上一步结果决定下一步

### Stage 4：标准化协议 (2026)

工具数量爆炸 → 需要标准化。

**MCP (Model Context Protocol)**：Anthropic 2024 年底发布，成为事实标准。

```
┌──────────────┐
│   AI Agent   │
└──────┬───────┘
       │ MCP (std)
       ▼
┌──────────────┐
│  MCP Server  │  ← 文件系统
└──────────────┘
┌──────────────┐
│  MCP Server  │  ← GitHub
└──────────────┘
┌──────────────┐
│  MCP Server  │  ← Slack
└──────────────┘
```

**核心价值**：
- **工具发现**：Agent 自动发现可用的工具
- **标准化接口**：同一协议连接任何 MCP Server
- **生态共享**：写一次 MCP Server，所有 Agent 都能用

## MCP 生态 (2026)

### 主流 MCP Server

| MCP Server | 功能 | 维护方 |
|---|---|---|
| **Filesystem** | 本地文件读写 | 官方 |
| **GitHub** | PR/Issue/Repo 操作 | 官方 + 社区 |
| **Slack** | 消息发送/读取 | 官方 |
| **Puppeteer** | 浏览器自动化 | 社区 |
| **SQL Database** | 数据库查询 | 社区 |
| **Memory** | 知识库 | 社区 |
| **RAG** | 检索增强生成 | 社区 |

### OpenClaw 的 MCP 策略

OpenClaw **不内置 MCP runtime**，而是通过 `mcporter` 桥接：

- MCP 集成与核心解耦
- 无需重启 Gateway 即可增删 MCP Server
- 减少 MCP 变动对核心的影响

## 工具调用的设计挑战

### 1. 工具描述的质量

工具描述是 Agent 选择工具的唯一依据：

```json
{
  "name": "send_email",
  "description": "发送邮件给指定收件人"
}
// ❌ 模糊：Agent 可能误用于"读取邮件"
}

{
  "name": "send_email",
  "description": "发送新邮件，包含 subject、body、to 字段。
  不适用于：读取邮件、列出邮件、删除邮件。
  返回：发送成功/失败状态。"
}
// ✅ 精确：避免误用
```

### 2. 参数验证

工具参数需要验证：

```python
# ❌ 直接执行用户输入
result = send_email(to=user_input, ...)

# ✅ 类型验证 + 范围检查
assert isinstance(to, str)
assert "@" in to
assert len(to) < 320  # RFC 5321
result = send_email(to=to, ...)
```

### 3. 权限控制

工具调用需要权限边界：

```
User → "帮我删掉所有文件"
Agent → 权限检查：delete_file 需要 confirm=True
Agent → "这个操作很危险，请确认你要删除哪些文件？"
```

## 工具调用的未来

### 趋势 1：工具调用成为一等公民

不再把工具调用当作 LLM 的"附加功能"，而是作为独立的计算原语。

### 趋势 2：工具可组合

```python
# 未来的工具组合语法
result = (
    get_weather("上海")
    >> get_news(category="tech")
    >> summarize(max_tokens=100)
)
```

### 趋势 3：工具即 Agent

工具本身可以是 Agent（带工具的 LLM）：

```json
{
  "name": "research_agent",
  "description": "研究 Agent，可搜索网页、读取文档、输出报告",
  "is_agent": true
}
```

### 趋势 4：安全工具标准

随着 Agent 执行危险操作，安全工具标准会建立：
- 工具的 capability 声明
- 权限的动态授予/撤销
- 工具调用的可审计日志

## 给 Agent 开发者的工具设计指南

1. **描述优先**：工具描述比参数更重要，要包含用途 + 限制
2. **类型安全**：JSON Schema 定义参数类型
3. **幂等性**：同一调用多次执行结果一致
4. **错误恢复**：工具失败时返回清晰错误，不要静默失败
5. **权限显式**：需要高权限的操作要明确标注
6. **MCP 兼容**：新工具优先实现 MCP 接口
