# Claw Code 架构解析

> 基于 ultraworkers/claw-code 源码结构分析，2026-04-17

## 仓库结构

```
claw-code/
├── rust/                    # Rust 工作区（主流）
│   └── crates/
│       ├── api/             # Provider 客户端、流式响应、认证
│       ├── commands/        # Slash 命令注册表和帮助渲染
│       ├── compat-harness/  # 上游 TS 源码的 Manifest 提取
│       ├── mock-anthropic-service/  # 确定性本地 mock 服务
│       ├── plugins/         # 插件元数据和管理
│       ├── runtime/         # Session、Config、权限、MCP、Prompt 循环
│       ├── rusty-claude-cli/# 主 CLI 二进制 (`claw`)
│       ├── telemetry/       # Session 追踪和使用量遥测
│       └── tools/           # 内置工具、技能解析、工具搜索
├── src/                     # Python/参考实现（非主流运行时）
├── tests/                   # 测试套件
├── docs/                    # 文档
├── CLAUDE.md                # Agent 上下文指南
├── USAGE.md                 # 使用指南
├── PHILOSOPHY.md            # 项目哲学
├── PARITY.md                # Rust 移植 Parity 状态
└── ROADMAP.md               # 路线图
```

## 核心架构设计

### 状态机优先

Claw Code 的核心设计原则：**每个 Worker 都有显式生命周期状态**。

定义的 Worker 状态：
- `spawning` → 正在启动
- `trust_required` → 等待信任提示确认
- `ready_for_prompt` → 等待接收任务
- `prompt_accepted` → 任务已被接受
- `running` → 执行中
- `blocked` → 阻塞（等待某资源）
- `finished` → 完成
- `failed` → 失败

**为什么重要**：传统 Agent harness 的问题在于"session 存在"不等于"session 就绪"。Claw Code 通过显式状态机解决了这个问题。

### Prompt 契约 SLA

在 `ready_for_prompt` 之后，暴露第一个任务是否在有限窗口内被实际接受的信号：
- `prompt.sent` — 任务已发送
- `prompt.accepted` — 任务被接受
- `prompt.acceptance_delayed` — 接受延迟
- `prompt.acceptance_timeout` — 超时

### 认证体系

支持多种凭证格式（容易搞混，这是最常见的 401 原因）：

| 凭证类型 | 环境变量 | HTTP Header | 典型来源 |
|---|---|---|---|
| `sk-ant-*` API Key | `ANTHROPIC_API_KEY` | `x-api-key: sk-ant-...` | console.anthropic.com |
| OAuth Access Token | `ANTHROPIC_AUTH_TOKEN` | `Authorization: Bearer ...` | 兼容代理或 OAuth Flow |
| OpenRouter Key | `OPENAI_API_KEY` + `OPENAI_BASE_URL` | `Authorization: Bearer ...` | openrouter.ai/keys |

**教训**：将 `sk-ant-*` 粘贴到 `ANTHROPIC_AUTH_TOKEN` 会得到 401，因为 API Key 不能放在 Bearer 头中。Claw Code 现在会检测这种确切错误并在错误信息中给出修复提示。

### 工具系统（Tool System）

Rust 版实现了完整的工具链：
- **bash** — 执行 shell 命令
- **read / glob / grep** — 文件系统操作
- **write / edit** — 文件修改
- **web search / fetch** — Web 工具
- **sub-agent / agent surfaces** — 子 Agent 协作
- **todo tracking** — 任务追踪
- **MCP server lifecycle** — MCP 服务管理
- **session persistence** — Session 持久化和恢复
- **git integration** — Git 集成
- **cost / usage / stats** — 使用量追踪

### Mock Parity Harness

最有特色的功能之一：确定性测试框架。

```bash
cd rust
./scripts/run_mock_parity_harness.sh
```

覆盖的测试场景：
- `streaming_text` — 流式文本响应
- `read_file_roundtrip` — 文件读取往返
- `write_file_allowed` — 写文件权限
- `write_file_denied` — 写文件拒绝
- `multi_tool_turn_roundtrip` — 多工具调用轮次
- `bash_stdout_roundtrip` — Bash 输出往返
- `bash_permission_prompt_approved` — Bash 权限提示批准
- `bash_permission_prompt_denied` — Bash 权限提示拒绝
- `plugin_tool_roundtrip` — 插件工具往返

## 与 Claude Code 的 Parity 追踪

参见 [parity-status.md](./parity-status.md)。

## 设计教训

1. **Terminal 是 Transport，不是 Truth**：tmux/TUI 是实现细节，编排状态必须在其之上
2. **Recovery 在 Escalation 之前**：已知失败模式应该先自动恢复一次，再请求人工介入
3. **Branch 新鲜度优先于 Blame**：在将红色测试视为新回归之前，先检测陈旧分支
4. **部分成功是一等的**：例如 MCP 启动可以对部分服务成功、对部分服务失败，报告结构化的降级模式
