# Claw Code Parity 状态追踪

> 与上游 Claude Code 的功能 Parity 进度，2026-04-17

## Parity 目标定义

"Parity"意味着 Claw Code（Rust 实现）的功能集合与 Claude Code（Python/Node.js 原始实现）对齐。目标不是像素级复制，而是**功能等价**：相同的工具集、相同的权限模型、相同的 session 语义。

## 当前 Parity 状态

| 功能 | Claude Code | Claw Code (Rust) | 状态 |
|---|---|---|---|
| Anthropic/OpenAI Provider + Streaming | ✅ | ✅ | ✅ Parity |
| 直接 Bearer Token 认证 | ✅ | ✅ | ✅ Parity |
| Interactive REPL (rustyline) | ✅ | ✅ | ✅ Parity |
| 内置工具 (bash/read/write/grep/glob) | ✅ | ✅ | ✅ Parity |
| Web 工具 (search/fetch) | ✅ | ✅ | ✅ Parity |
| Sub-agent / Agent Surfaces | ✅ | ✅ | ✅ Parity |
| Todo 追踪 | ✅ | ✅ | ✅ Parity |
| Notebook 编辑 | ✅ | ✅ | ✅ Parity |
| CLAUDE.md / 项目记忆 | ✅ | ✅ | ✅ Parity |
| Config 文件层级 | ✅ | ✅ | ✅ Parity |
| 权限系统 | ✅ | ✅ | ✅ Parity |
| MCP Server 生命周期 + 检查 | ✅ | ✅ | ✅ Parity |
| Session 持久化 + 恢复 | ✅ | ✅ | ✅ Parity |
| Cost / Usage / Stats | ✅ | ✅ | ✅ Parity |
| Git 集成 | ✅ | ✅ | ✅ Parity |
| Markdown 终端渲染 (ANSI) | ✅ | ✅ | ✅ Parity |
| Model 别名 (opus/sonnet/haiku) | ✅ | ✅ | ✅ Parity |
| 直接 CLI 子命令 | ✅ | ✅ | ✅ Parity |
| Slash Commands | ✅ | ✅ | ✅ Parity |
| Hooks (生命周期钩子) | ✅ | ✅ | ✅ Parity |
| 插件管理表面 | ✅ | ✅ | ✅ Parity |
| Skills 清单/安装表面 | ✅ | ✅ | ✅ Parity |
| 机器可读 JSON 输出 | ✅ | ✅ | ✅ Parity |
| ACP/Zed 集成 | ❌ (not in scope) | 🔄 In Progress | 🔄 WIP |

## ACP/Zed 状态说明

> [!WARNING]
> Claw Code 尚未发布 ACP/Zed daemon 入口点。运行 `claw acp`（或 `claw --acp`）获取当前状态。`claw acp serve` 目前只是一个可发现性别名，真正的 ACP 支持在 `ROADMAP.md` 中单独跟踪。

## Mock Parity Harness 状态

确定性测试框架已完全实现：

```bash
cd rust
cargo test --workspace
./scripts/run_mock_parity_harness.sh
```

主要产物：
- `crates/mock-anthropic-service/` — 可复用的确定性 mock 服务
- `crates/rusty-claude-cli/tests/mock_parity_harness.rs` — CLI 测试
- `scripts/run_mock_parity_harness.sh` — 可重现包装器
- `scripts/run_mock_parity_diff.py` — 场景清单 + PARITY 映射运行器
- `mock_parity_scenarios.json` — 场景到 PARITY 的 manifest

## 迁移笔记

Rust 移植过程中的关键决策：

1. **Workspace Layout**：每个 crate 单一职责（api/commands/runtime/tools 等）
2. **Mock First**：先实现确定性 mock，再验证真实 API 集成
3. **Feature Flag**：通过 Cargo feature 控制可选依赖
4. **CLI 表面**：保持与上游兼容的标志和子命令

## 持续 Parity 策略

- 每次 Claude Code 上游更新时，运行 `mock_parity_harness.sh` 验证
- 不追求 Copilot-level parity，聚焦 CLI harness 场景
- Parity 差异通过 `PARITY.md` 显式跟踪
