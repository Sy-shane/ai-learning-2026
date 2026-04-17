# 本地 LLM 与端侧推理

> 隐私、成本与主权：2026 本地/端侧 AI 部署趋势，2026-04-17

## 为什么本地 LLM 在 2026 爆发

### 隐私驱动

- **数据不出设备**：企业合规要求（GDPR、医疗、金融）
- **用户主权**：用户不想把对话数据交给第三方
- **敏感场景**：医疗记录、法律文档、财务数据

### 成本驱动

- **API 成本累积**：大量调用时，API 成本远超本地部署
- **离线场景**：网络不可用时仍需 AI 能力
- **无限使用**：本地推理没有 token 限制

### 技术成熟

- **量化技术**：GPTQ/AWQ/GGUF 量化，模型体积缩小 4-8x
- **硬件进步**：Apple M4、NV NIM、本地 GPU 服务器
- **推理优化**：vLLM、llama.cpp、MLX 的性能提升

## 2026 本地 LLM 生态

### 推理框架

| 框架 | 平台 | 特点 |
|---|---|---|
| **llama.cpp** | 全平台 | 最广泛支持，GGUF 格式 |
| **MLX** | Apple Silicon | Apple 官方，Metal 加速 |
| **vLLM** | Linux + NVIDIA | PagedAttention，高吞吐 |
| **llamafile** | 单文件 | 零依赖，跨平台 |
| **Ollama** | 全平台 | 简化部署，docker one-click |

### 量化级别

| 格式 | 精度 | 34B 模型大小 | 适用场景 |
|---|---|---|---|
| FP16 | 16位浮点 | ~68GB | 最高精度，本地研究 |
| Q8 | 8位整数 | ~34GB | 平衡模式 |
| Q5_K_M | 5位 | ~22GB | 推荐选择 |
| Q4_K_M | 4位 | ~19GB | 大多数 M 系列 Mac |
| Q3_K_M | 3位 | ~15GB | 低内存设备 |
| Q2_K | 2位 | ~13GB | 极低内存 |

### 托管平台

- **Ollama Cloud**：Ollama 官方托管
- **GroqCloud**：超快推理（Llama 3.1 8B 达到 800 tok/s）
- **Together AI**：开源模型集合
- **Fireworks AI**：高效推理 API
- **Anthropic via API**：Claude 非本地选项

## Apple Silicon 的独特优势

Apple Silicon (M1-M4) 的统一内存架构让本地推理有独特优势：

- **MLX 框架**：Apple 官方机器学习优化
- **Metal 加速**：GPU 级别的图形渲染器加速推理
- **统一内存**：CPU/GPU 共享内存，没有传输带宽瓶颈
- **能效比**：比 NVIDIA GPU 更高效的瓦特/推理量

典型 M4 Mac 配置下：
- **M4 Max (128GB)**：可运行 70B Q4 模型
- **M4 Pro (64GB)**：可运行 34B Q4 模型
- **M4 (24GB)**：可运行 7B Q4 模型

## 本地 LLM + OpenClaw

OpenClaw 的架构天然适合本地 LLM：

```
OpenClaw Gateway
    + 
本地 Ollama/llama.cpp
    +
 Whisper (语音识别, 本地)
    +
 TTS (本地可选)
```

配置示例：
```bash
export OPENAI_API_KEY="ignored"
export OPENAI_BASE_URL="http://localhost:11434/v1"
export OPENAI_API_KEY="local-dev-token"

# 模型：llama3.1 8B 或 qwen2.5-coder
```

## 隐私计算场景

### 完全本地（最高隐私）
- Model：本地量化模型
- TTS/STT：本地
- 数据：永不离开设备

### 本地 + 受信任云（平衡）
- Model：本地或受信任云
- STT：本地（Whisper）
- 敏感数据：本地处理

### 全云（最高能力）
- 所有模型：API 调用
- 数据处理在云端
- 依赖网络

## 趋势判断

1. **Apple Silicon 会成为个人 AI 的标准硬件**：隐私 + 能效 + 统一内存
2. **GGUF 格式会是本地模型分发标准**：Ollama + llamafile 都支持
3. **Ollama 是本地部署最佳起点**：one-line setup
4. **本地 LLM 不是替代云端，而是补充**：复杂推理用云端，简单任务用本地
5. **2026 年的本地模型能力**：追上 GPT-3.5 水平的开源模型会越来越多
