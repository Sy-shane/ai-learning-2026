# OpenClaw 安全模型

> 安全默认设计、DM 策略和运营指南，2026-04-17

## 核心原则

OpenClaw 连接到真实消息平台。**将入站 DM 视为不可信输入。**

安全目标：在保持真正工作的强大功能和让危险路径显式化并由操作员控制之间取得平衡。

## DM 安全策略

### 模式一：Pairing（默认）

未知发送者收到配对码，消息被拦截不处理。

```bash
# 用户发送消息 → Bot 回应配对码
# 操作员审批
openclaw pairing approve <channel> <code>
```

### 模式二：Open

任何人可以直接和 Bot 对话（需加入 allowlist）。

```json
{
  "channels": {
    "feishu": {
      "dmPolicy": "open",
      "allowFrom": ["*"]
    }
  }
}
```

### 模式三：Allowlist

只有白名单用户可以交互。

```json
{
  "dmPolicy": "open",
  "allowFrom": ["ou_xxx", "ou_yyy"]
}
```

## 权限系统

### Session 权限级别

| 权限模式 | 说明 |
|---|---|
| `read-only` | 只能读文件 |
| `workspace-write` | 读写工作区文件 |
| `danger-full-access` | 危险：完全访问 host |

### Sandbox 模式

非 `main` session 可以配置为在隔离环境中运行：

```json
{
  "agents": {
    "defaults": {
      "sandbox": {
        "mode": "non-main",
        "allow": ["bash", "read", "write"],
        "deny": ["browser", "canvas", "cron"]
      }
    }
  }
}
```

典型 sandbox 默认：
- **允许**：`bash`、`process`、`read`、`write`、`sessions_list`、`sessions_history`、`sessions_send`、`sessions_spawn`
- **拒绝**：`browser`、`canvas`、`nodes`、`cron`、`gateway`

## Docker 沙箱

对于高风险场景，OpenClaw 支持 Docker 隔离执行：

```bash
openclaw gateway --sandbox docker
```

容器内的 session：
- 无法访问 host 文件系统
- 网络访问受限
- 工具权限完全由配置控制

文档：https://docs.openclaw.ai/install/docker

## 安全检查清单

运营前检查：

- [ ] 运行 `openclaw doctor` 检查配置风险
- [ ] 确认 DM policy 是 `pairing`（不是 `open`）
- [ ] 检查 allowlist 配置正确
- [ ] 确认核心工具（browser/canvas）仅在信任的 session 中启用
- [ ] 确认 Gateway 不暴露在公网（除非配置了认证）

## 安全默认值设计

OpenClaw 的安全默认值是**偏保守**的：

1. **DM 默认 pairing**：你需要主动允许才能开放
2. **工具执行需要确认**：危险操作提示确认
3. **Session 隔离**：非 main session 可选 sandbox
4. **日志可追溯**：所有操作有日志

这与"便利性"存在张力，但 OpenClaw 选择安全优先。

## 报告安全问题

发现安全漏洞？请参考：
- https://docs.openclaw.ai/gateway/security
- GitHub Security Advisories

## 给运营者的建议

1. **不要把 Gateway 暴露在公网**（除非你完全理解风险）
2. **定期运行 `openclaw doctor`** 检查配置
3. **DM policy 优先 pairing**，除非你有明确理由 open
4. **订阅安全公告**，及时更新版本
5. **区分运行环境**：开发用 full-access，生产用 sandbox
