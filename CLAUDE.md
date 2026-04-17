# CLAUDE.md — AI Agent 上下文

本文件为 AI Agent 提供项目上下文。每次对话开始时，Agent 应阅读此文件。

## 项目概述

`ai-learning-2026` 是一个持续更新的 AI 学习知识库，聚焦三大方向：
1. **Agent 设计理念**：Claw Code、OpenClaw、Claude Code 的设计哲学与架构
2. **2026 前沿方向**：Agent Framework、多智能体、本地 LLM、推理模型
3. **工具生态**：从 Claude Code 源码泄露事件中学习工程实践

## 关键项目参考

### Claw Code (ultraworkers/claw-code)
- Rust 重写版 CLI Agent harness，185k+ stars
- 三层协调系统：OmX（工作流层）+ ClawHIP（事件路由）+ OmO（多 Agent 协调）
- 核心洞察：Discord 是真正的人机接口，人类负责方向，Claw 负责执行
- GitHub: https://github.com/ultraworkers/claw-code

### OpenClaw (openclaw/openclaw)
- 个人 AI 助手框架，TypeScript 实现
- Gateway 架构：Session / Channel / Tools 三层解耦
- 支持 20+ 消息渠道，隐私优先，本地执行
- GitHub: https://github.com/openclaw/openclaw

### Claude Code Reference
- Anthropic 官方 CLI 工具，Python + Node.js 实现
- 核心模块：cost-tracker、bundled skills、remember skill、task system
- 参考价值：记忆系统设计、cost tracking、技能bundling 策略

## 当前学习重点

- [ ] 完成 Claw Code 哲学文档
- [ ] 完成 OpenClaw 架构解析
- [ ] 完成 2026 AI 趋势综述
- [ ] 补充 Claude Code 源码分析
- [ ] 添加每周自动更新 workflow

## 写作风格指南

1. **结论先行**：首句必须包含核心结论
2. **受众适配**：技术实现 vs 战略洞察 内容分层
3. **可扫描性**：长段落用多级标题拆解
4. **引用原始**：关键引述标注来源，不过度解读

## 关于仓库维护

- 使用 GitHub Actions 每周自动更新热门项目动态
- PR Welcome：发现错误或有补充，直接提 PR
- 讨论：使用 GitHub Issues 进行主题讨论
