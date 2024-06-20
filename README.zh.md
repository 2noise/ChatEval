<div align="center">

[**English**](README.md) | **简体中文**

# ChatTTS音色稳定性评估与识别

本脚本旨在找到稳定的说话者音色。主要代码包括音频生成、稳定性评估两个脚本。

评估基于通义实验室 [ERes2NetV2 说话人识别模型](https://modelscope.cn/models/iic/speech_eres2netv2_sv_zh-cn_16k-common/summary)

</div>

## 文件说明

- `audio_generator.py`: 生成随机说话者并生成测试音频的脚本
- `consistency_evaluator.py`: 评估稳定性的脚本
- `test_data.yaml`: 生成音频的文本数据

## 安装

请确保您已经安装了 ChatTTS 项目并正常运行。[安装步骤](https://github.com/2noise/ChatTTS?tab=readme-ov-file#get-started)

本项目并将 speaker_consistency 文件夹复制到 ChatTTS 项目的根目录下。

复制后的目录类似：
```
├── ChatTTS
│         ├── __init__.py
│         ├── core.py
│         └── 略
└── speaker_consistency
          ├── audio_generator.py
          ├── consistency_evaluator.py
          ├── requirements.txt
          └── test_data.yaml
```
安装依赖
```bash
pip install -r ./speaker_consistency/requirements.txt
```

## 使用方法

### 1. 生成测试音频

运行`audio_generator.py`脚本生成测试音频：

```bash
python speaker_consistency/audio_generator.py
```
### 2. 评估生成音频的稳定性

运行`consistency_evaluator.py`脚本进行评估：

```bash
python speaker_consistency/consistency_evaluator.py
```

评估结果将会保存在`evaluation_results.csv`文件中。

### 3. 数据集

您可以通过编辑`test_data.yaml`文件来修改测试文本。


## 命令行参数说明

### 1. 生成脚本（`audio_generator.py`）

```bash
python audio_generator.py --dir <output_directory> --num <number_of_speakers> --ds <dataset_yaml_file>
```

参数说明：

- `--dir`：存储生成评估音频文件的目录。默认为 `./test_audio`。
- `--num`：生成的随机说话者数目。默认为 `10`。
- `--ds`：数据集YAML文件的路径。默认为 `test_data.yaml`。

### 2. 评估脚本（`consistency_evaluator.py`）

```bash
python consistency_evaluator.py --dir <output_directory>
```

参数说明：

- `--dir`：前一步骤生成音频的目录。默认为 `./test_audio`。

## Rank 指标计算说明

通过计算每对音频片段嵌入向量之间的余弦相似度，得到相似度的均值和标准差。
对标准差进行归一化处理后，`rank` 指标中均值占70%的权重，标准差占30%的权重。
通常`rank` 指标越高，音频片段的一致性越好。

## 输出样本

| id   | ... | rank_TestA | rank_TestB |
|------|-----|------------|------------|
| 0000 | ... | 0.802779   | 0.809263   |
| 0001 | ... | 0.858448   | 0.773149   |
| 0002 | ... | 0.763376   | 0.779981   |

在所有样本集上得分**都高**的说话者音色相对更稳定，可以根据自己的使用场景调整测试集。

## 如何使用说话者音色

说话者音色默认存放在生成音频的目录下 <id> 文件夹中，文件名为 `speaker.pt` 。

您可以通过以下代码加载说话者音色：

```python
spk = torch.load(<PT-FILE-PATH>, map_location=torch.device("cpu"))
params_infer_code = {
    'spk_emb': spk,
}
```

## 局限性

受到样本数量和多样性的影响，评估结果的泛化能力可能会受到限制。

## 相关项目

- [ChatTTS](https://github.com/2noise/ChatTTS)
- [eres2netv2](https://modelscope.cn/models/iic/speech_eres2netv2_sv_zh-cn_16k-common/summary)
