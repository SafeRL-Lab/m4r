
  
<h1 align="center" style="font-size: 30px;"><strong><em>AccidentBench</em></strong>: Benchmarking Multi-Modal Understanding and Reasoning in Vehicle Accidents and Beyond</h1>



# Content
  - [About the Dataset](#About-the-Dataset)
  - [Dataset Format](#Dataset-Format)
  - [Installation](#Installation)
  - [Download Dataset](#Download-Dataset)
  - [Basic Usage](#Basic-Usage)

 ---





## About the Dataset:
This benchmark includes approximately 2,000 videos and 19,000 human-annotated question-answer pairs, covering a wide range of reasoning tasks (as shown in Figure 1). All annotations were performed by highly educated annotators, each holding a degree in engineering-related fields such as mathematics or computer science. The dataset features a variety of video lengths, categories, and frame counts, and spans three primary safety-critical real-world scenarios: **vehicle accidents**, **ship motion**, and **airplane navigation**. An overview of the dataset’s characteristics is shown in Appendix's Figure 7, which illustrates the distributions of video duration, domain coverage, and reasoning styles. During annotation, we first design the hard-level tasks and label each question with the ground-truth answer. Based on these, we then construct the medium and easy tasks. The primary differences between difficulty levels lie in the number and types of answer choices.

### Dataset Format:

```jsonc
{
  "id": ,
  "dataset": "str",              // e.g., sub dataset filename
  "scene_name": "str",           // e.g., video filename
  "reasoning_style": "str",      // e.g., temporal_reasoning, intent_goal_reasoning, etc.
  "question": "str",             // The reasoning question related to the scene
  "ground_truth": "str",         // Correct answer key (e.g., "A", "B", etc.)
  "options": ["str", "str", "str", "str", "str", "str"]  // Multiple-choice options
}
```

One example from air space:
```jsonc
  {
    "id": 1,
    "dataset": "air_space_long",
    "scene_name": "air_space_long_1.mp4",
    "reasoning_style": "intent_goal_reasoning",
    "question": "How many moving airplanes are observed in this video?",
    "ground_truth": "A",
    "options": [
      "E. [0,1]",
      "C. [8,9]",
      "A. [4,5]",
      "D. [6,7]",
      "B. [10,11]",
      "F. [2,3]"
    ]
  }
```

 <div align=center>
 <img src="./docs/figures/qa-example.png" width="95%"/> 
 </div>
<div align=center>
<center style="color:#000000;text-decoration:underline">Figure 1.  A question and answer example: For each open-space reasoning setting, we include three
types of video lengths: short, medium, and long. Each video length includes tasks designed to
evaluate temporal reasoning, spatial reasoning, and intent reasoning.</center>
 </div>

 

 
## Installation

For development, you can install the package by cloning the repository and running the following command:
```bash
pip install uv
git clone git@github.com:SafeRL-Lab/m4r.git
cd m4r
uv venv dev
source dev/bin/activate
uv pip install -e .
uv pip install -U "qwen-vl-utils"   
```

<!-- ```bash
uv venv -p python3.11.5 dev311
source dev311/bin/activate
uv pip install -e .
``` -->



## Download Dataset

You can download the dataset directly from our [Hugging Face repository](https://huggingface.co/datasets/Accident-Bench/Dataset).




## Basic Usage

Here's a basic evaluation example:


> Download the dataset from [Hugging Face](https://huggingface.co/datasets/Accident-Bench/Dataset), and set the dataset path to the corresponding task file. For example, specify the dataset path as `/your-dataset-path/land_space/short/hard/spatial_reasoning.json` in the task configuration file located at `/Open-Space-Reasoning/lmms_eval/tasks/land_space_short/land_space_hard.yaml`.


```bash
accelerate launch --num_processes=1 --main_process_port=12346 -m lmms_eval \
        --model qwen2_5_vl \
        --model_args=pretrained=Qwen/Qwen2.5-VL-7B-Instruct,max_pixels=12845056,use_flash_attention_2=False,interleave_visuals=True \
        --tasks land_space_hard \
        --batch_size 1 \
        --log_samples \
        --output_path /pasteur2/u/xhanwang/lmms-eval/outputs/land_space_hard/
```

Modify the following examples to test more models as the above script.
> More examples can be found in [examples/models](examples/models)

**Evaluation of OpenAI-Compatible Model**

```bash
bash examples/models/openai_compatible.sh
bash examples/models/xai_grok.sh
```

**Evaluation of vLLM**

```bash
bash examples/models/vllm_qwen2vl.sh
```

**Evaluation of LLaVA-OneVision**

```bash
bash examples/models/llava_onevision.sh
```

**Evaluation of LLaMA-3.2-Vision**

```bash
bash examples/models/llama_vision.sh
```

**Evaluation of Qwen2-VL**

```bash
bash examples/models/qwen2_vl.sh
bash examples/models/qwen2_5_vl.sh
```

**Evaluation of LLaVA on MME**

If you want to test LLaVA 1.5, you will have to clone their repo from [LLaVA](https://github.com/haotian-liu/LLaVA) and

```bash
bash examples/models/llava_next.sh
```

**Evaluation with tensor parallel for bigger model (llava-next-72b)**

```bash
bash examples/models/tensor_parallel.sh
```

**Evaluation with SGLang for bigger model (llava-next-72b)**

```bash
bash examples/models/sglang.sh
```

**Evaluation with vLLM for bigger model (llava-next-72b)**

```bash
bash examples/models/vllm_qwen2vl.sh
```

**More Parameters**

```bash
python3 -m lmms_eval --help
```

**Environmental Variables**
Before running experiments and evaluations, we recommend you to export following environment variables to your environment. Some are necessary for certain tasks to run.

```bash
export OPENAI_API_KEY="<YOUR_API_KEY>"
export HF_HOME="<Path to HF cache>" 
export HF_TOKEN="<YOUR_API_KEY>"
export HF_HUB_ENABLE_HF_TRANSFER="1"
export REKA_API_KEY="<YOUR_API_KEY>"
# Other possible environment variables include 
# ANTHROPIC_API_KEY,DASHSCOPE_API_KEY etc.
```

**Common Environment Issues**

Sometimes you might encounter some common issues for example error related to httpx or protobuf. To solve these issues, you can first try

```bash
python3 -m pip install httpx==0.23.3;
python3 -m pip install protobuf==3.20;
# If you are using numpy==2.x, sometimes may causing errors
python3 -m pip install numpy==1.26;
# Someties sentencepiece are required for tokenizer to work
python3 -m pip install sentencepiece;
```











