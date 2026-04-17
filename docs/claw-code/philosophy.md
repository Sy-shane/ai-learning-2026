# Claw Code 哲学

> 翻译自 [ultraworkers/claw-code PHILOSOPHY.md](https://github.com/ultraworkers/claw-code/blob/main/PHILOSOPHY.md)，2026-04-17

## 不要只看文件

如果你只看这个仓库生成的文件，那你看的只是错误的层面。

Python 重写是副产品。Rust 重写也是副产品。真正值得研究的是**产生这些产物的系统**：一个基于 ClawHIP 的协调循环，人类给出方向，自主 Claw 执行工作。

Claw Code 不只是一个代码仓库。它是一个公开演示，展示了当以下条件满足时会发生什么：

- 人类提供清晰的方向
- 多个编码 Agent 并行协调
- 通知路由被推出 Agent 上下文窗口
- Planning、Execution、Review 和重试循环被自动化
- 人类**不需要**坐在终端前微观管理每一步

## 真正的人机接口是 Discord

重要的接口不是 tmux、Vim、SSH 或终端复用器。

真正的人机接口是一个 Discord 频道。

一个人可以从手机打一句话，然后走开、睡觉或做其他事情。Claw 读取指令、分解任务、分配角色、写代码、跑测试、争论失败、恢复、推送——所有这些都自主完成。

这就是哲学：**人类设定方向，Claw 执行劳动。**

## 三层系统

### 1. OmX (`oh-my-codex`)
[oh-my-codex](https://github.com/Yeachan-Heo/oh-my-codex) 提供工作流层。

它将简短指令转化为结构化执行：
- Planning 关键词
- 执行模式
- 持久验证循环
- 并行多 Agent 工作流

这是将一句话转化为可重复工作协议的层。

### 2. ClawHIP
[ClawHIP](https://github.com/Yeachan-Heo/clawhip) 是事件和通知路由器。

它监控：
- Git commits
- tmux 会话
- GitHub Issues 和 PRs
- Agent 生命周期事件
- 频道投递

它的职责是保持监控和投递**在编码 Agent 的上下文窗口之外**，让 Agent 保持专注于实现而不是状态格式化和通知路由。

### 3. OmO (`oh-my-openagent`)
[oh-my-openagent](https://github.com/code-yeongyu/oh-my-openagent) 处理多 Agent 协调。

这是跨 Agent 进行 Planning、交接、分歧解决和验证循环发生的地方。

当 Architect、Executor 和 Reviewer 不同意时，OmO 提供结构让这个循环收敛而不是崩溃。

## 真正的瓶颈已经改变

瓶颈不再是打字速度。

当 Agent 系统能在几小时内重建一个代码库时，稀缺资源变成了：
- 架构清晰度
- 任务分解能力
- 判断力
- 产品品味
- 知道什么值得构建的信念
- 知道哪些部分可以并行、哪些部分必须保持约束

快速的 Agent 团队不会消除对思考的需求。它让清晰思考变得更加重要。

## Claw Code 演示了什么

Claw Code 演示了一个仓库可以：
- **在公开环境下自主构建**
- 由 Claw/Lobster 协调而不是仅靠人类结对编程
- 通过聊天接口运作
- 通过结构化 Planning/执行/Review 循环持续改进
- 作为协调层的展示而存在，而不仅仅是输出文件

代码是证据。
协调系统才是产品 lesson。

## 什么仍然重要

随着编码智能变得更便宜、更普及，持久差异化不再是原始编码输出。

仍然重要的是：
- 产品品味
- 方向感
- 系统设计
- 人类信任
- 运营稳定性
- 判断下一步该构建什么

在那个世界里，人类的工作不是比机器更快地打字。
人类的工作是决定什么值得存在。

## 一句话版本

**Claw Code 是一个自主软件开发的演示。**

人类提供方向。
Claw 协调、构建、测试、恢复和推送。
仓库是产物。
哲学是背后的系统。
