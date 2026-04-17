# OpenClaw Skills 系统

> Skill 架构、触发机制和使用指南，2026-04-17

## 什么是 Skill

Skill 是 OpenClaw 的功能扩展单元。每个 Skill 是一个带有 `SKILL.md` 的独立包，定义了：
- **触发词**：什么条件下激活这个 Skill
- **功能描述**：Skill 做什么
- **使用说明**：如何正确使用

```
skill-name/
├── SKILL.md       ← Skill 定义文件
├── scripts/       ← 可选脚本
└── references/   ← 可选参考文档
```

## Skill 触发机制

### 关键词触发

SKILL.md 中的 `description` 字段用于语义匹配。当用户消息匹配描述时，对应 Skill 被激活。

示例（飞书相关 Skill）：
```markdown
# Skill: feishu-bitable

**触发词**：飞书多维表格、Bitable、创建数据表、查询记录
```

### 显式调用

通过 `/skills` 命令列出所有技能，或直接调用：
```
/skills list
/skills install <path>
```

## 内置 Skills（部分）

| Skill | 触发词 | 功能 |
|---|---|---|
| `feishu-bitable` | 多维表格、Bitable | 飞书多维表格增删改查 |
| `feishu-calendar` | 日历、日程 | 飞书日历和日程管理 |
| `feishu-im-read` | 聊天记录、消息搜索 | 读取飞书消息历史 |
| `feishu-task` | 任务、待办 | 飞书任务管理 |
| `feishu-create-doc` | 创建文档 | 飞书云文档创建 |
| `weather` | 天气、气温 | 天气查询 |
| `healthcheck` | 安全检查、审计 | 系统安全检查 |

完整列表：https://clawhub.ai

## Skill 开发

### SKILL.md 格式

```markdown
# Skill: my-skill

**触发词**：关键词1 / 关键词2 / 关键词3

## 功能
描述这个技能做什么。

## 使用限制
任何约束或注意事项。

## 示例
使用示例。
```

### Skill 发现机制

OpenClaw 在以下位置查找 Skills：
- **内置 Skills**：`~/.openclaw/skills/`
- **扩展 Skills**：`~/.openclaw/extensions/*/skills/`
- **Workspace Skills**：`~/.openclaw/workspace/skills/`
- **ClawHub**：在线技能市场

## Skill vs 插件

| 维度 | Skill | Plugin |
|---|---|---|
| 复杂度 | 轻量（SKILL.md + 脚本） | 重量（npm 包） |
| 安装 | 复制文件夹 或 ClawHub | npm install |
| 更新 | 手动 | 包管理器 |
| 权限 | 通常是只读工具 | 可以扩展任何功能 |

## ClawHub

**https://clawhub.ai** 是 OpenClaw 的官方技能市场。

发布流程：
1. 在独立仓库开发 Skill
2. 测试完整
3. 提交到 ClawHub（需审核）
4. 用户通过 `/skills install <path>` 安装

**核心添加门槛很高**：新功能需要强烈的产品和安全理由才能进入核心，而不是作为 Skill。

## 最佳实践

1. **优先 Skill 再插件**：新功能先用 Skill 验证需求，再考虑插件化
2. **Skill 保持单一职责**：一个 Skill 做一件事
3. **描述要语义丰富**：方便 AI 准确匹配触发
4. **本地测试**：Skill 开发后在本地上机测试再发布
