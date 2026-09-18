<<<<<<< HEAD
# AudioTune 项目规划

## 一 项目背景

目标岗位主要要求以下能力：

- AI 智能体架构设计与核心模块开发
- 任务理解、工具调用、流程执行和业务自动化
- 训练数据采集、清洗、标注和数据集构建
- SFT、LoRA 等大模型微调技术
- 模型评测、推理性能优化和服务部署
- 知识库、插件、工作流和提示词资产建设
- 多模态应用、语音应用、视频分析和 RAG 系统
- Python、FastAPI、LangChain/LangGraph、Docker 等工程能力

现有 CloudPolicy Wiki 已经覆盖：

- 企业级 Wiki 知识库
- RAG 检索链路
- 权限控制
- 混合检索与语义重排
- AI 流式问答
- 引用溯源
- 检索评测体系
- Java 后端与系统部署

目前主要缺少：

- Python AI 服务
- 训练数据管线
- SFT/LoRA 微调
- 长时间训练任务管理
- 模型训练后的效果评测
- Agent 工具调用和流程编排

因此，下一步不再重复开发普通问答 Agent，而是设计一个能够补足“微调与 AI 工程化”能力的新项目。

## 二 项目定位

项目名称：

> AudioTune｜面向企业语音内容生产的 TTS 微调与智能训练平台

项目定位：

> 将开源 TTS 模型封装成可配置、可评测、可部署的企业语音模型训练平台，支持音频数据清洗、训练集构建、SFT 微调、训练任务管理、模型评测、模型版本管理和推理服务，并通过 Agent 自动编排训练流程。

项目不是重新发明 TTS 模型，而是重点展示：

> 数据处理 → 模型微调 → 效果评测 → 训练任务管理 → 推理部署 → Agent 自动化编排

## 三 目标业务场景

推荐选择：

> 企业语音内容生产与政策播报

可服务于：

- 企业宣传片配音
- 培训课程配音
- 政策解读音频生成
- 客服语音素材生成
- 数字人播报
- 短视频批量配音

与 CloudPolicy Wiki 的联动方式：

```
Wiki 政策文档
    ↓
生成政策解读稿
    ↓
选择播报风格
    ↓
调用 AudioTune 生成音频
    ↓
输出带文档来源的政策播报内容
```

这样两个项目可以形成完整的业务链路，而不是两个相互独立的 Demo。

## 四 技术路线选择

### 首选：Qwen3-TTS

Qwen3-TTS 官方已经提供单说话人微调流程，支持：

- 0.6B 和 1.7B Base 模型
- 音频、文本、参考音频 JSONL 数据格式
- audio codes 预处理
- SFT 训练
- checkpoint 输出
- 微调后推理

第一版优先使用 Qwen3-TTS 作为训练底座，原因是：

- 官方训练文档较完整；
- 数据格式清晰；
- 容易封装为 Python/FastAPI 服务；
- 方便构建标准化训练任务；
- 适合展示数据集构建、SFT、评测和服务化能力。

注意：Qwen3-TTS 当前官方文档提供的是 SFT 流程，并非官方 LoRA 流程。第一阶段先完成标准 SFT，再考虑 PEFT/LoRA 改造。

参考：

- https://github.com/QwenLM/Qwen3-TTS
- https://github.com/QwenLM/Qwen3-TTS/blob/main/finetuning/README.md

### 对照项目：GPT-SoVITS

GPT-SoVITS 适合作为快速效果验证和基线对照，具备：

- 零样本 TTS
- 少样本微调
- 音频切分
- 降噪
- ASR
- 文本标注
- WebUI 训练流程

它适合快速验证“微调前后是否有明显听感差异”，但不作为 AudioTune 的主要工程底座。

参考：

- https://github.com/RVC-Boss/GPT-SoVITS

### 后续方向：CosyVoice

CosyVoice 适合后续扩展：

- 多语言生成
- 跨语言生成
- 指令控制
- 流式推理
- FastAPI/gRPC 部署
- 更复杂的语音应用

但第一版暂不使用 CosyVoice 作为主线，因为其训练流程和 LoRA 改造复杂度更高。

参考：

- https://github.com/FunAudioLLM/CosyVoice

### 研究型方向：Fish Audio S2

Fish Audio S2 是较新的多说话人、可控式 TTS 项目，支持微调代码和流式推理，适合后续研究，但不作为第一版项目基础。

## 五 核心功能

### 1 音频数据管理

支持：

- 音频上传
- 音频格式检查
- 采样率检查
- 时长检查
- 音量和静音比例检测
- 噪声检测
- 爆音和削波检测
- 音频切分
- ASR 转写
- 文本校对
- 训练集和验证集划分

### 2 数据集构建

生成标准化训练文件：

```
{
  "audio": "./data/utt0001.wav",
  "text": "这是用于训练的语音文本。",
  "ref_audio": "./data/ref.wav"
}
```

支持：

- JSONL 生成
- 训练集/验证集划分
- 数据统计
- 无效样本过滤
- 重复样本检测
- 训练数据版本记录

### 3 TTS 微调

第一版实现：

- 基于 Qwen3-TTS Base 模型进行单说话人 SFT；
- 支持训练轮数、学习率、Batch Size 等参数配置；
- 支持 checkpoint 保存；
- 支持训练日志和 Loss 曲线；
- 支持训练失败记录；
- 支持训练结果试听。

后续扩展：

- LoRA/Adapter 参数高效微调；
- 断点续训；
- 多组实验对比；
- 自动选择最佳 checkpoint。

### 4 训练任务管理

训练任务需要设计状态机：

```
CREATED
  ↓
DATA_CHECKING
  ↓
DATA_PREPARING
  ↓
TRAINING
  ↓
EVALUATING
  ↓
SUCCEEDED
```

异常状态：

```
FAILED
CANCELLED
RETRYING
```

支持：

- 创建训练任务
- 查看任务状态
- 查看训练日志
- 取消任务
- 失败重试
- GPU 设备选择
- 训练配置保存
- checkpoint 管理

### 5 模型推理服务

使用 FastAPI 提供：

- 文本转语音接口
- 试听接口
- 批量生成接口
- 模型列表接口
- 模型版本切换接口
- 音频下载接口
- 推理耗时统计

### 6 Agent 流程编排

Agent 不负责重新实现训练逻辑，而是通过工具调用控制训练流程。

工具包括：

```
inspect_audio
clean_audio
build_dataset
prepare_audio_codes
create_training_job
query_training_status
stop_training_job
generate_preview
evaluate_model
publish_model
```

用户请求示例：

> 使用这批音频训练一个适合政策播报的声音，先检查音频质量，自动划分验证集，训练完成后生成三段试听，并输出评测报告。

Agent 执行：

1. 理解用户任务；
2. 检查音频质量；
3. 汇总异常样本；
4. 请求用户确认是否清洗；
5. 构建训练集；
6. 创建 SFT 训练任务；
7. 轮询训练状态；
8. 生成测试音频；
9. 执行效果评测；
10. 输出训练报告；
11. 发布可调用模型。

推荐使用 LangGraph 管理有状态流程，LangChain 负责模型调用和工具封装。

## 六 效果评测体系

评测必须比较：

1. 基础模型零样本生成；
2. 微调模型生成；
3. 人工目标音频。

三组数据使用相同文本和相同参考音频。

### 1 内容准确性

使用 ASR 重新识别生成音频：

- 中文：CER
- 英文：WER

目标是判断是否出现：

- 漏字
- 重复
- 错读
- 发音不清
- 长文本退化

### 2 音色相似度

使用说话人识别模型提取 Embedding，并计算余弦相似度：

```
similarity =
cosine(embedding(generated_audio),
       embedding(target_audio))
```

比较微调前后的 Speaker Similarity 变化。

### 3 自然度

使用人工 MOS 评分：

```
1 分：明显不自然
2 分：问题较多
3 分：基本可接受
4 分：比较自然
5 分：接近真人
```

同时可以进行基础模型与微调模型的双盲偏好测试。

### 4 风格匹配度

如果项目包含温柔、沉稳、正式播报等风格，需要评估：

- 风格分类准确率；
- 平均音高；
- 语速；
- 停顿比例；
- 能量变化；
- 句尾语调；
- 人工风格匹配评分。

### 5 工程性能

记录：

- 首音频延迟 TTFA；
- 实时率 RTF；
- 单段音频生成耗时；
- GPU 显存占用；
- 模型大小；
- 训练耗时；
- 并发生成数量；
- 训练失败率；
- 推理接口成功率。

### 6 最终评测表

```
指标                  基础模型       微调模型       变化
CER                   8.6%          5.1%           -3.5pt
Speaker Similarity    0.71          0.84           +0.13
自然度 MOS            3.42          3.86           +0.44
风格匹配 MOS          3.05          4.02           +0.97
RTF                   0.39          0.44           +0.05
显存占用              8.2GB         8.7GB          +0.5GB
微调版本偏好率        -             68%            -
```

### 第一版建议验收标准

- 测试集 CER 不高于基础模型；
- Speaker Similarity 有稳定提升；
- 自然度 MOS 提升至少 0.3；
- 风格匹配评分提升至少 0.5；
- 微调版本双盲偏好率超过 60%；
- 推理耗时增加不超过 20%；
- 长文本生成不出现明显重复、漏字和中断。

## 七 分阶段实现路线

### 阶段一：基线与可行性验证

目标：确认 TTS 微调是否有实际效果。

任务：

- 安装 Qwen3-TTS；
- 准备本人或获得授权的声音数据；
- 构建最小训练集；
- 完成 Base 模型零样本推理；
- 完成标准 SFT；
- 生成微调后音频；
- 使用同一批测试文本进行对比；
- 记录 CER、音色相似度、试听结果和推理耗时。

交付物：

- 基础模型音频；
- 微调模型音频；
- 训练日志；
- 初步评测表；
- 可行性结论。

### 阶段二：数据处理管线

目标：将手动数据准备流程自动化。

任务：

- 音频格式统一；
- 音频切分；
- ASR 转写；
- 音频质量检测；
- 文本清洗；
- 训练集/验证集划分；
- JSONL 生成；
- 数据集版本记录。

交付物：

- 数据处理 CLI；
- 数据质量报告；
- 标准训练数据集；
- 数据集版本号。

### 阶段三：训练任务服务化

目标：将训练脚本封装为可管理的服务。

任务：

- 设计训练任务表；
- 编写 FastAPI 接口；
- 创建异步训练任务；
- 保存训练配置；
- 监控训练状态；
- 保存训练日志；
- 支持失败重试和任务取消；
- 管理 checkpoint。

交付物：

- FastAPI 服务；
- 训练任务状态机；
- 训练管理接口；
- 模型版本管理功能。

### 阶段四：模型推理与评测服务

目标：完成从模型训练到模型调用的闭环。

任务：

- 封装 TTS 推理接口；
- 生成试听结果；
- 实现自动 CER/WER 评测；
- 实现 Speaker Similarity 评测；
- 生成实验对比报告；
- 记录 TTFA、RTF 和显存占用；
- 支持模型版本切换。

交付物：

- 推理 API；
- 自动评测脚本；
- 评测报告；
- 基础模型与微调模型对比结果。

### 阶段五：Agent 流程编排

目标：体现岗位要求中的 Agent 工具调用和流程执行能力。

任务：

- 使用 LangGraph 定义训练流程；
- 封装音频检查工具；
- 封装数据集构建工具；
- 封装训练任务工具；
- 封装状态查询工具；
- 封装试听和评测工具；
- 增加人工确认节点；
- 增加失败重试和中断恢复。

交付物：

- AudioTune Agent；
- 工具调用日志；
- 流程状态图；
- 多步骤任务演示；
- 失败场景处理报告。

### 阶段六：业务场景联动

目标：把 AudioTune 从训练工具变成业务应用。

推荐场景：

> CloudPolicy Wiki 政策文档自动播报

流程：

```
选择 Wiki 文档
  ↓
提取文档内容
  ↓
生成政策解读稿
  ↓
选择播报风格
  ↓
调用 TTS 模型
  ↓
生成语音
  ↓
保留政策原文引用
  ↓
发布音频内容
```

交付物：

- Wiki 与 AudioTune 联动接口；
- 政策播报页面；
- 带来源引用的音频内容；
- 端到端演示视频。

## 八 简历项目表述

### 项目描述

> AudioTune｜面向企业语音内容生产的 TTS 微调与智能训练平台。基于 Qwen3-TTS 构建音频数据清洗、训练集生成、SFT 微调、异步训练任务管理、模型效果评测、版本管理和推理服务，并使用 LangGraph Agent 自动编排数据检查、训练创建、状态监控、试听生成和模型评测流程。

### 简历亮点

- 设计并实现音频数据处理管线，支持音频质量检测、切分、ASR 转写、文本清洗、训练/验证集划分和 JSONL 数据集生成。
- 基于 Qwen3-TTS Base 模型实现单说话人 SFT 微调，支持训练参数配置、checkpoint 管理、训练日志记录和微调后推理。
- 使用 FastAPI 将训练和推理流程服务化，设计训练任务状态机，支持异步训练、状态查询、失败重试、任务取消和模型版本管理。
- 基于 LangGraph 构建音频训练 Agent，通过工具调用自动完成数据检查、数据集构建、训练任务创建、状态监控、试听生成和效果评测。
- 建立包含 CER/WER、Speaker Similarity、MOS、风格匹配度、RTF、TTFA 和显存占用的模型评测体系，量化分析微调效果与推理成本。

## 九 合规与安全要求

- 只使用本人声音或获得明确授权的音频数据；
- 对生成音频增加 AI 合成标识；
- 记录声音数据来源和授权信息；
- 禁止未经授权复制他人声音；
- 在项目文档中明确模型许可证和数据许可证；
- 不将声音克隆功能包装成可用于冒充他人的工具。

## 十 项目完成后的能力组合

完成 AudioTune 后，项目组合将形成：

### CloudPolicy Wiki

证明：

- RAG
- 企业知识库
- 权限控制
- 检索优化
- 模型评测
- Java 后端
- AI 应用部署

### AudioTune

证明：

- Python/FastAPI
- 数据清洗与数据集构建
- SFT/LoRA 微调
- 训练任务管理
- 模型评测
- 推理服务
- LangGraph Agent
- 工具调用和流程编排

### 医学图像分割科研项目

证明：

- 深度学习
- 计算机视觉
- PyTorch
- 模型训练
- 实验设计
- 论文研究

三者组合可以覆盖目标岗位中大部分核心要求，并形成清晰的职业定位：

> 具备计算机视觉研究基础，能够使用 Python 和 Java 完成大模型应用、RAG 系统、TTS 微调、Agent 编排和 AI 服务工程化落地。
=======
# Qwen3-TTS Easy Finetuning

<p align="center">
  <img src="https://img.shields.io/github/stars/mozi1924/Qwen3-TTS-EasyFinetuning?style=for-the-badge&color=ffd700" alt="GitHub Stars">
  <img src="https://img.shields.io/github/license/mozi1924/Qwen3-TTS-EasyFinetuning?style=for-the-badge&color=blue" alt="License">
  <img src="https://img.shields.io/badge/Python-3.11+-blue?style=for-the-badge&logo=python&logoColor=white" alt="Python 3.11+">
  <img src="https://img.shields.io/badge/PyTorch-2.0+-ee4c2c?style=for-the-badge&logo=pytorch&logoColor=white" alt="PyTorch">
</p>

<p align="center">
  <b>English</b> | <a href="./README_zh.md">简体中文</a>
</p>

An easy-to-use workspace for fine-tuning the **Qwen3-TTS** model. This repository streamlines the entire process—from raw audio ingestion to creating high-stability, expressive custom voice models.

---

### 📚 Tutorial
For a comprehensive step-by-step guide with illustrations, please refer to my article:

👉 [English Article](https://mozi1924.com/article/qwen3-tts-finetuning-en/) | [中文文章](https://mozi1924.com/article/qwen3-tts-finetuning-zh/)

### 🎙️ Why Fine-tuning instead of Zero-shot?

While zero-shot voice cloning is convenient for quick tests, **Supervised Fine-Tuning (SFT)** offers significant advantages for production-grade results:

*   **Timbre Stability**: Fine-tuned models capture the intricate nuances of the target speaker more accurately, ensuring consistent output across diverse text contexts.
*   **Expressive Control**: SFT supports natural language tone and rhythm guidance (e.g., "Speak sadly", "Faster pace"), enabling more emotive and human-like synthesis.
*   **Accent-free Cross-lingual Synthesis**: Effectively prevents "original language accents" during cross-lingual inference (e.g., a Chinese-sounding voice used for English speech will sound like a native English speaker).

---

## ✨ Key Features

*   **Integrated Pipeline**: Automated audio splitting, ASR transcription, dataset cleaning, and tokenization in a single workflow.
*   **Modern WebUI**: A premium Gradio interface for seamless data preparation, training monitoring, and interactive inference.
*   **Robust CLI**: Unified command-line interface for professional automation and remote server management.
*   **Optimized Presets**: Hardcoded, expert-tuned training parameters for different model variants (0.6B / 1.7B).
*   **Docker Ready**: Out-of-the-box environment support via pre-configured Docker images.

---

## 💻 Environment & Requirements

### Host Development Environment
Information about the environment used for developing and testing this project:
- **OS**: Ubuntu 24.04.4 LTS (Kernel 6.17.0-14-generic)
- **CPU**: 2 x Intel(R) Xeon(R) Platinum 8259CL (KVM, 32 cores)
- **Memory**: 32 GB
- **GPU**: 2 x NVIDIA GeForce RTX 3080 (10 GB VRAM each)
- **Driver & CUDA**: NVIDIA Driver 590.48.01 / CUDA 13.1
- **Python**: 3.11.14

### Recommended Training Environment
To ensure stable training and avoid Out-of-Memory (OOM) errors, we recommend:
- **GPU**: NVIDIA GPU with >= 16 GB VRAM (24 GB recommended for 1.7B model)
- **Memory**: >= 32 GB RAM
- **Storage**: SSD with at least 50 GB free space
- **OS**: Linux (Ubuntu 20.04+ recommended)
- **Software**: CUDA 12.4+ (v12.8+ recommended), Python 3.10+

> **⚠️ Special Note for Windows Users (GPU Training):**
> Due to architectural limitations, **do not use Rancher Desktop** if you require GPU support on Windows, as it lacks native Nvidia GPU capabilities. Instead, choose one of the following:
> 1. **Run on a native Linux GPU host** (Highly Recommended: best performance and stability).
> 2. **Use pure WSL2 (Ubuntu)** (Recommended: install native Docker Engine or Python in WSL2 for seamless GPU access).
> 3. **Use Docker Desktop** (Supports GPU, but comes with significant performance overhead and is not guaranteed to be perfectly stable/usable on all Windows configurations).

---

## 🚀 Getting Started

### 1. Installation

**Using Docker (Recommended)**
```bash
# Pull the pre-built image from GHCR (Default)
docker compose up -d

# For users in mainland China, use the Aliyun mirror for faster downloads:
# (Linux)
DOCKER_IMAGE=registry.cn-hangzhou.aliyuncs.com/mozi1924/qwen3-tts-easyfinetuning:latest docker compose up -d

# (Windows PowerShell)
$env:DOCKER_IMAGE="registry.cn-hangzhou.aliyuncs.com/mozi1924/qwen3-tts-easyfinetuning:latest"; docker compose up -d

# Force a local build
docker compose up -d --build
```

**Using Python Virtual Environment**
```bash
# Running this directly in a Windows environment is not actively maintained and is not planned for further maintenance. Please use Docker, which is the fastest, most stable, and most efficient recommended method.

python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# Install Flash Attention matching your CUDA/Torch version
pip install flash-attn==2.8.3 --no-build-isolation
```

### 2. Using the WebUI (Easiest)
Launch the Gradio WebUI to manage the entire lifecycle through your browser:
```bash
python src/webui.py
```
*   **Data Prep**: Upload raw audio -> Split -> ASR -> Tokenize.
*   **Training**: Select dataset -> Configure settings -> Launch Tensorboard -> Start Training.
*   **Inference**: Load your trained checkpoint and test your custom voice!

### 3. Using the CLI (Professional)
The `src/cli.py` serves as a unified entry point for all operations:

**Step A: Prepare Data**
Place your raw `.wav` files in a directory (e.g., `raw-dataset/my_speaker/`).
```bash
python src/cli.py prepare --input_dir raw-dataset/my_speaker --speaker_name my_speaker
```

**Step B: Start Training**
```bash
python src/cli.py train --experiment_name exp1 --speaker_name my_speaker --epochs 3
```

For `CustomVoice` fine-tuning, generate the speaker embedding once after ASR and before training:
```bash
python src/cli.py embed --speaker_name my_speaker --init_model Qwen/Qwen3-TTS-12Hz-0.6B-CustomVoice
python src/cli.py train --experiment_name exp1 --speaker_name my_speaker --init_model Qwen/Qwen3-TTS-12Hz-0.6B-CustomVoice --epochs 3
```

**Step C: Run Inference**
```bash
python src/cli.py infer --checkpoint output/exp1/checkpoint-epoch-2 --speaker my_speaker --text "Hello world! This is my custom voice."
```

---

## 📂 Project Structure

*   `src/webui.py`: Main Gradio interface.
*   `src/cli.py`: Unified command-line interface.
*   `src/sft_12hz.py`: Core fine-tuning logic.
*   `src/step1_audio_split.py`: Audio preprocessing & segmentation.
*   `src/step2_asr_clean.py`: Automatic transcription & labeling.
*   `src/prepare_data.py`: Pre-tokenizing audio into discrete codes (Step 3).

---

## ⚠️ Disclaimer

By using this tool to fine-tune models, you agree that you will not use it for any illegal purposes or to infringe upon the rights of others. The author is not responsible for any direct or indirect consequences arising from the use of this tool, including but not limited to hardware damage or legal disputes.

---

## 🤝 Acknowledgments

*   Built upon [Qwen3-TTS](https://github.com/qwenLM/Qwen3-tts) and [Qwen3-ASR](https://github.com/qwenLM/Qwen3-asr).
*   Training presets inspired by community research (e.g., [rekuenkdr](https://github.com/rekuenkdr)).

---

## ⭐ Star History

[![Star History Chart](https://api.star-history.com/svg?repos=mozi1924/Qwen3-TTS-EasyFinetuning&type=date&legend=top-left)](https://www.star-history.com/#mozi1924/Qwen3-TTS-EasyFinetuning&type=date&legend=top-left)
>>>>>>> qwen3-upstream/master
