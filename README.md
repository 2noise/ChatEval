<div align="center">

**English** | [**简体中文**](README.zh.md)

# Identify speakers with stable voice timbre

This script aims to identify speakers with stable voice timbre. 

The evaluation is based on the Institute for Intelligent Computing's [ERes2NetV2 speaker recognition model](https://modelscope.cn/models/iic/speech_eres2netv2_sv_zh-cn_16k-common/summary).

</div>

## File Description

- `audio_generator.py`: Script to generate random speakers and create test audio
- `consistency_evaluator.py`: Script to evaluate stability
- `test_data.yaml`: Text data for generating audio

## Installation

Please ensure you have installed the ChatTTS project and it is running correctly. [Installation steps](https://github.com/2noise/ChatTTS?tab=readme-ov-file#get-started)

Clone this project and copy the speaker_consistency folder to the root directory of the ChatTTS project.

The directory should look like this:
```
├── ChatTTS
│         ├── __init__.py
│         ├── core.py
│         └── ...
└── speaker_consistency
          ├── audio_generator.py
          ├── consistency_evaluator.py
          ├── requirements.txt
          └── test_data.yaml
```
Install dependencies:
```bash
pip install -r ./speaker_consistency/requirements.txt
```

## Usage

### 1. Generate Test Audio

Run the `audio_generator.py` script to generate test audio:

```bash
python speaker_consistency/audio_generator.py
```
### 2. Evaluate the Stability of the Generated Audio

Run the `consistency_evaluator.py` script to perform the evaluation:

```bash
python speaker_consistency/consistency_evaluator.py
```

The evaluation results will be saved in the `evaluation_results.csv` file.

### 3. Dataset

You can edit the `test_data.yaml` file to modify the test text.

## Command Line Parameters

### 1. Generation Script (`audio_generator.py`)

```bash
python audio_generator.py --dir <output_directory> --num <number_of_speakers> --ds <dataset_yaml_file>
```

Parameter Description:

- `--dir`: Directory to store the generated test audio files. Default is `./test_audio`.
- `--num`: Number of random speakers to generate. Default is `10`.
- `--ds`: Path to the dataset YAML file. Default is `test_data.yaml`.

### 2. Evaluation Script (`consistency_evaluator.py`)

```bash
python consistency_evaluator.py --dir <output_directory>
```

Parameter Description:

- `--dir`: Directory containing the audio files generated in the previous step. Default is `./test_audio`.

## Rank Metric Calculation

The cosine similarity between the embedding vectors of each pair of audio segments is calculated to obtain the mean and standard deviation of the similarity. After normalizing the standard deviation, the `rank` metric assigns a weight of 70% to the mean and 30% to the standard deviation. Generally, the higher the `rank` metric, the better the consistency of the audio segments.

## Sample Output

| id   | ... | rank_TestA | rank_TestB |
|------|-----|------------|------------|
| 0000 | ... | 0.802779   | 0.809263   |
| 0001 | ... | 0.858448   | 0.773149   |
| 0002 | ... | 0.763376   | 0.779981   |

Speakers with consistently high scores across all sample sets have relatively stable voice timbre. You can adjust the test set according to your specific use case.

## How to Use Speaker Timbre

The speaker timbre is stored by default in the directory of the generated audio under the <id> folder, with the filename `speaker.pt`.

You can load the speaker timbre with the following code:

```python
spk = torch.load(<PT-FILE-PATH>, map_location=torch.device("cpu"))
params_infer_code = {
    'spk_emb': spk,
}
```

## Limitations

The evaluation results may be limited by the quantity and diversity of the samples.

## Related Projects

- [ChatTTS](https://github.com/2noise/ChatTTS)
- [eres2netv2](https://modelscope.cn/models/iic/speech_eres2netv2_sv_zh-cn_16k-common/summary)
