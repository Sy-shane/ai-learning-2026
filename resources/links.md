# 关键参考链接

> AI Learning KB 涉及的所有关键项目、文档和资源索引，2026-04-17

---

## Claw Code 生态

### 核心仓库
- **Claw Code**: https://github.com/ultraworkers/claw-code
- **ClawHIP**: https://github.com/Yeachan-Heo/clawhip
- **oh-my-codex**: https://github.com/Yeachan-Heo/oh-my-codex
- **oh-my-openagent**: https://github.com/code-yeongyu/oh-my-openagent
- **oh-my-claudecode**: https://github.com/Yeachan-Heo/oh-my-claudecode
- **oh-my-codex**: https://github.com/Yeachan-Heo/oh-my-codex

### Claw Code Discord
- https://discord.gg/5TUQKqFWd

---

## OpenClaw

### 官方资源
- **GitHub**: https://github.com/openclaw/openclaw
- **文档**: https://docs.openclaw.ai
- **Discord**: https://discord.gg/clawd
- **官网**: https://openclaw.ai
- **ClawHub (技能市场)**: https://clawhub.ai

### 关键文档
- **Vision**: https://github.com/openclaw/openclaw/blob/main/VISION.md
- **Architecture**: https://docs.openclaw.ai/concepts/architecture
- **Security**: https://docs.openclaw.ai/gateway/security
- **Skills**: https://docs.openclaw.ai/tools/skills
- **Channels**: https://docs.openclaw.ai/channels

### 相关工具
- **mcporter**: https://github.com/steipete/mcporter (MCP 桥接)
- **nix-openclaw**: https://github.com/openclaw/nix-openclaw

---

## Claude Code

### 源码参考
- **Claude Code (Anthropic 官方)**: https://github.com/anthropics/claude-code
  - 注意：2025 年初因安全考量撤回，但泄露版本仍在社区流传
- **jarmuine/claude-code (泄露镜像)**: https://github.com/jarmuine/claude-code
  - 1900 文件，512k+ LOC，可借鉴 cost-tracker、bundled skills

### Claude Code 分析
- **Parity 追踪**: Claw Code 的 `PARITY.md` 记录了与 Claude Code 的功能对比

---

## Agent Framework 生态

### Foundation Model Providers
- **Anthropic**: https://anthropic.com (Claude + Tool Use + MCP)
- **OpenAI**: https://openai.com (GPT-4o, o3, Agents SDK)
- **Google DeepMind**: https://deepmind.google (Gemini + Agent Development Kit)
- **DeepSeek**: https://deepseek.com (DeepSeek-R1, 开源推理模型)
- **Qwen**: https://qwenlm.github.io (QwQ-32B, 本地可运行推理)

### Orchestration Frameworks
- **LangGraph**: https://github.com/langchain-ai/langgraph
- **AutoGen**: https://github.com/microsoft/autogen
- **CrewAI**: https://github.com/crewai/crewai
- **SmolAgents**: https://github.com/huggingface/smolagents

### Application Frameworks
- **Vercel AI SDK**: https://github.com/vercel/ai
- **Retool**: https://retool.com

---

## 本地 LLM

### 推理框架
- **llama.cpp**: https://github.com/ggerganov/llama.cpp
- **MLX** (Apple Silicon): https://github.com/ml-explore/mlx
- **vLLM**: https://github.com/vllm-project/vllm
- **llamafile**: https://github.com/Mozilla-Ocho/llamafile
- **Ollama**: https://github.com/ollama/ollama

### 模型分发
- **HuggingFace**: https://huggingface.co
- **GroqCloud**: https://console.groq.com
- **Together AI**: https://together.ai
- **Fireworks AI**: https://fireworks.ai

### 量化格式
- **GGUF**: https://github.com/ggerganov/ggml/blob/master/gguf.md
- **GPTQ**: https://github.com/aut馒头的GPTQ
- **AWQ**: https://github.com/mit-han-lab/llm-awq

---

## 推理模型

### 主要模型
- **Claude Sonnet 4**: https://anthropic.com
- **GPT-o3**: https://openai.com
- **Gemini 2.5 Pro**: https://deepmind.google/gemini
- **DeepSeek-R1**: https://github.com/deepseek-ai/DeepSeek-R1
- **QwQ-32B**: https://qwenlm.github.io

### 基准测试
- **GPQA Diamond**: 化学 PhD 水平测试
- **ARC-AGI**: 推理能力测试
- **MMMU**: 多模态理解
- **AIME**: 数学竞赛

---

## MCP (Model Context Protocol)

- **MCP 官方文档**: https://modelcontextprotocol.io
- **Anthropic MCP**: https://docs.anthropic.com/en/docs/mcp

### 主流 MCP Server
- **Filesystem**: https://github.com/modelcontextprotocol/servers/tree/main/src/filesystem
- **GitHub**: https://github.com/modelcontextprotocol/servers/tree/main/src/github
- **Slack**: https://github.com/modelcontextprotocol/servers/tree/main/src/slack
- **Puppeteer**: https://github.com/modelcontextprotocol/servers/tree/main/src/puppeteer

---

## 学习资源

### 论文
- **Chain-of-Thought**: "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models"
- **ReAct**: "Synergizing Reasoning and Acting in Language Models"
- **Tree of Thoughts**: "Tree of Thoughts: Deliberate Problem Solving with Large Language Models"
- **Process Reward Model**: "Let's Verify Step by Step"

### 博客 & 文章
- **Anthropic Research**: https://www.anthropic.com/research
- **OpenAI Blog**: https://openai.com/blog
- **Google DeepMind**: https://deepmind.google/discover/blog/

---

*最后更新：2026-04-17*
