# 2026 年 TTS 微调开源项目检索

检索时间：2026-09-18

目标：寻找适合 AudioTune 二次开发的 TTS 开源底座，重点关注官方是否提供微调流程、代码是否允许修改，以及模型权重和训练数据是否存在额外限制。

## 结论

首选 **Qwen3-TTS**。它在 2026 年 1 月发布，官方仓库已经提供 `0.6B/1.7B Base` 模型和单说话人 SFT 微调流程，训练数据格式、预处理和 checkpoint 输出都比较清晰，最适合直接作为 AudioTune 的第一版训练底座。

第二选择是 **GPT-SoVITS**，适合快速验证“少量个人声音数据能否明显改善音色相似度”，并且自带音频切分、ASR、标注和 WebUI 微调流程。但它更像一个完整的应用型 WebUI，不如 Qwen3-TTS 适合拆成标准化训练任务服务。

后续扩展可以考虑 **CosyVoice**。它的代码采用 Apache-2.0，覆盖多语言、跨语言、流式和部署能力；但工程复杂度更高，适合 AudioTune 第二阶段或作为对照底座。

## 候选项目对比

| 项目 | 2026 活跃/更新证据 | 微调能力 | 代码许可 | 主要风险 | 建议 |
|---|---|---|---|---|---|
| [Qwen3-TTS](https://github.com/QwenLM/Qwen3-TTS) | 官方 README 标注 2026-01-22 发布 | 官方提供 0.6B/1.7B Base 单说话人 SFT；JSONL 包含 audio/text/ref_audio | Apache-2.0 | 当前官方微调文档主要覆盖单说话人 SFT，多说话人和高级微调尚未开放 | **AudioTune 第一版首选** |
| [GPT-SoVITS](https://github.com/RVC-Boss/GPT-SoVITS) | 当前 README 已覆盖 PyTorch 2.7/2.8、CUDA 12.8 等环境 | 少样本微调，官方称约 1 分钟数据即可做 few-shot；有 WebUI 数据处理和训练流程 | MIT（仓库代码） | 预训练模型、G2PW、UVR5 等外部权重需分别核查许可证 | 快速效果验证/基线 |
| [CosyVoice](https://github.com/FunAudioLLM/CosyVoice) | 官方仓库持续维护，GitHub 标注 Apache-2.0 | 仓库定位包含 training、fine-tuning、deployment | Apache-2.0（代码） | 模型权重、数据集和依赖组件要单独核查；训练栈较复杂 | 第二阶段多语言/流式扩展 |
| [F5-TTS](https://github.com/SWivid/F5-TTS) | 2026 年仍有 issue/PR 活动，官方提供 finetune CLI | 官方提供 Accelerate/Gradio 微调流程，代码可直接改 | MIT（代码） | 官方 Emilia 训练底模为 CC-BY-NC；维护者明确说明微调后仍不能商业使用 | 研究型对照，不作为商业底座 |
| [IndexTTS-2](https://github.com/index-tts/index-tts) | 仓库仍在维护 | 允许将模型重新训练、微调、LoRA 等定义为 Derivative Work | 自定义 bilibili Model Use License | 不是标准 Apache-2.0 模型授权；超大用户/高收入主体需另行取得许可，且有用途和分发义务 | 可研究，不建议首版采用 |
| [Fish Speech](https://github.com/fishaudio/fish-speech) | LICENSE 于 2026-03-07 更新 | 自定义许可证明确覆盖 fine-tune 和 LoRA 衍生模型 | Fish Audio Research License | 商业使用必须另签书面许可；研究/非商业可用但有署名和展示要求 | 非商业研究备选 |

## 许可证判断

“允许二次开发”必须拆成三层判断：

1. **仓库代码**能否修改和再分发；
2. **预训练权重**能否用于微调、部署和商业使用；
3. **训练数据来源**是否把限制传递给权重和下游微调模型。

因此，不能只看到仓库首页的 MIT/Apache-2.0 就认定整个模型可商业使用。例如 F5-TTS 的代码是 MIT，但官方底模受 Emilia 数据的 CC-BY-NC 影响，官方维护者明确回答：基于该底模微调后仍不能商业使用。

Qwen3-TTS 的仓库代码和模型入口标注 Apache-2.0，且官方直接提供 Base 微调脚本，是当前候选中最符合“可二次开发 + 有明确微调入口”的项目。不过在正式商用前，仍应固定下载版本并核对对应 Hugging Face/ModelScope 模型卡、第三方依赖和数据集授权。

## 对 AudioTune 的推荐路线

### 第一版

- 底座：Qwen3-TTS-12Hz-0.6B-Base；显存和训练成本更低。
- 对照：Qwen3-TTS-12Hz-1.7B-Base。
- 训练方式：先实现官方单说话人 SFT，不立即改造 LoRA。
- 数据格式：统一生成 `audio/text/ref_audio` JSONL。
- 平台边界：将官方 `prepare -> SFT -> checkpoint -> inference` 封装成训练任务状态机。

### 第二版

- 增加 GPT-SoVITS 作为少样本基线。
- 增加 CER/WER、speaker similarity、RTF、显存和试听样本对比。
- 抽象模型适配器，让 Qwen3-TTS 与 GPT-SoVITS 使用同一套数据集、任务、评测和模型版本接口。

### 第三版

- 评估 CosyVoice 的多语言、跨语言和流式能力。
- 再考虑 LoRA/PEFT、多说话人训练和分布式训练。

## 个人/社区作者完成的平台型项目

这类项目不一定拥有大公司的模型，但更接近“个人把训练流程做成工具”的形态，值得直接借鉴 AudioTune 的产品结构。

### 1. [Qwen3-TTS-EasyFinetuning](https://github.com/mozi1924/Qwen3-TTS-EasyFinetuning)

目前最接近 AudioTune 定位的个人项目。作者将 Qwen3-TTS 封装成完整工作流：音频切分、ASR 转写、数据清洗、tokenization、Gradio WebUI、训练监控、CLI、Docker 和微调后推理。项目还提供 0.6B/1.7B 训练预设，仓库标注 Apache-2.0。

它可以作为 AudioTune 的“竞品参考/最小原型”，但目前更偏单机工具，还没有完整的数据库任务状态、权限、模型版本治理和可复现实验管理。

### 2. [f5-tts-lora-finetuning](https://github.com/instavar/f5-tts-lora-finetuning)

社区作者在 F5-TTS 上增加了 PEFT/LoRA 微调、Gradio 推理、Docker、评测脚本、训练日志和断点恢复约定。README 给出了 LoRA 参数、适配器保存、恢复和评测流程，工程化程度明显高于普通训练脚本。

适合借鉴 AudioTune 的：

- Adapter/Checkpoint 管理；
- 固定数据集划分和评测协议；
- 训练中断后的恢复；
- 基础模型与 LoRA 适配器的分离。

注意：它仍然继承 F5-TTS 官方底模的 CC-BY-NC 权重限制，不能因为 LoRA 代码是 MIT 就直接用于商业模型。

### 3. [tts-forge](https://github.com/thechandanbhagat/tts-forge)

个人完成的 Windows 端 TTS 训练管线，覆盖录音、音频质量检查、归一化、重采样、静音裁剪、降噪、训练/验证集划分、XTTS v2/VITS 微调、TensorBoard 和批量推理。项目代码采用 MIT，但底层 Coqui TTS 还需要遵守 MPL-2.0。

它更像“面向个人用户的一键训练工具”，适合参考音频采集、数据集向导和低显存训练体验，不适合直接作为企业级后端架构。

### 4. [CosyVoice LoRA Finetune Framework](https://github.com/leeoisaboy/cosyvoice-lora-finetune-framework)

社区作者实现了 CosyVoice 的 LLM+Flow 联合训练、LoRA、数据准备、权重合并和无 Prompt 推理，仓库标注 MIT，同时声明需要遵守 CosyVoice 和 Matcha-TTS 原始协议。

适合研究模型适配器和联合训练，但仓库规模较小，不能等同于成熟平台。

### 5. [VoicePipeline](https://github.com/fcttechnologies/VoicePipeline)

这是一个围绕 F5-TTS 的端到端流水线：采集/下载授权音频、归一化、带词级时间戳的转写、说话人片段提取、数据集生成和本地微调。它强调每个阶段可恢复、幂等和状态驱动，并支持只构建数据集或只训练。

虽然作者组织属性不完全属于个人项目，但其设计很适合借鉴 AudioTune 的数据管线和任务状态机。

### 6. [qwen3-tts-webui](https://github.com/rodlunt/qwen3-tts-webui)

个人维护的 Qwen3-TTS 自托管 WebUI，使用 React + FastAPI + Docker，提供本地语音克隆、CustomVoice、VoiceDesign 和移动端界面。它暂时主要负责推理，不负责微调，但可作为 AudioTune 推理前端的参考。

## 个人项目的实际价值判断

最值得组合借鉴的是：

```text
Qwen3-TTS-EasyFinetuning  -> 数据准备 + WebUI + 训练入口
f5-tts-lora-finetuning    -> LoRA + 评测 + checkpoint/adapter 管理
tts-forge                 -> 录音向导 + Windows/低显存体验
VoicePipeline             -> 可恢复、幂等、状态驱动的数据流水线
qwen3-tts-webui           -> React/FastAPI/Docker 推理界面
```

因此，AudioTune 不必从零复制一个“大而全平台”。更合理的差异化方向是：吸收这些个人项目的实用流程，再补上它们普遍缺少的训练任务状态机、数据集版本、实验对比、评测报告、模型注册和 Agent 编排。

## 一手来源

- [Qwen3-TTS README](https://github.com/QwenLM/Qwen3-TTS)
- [Qwen3-TTS 官方微调说明](https://github.com/QwenLM/Qwen3-TTS/blob/main/finetuning/README.md)
- [GPT-SoVITS README](https://github.com/RVC-Boss/GPT-SoVITS)
- [GPT-SoVITS LICENSE](https://github.com/RVC-Boss/GPT-SoVITS/blob/main/LICENSE)
- [CosyVoice README](https://github.com/FunAudioLLM/CosyVoice)
- [F5-TTS 微调脚本](https://github.com/SWivid/F5-TTS/blob/main/src/f5_tts/train/finetune_cli.py)
- [F5-TTS 许可证说明与维护者答复](https://github.com/SWivid/F5-TTS/discussions/997)
- [IndexTTS-2 LICENSE](https://github.com/index-tts/index-tts/blob/main/LICENSE)
- [Fish Audio Research License](https://github.com/fishaudio/fish-speech/blob/main/LICENSE)
