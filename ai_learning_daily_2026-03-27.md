# AI 知识学习日报

> 每天学习时间：30分钟 | 模式：20分钟大模型应用 + 10分钟机器学习基础 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-27 周四 - 第4天

---

### 🎯 今日学习目标

| 部分 | 时间 | 主题 |
|------|------|------|
| 🕐 20分钟 | **大模型应用** | Fine-tuning 微调 |
| 🕐 10分钟 | **机器学习基础** | 计算机视觉基础 |

---

## 📖 第一部分：20分钟 - Fine-tuning 微调

---

### 1. 什么是 Fine-tuning？

**Fine-tuning = 微调**

**定义**：在预训练模型的基础上，用特定领域的数据继续训练

**类比**：
```
预训练模型 = 大学毕业生（通识知识）
微调 = 专业培训（领域知识）

结果 = 某领域的专家
```

---

### 2. 为什么需要 Fine-tuning？

| 预训练模型 | 微调后 |
|-----------|--------|
| 通用知识 | 领域知识 |
| 可能不懂行业术语 | 掌握行业术语 |
| 输出风格通用 | 符合企业/品牌风格 |
| 任务泛化 | 任务专精 |

---

### 3. Fine-tuning vs RAG

| 维度 | Fine-tuning | RAG |
|------|------------|-----|
| **知识更新** | 需要重新训练 | 添加文档即可 |
| **成本** | 高（GPU、时间） | 低 |
| **适用场景** | 特定格式输出 | 知识问答 |
| **能力提升** | 改变模型能力 | 提供外部知识 |
| **结果** | 模型永久改变 | 不改变模型 |

---

### 4. Fine-tuning 的类型

#### 1. 全量微调（Full Fine-tuning）

更新模型的所有参数

```
预训练模型（100亿参数）
    ↓
用领域数据训练
    ↓
所有参数都更新
```

**优点**：效果最好
**缺点**：成本高、需要大量数据

---

#### 2. 部分微调（Partial Fine-tuning）

只更新部分层

```
预训练模型
├── 底层层（通用特征）❌ 不更新
├── 中间层 ⚠️ 选择性更新
└── 顶层（任务特定）✅ 全部更新
```

**优点**：节省计算
**缺点**：效果略差

---

#### 3. PEFT（参数高效微调）

只更新少量参数

| 方法 | 说明 | 参数量 |
|------|------|--------|
| **LoRA** | 低秩适配 | 1% |
| **Prefix Tuning** | 前缀微调 | 0.1% |
| **Prompt Tuning** | 提示微调 | 0.01% |

---

### 5. LoRA（Low-Rank Adaptation）

**原理**：在原有权重上增加低秩矩阵

```
原权重 W
    ↓
W + A·B
    ↓
A·B 是低秩矩阵（参数少）
```

**优势**：
- 只训练 0.1-1% 的参数
- 效果接近全量微调
- 可合并到原模型

**代码示例**：

```python
from peft import LoraConfig, get_peft_model
from transformers import AutoModelForCausalLM

# 加载预训练模型
model = AutoModelForCausalLM.from_pretrained("gpt2")

# 配置 LoRA
lora_config = LoraConfig(
    r=16,  # 秩
    lora_alpha=32,
    target_modules=["q_proj", "v_proj"],  # 目标模块
    lora_dropout=0.05,
    bias="none"
)

# 应用 LoRA
model = get_peft_model(model, lora_config)

# 查看可训练参数
model.print_trainable_parameters()
# 输出：
# trainable params: 0.24% || all params: 124M || trainable%: 0.24%
```

---

### 6. Fine-tuning 流程

```
步骤1: 准备数据
        ↓
步骤2: 数据预处理
        ↓
步骤3: 配置模型
        ↓
步骤4: 训练
        ↓
步骤5: 评估
        ↓
步骤6: 保存模型
```

---

### 7. 准备数据

#### 数据格式

```python
# 格式1：对话格式（适合聊天）
training_data = [
    {
        "messages": [
            {"role": "system", "content": "你是一个 Java 代码助手。"},
            {"role": "user", "content": "如何实现单例模式？"},
            {"role": "assistant", "content": "单例模式有几种实现方式..."}
        ]
    },
    {
        "messages": [
            {"role": "user", "content": "什么是设计模式？"},
            {"role": "assistant", "content": "设计模式是解决常见软件设计问题的最佳实践..."}
        ]
    }
]

# 格式2：补全格式（适合文本生成）
training_data = [
    {
        "prompt": "写一个冒泡排序函数：",
        "completion": "def bubble_sort(arr):\n    n = len(arr)\n    ..."
    },
    {
        "prompt": "解释什么是递归：",
        "completion": "递归是指函数调用自身..."
    }
]
```

---

### 8. 完整 Fine-tuning 示例

#### 使用 Hugging Face

```python
from transformers import AutoModelForCausalLM, AutoTokenizer, TrainingArguments, Trainer
from datasets import Dataset
import torch

# 1. 加载模型和 tokenizer
model_name = "gpt2"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForCausalLM.from_pretrained(model_name)

# 设置 pad_token
tokenizer.pad_token = tokenizer.eos_token

# 2. 准备数据
data = [
    {"input": "写一个冒泡排序", "output": "def bubble_sort(arr):\n    n = len(arr)\n    for i in range(n):\n        for j in range(0, n-i-1):\n            if arr[j] > arr[j+1]:\n                arr[j], arr[j+1] = arr[j+1], arr[j]\n    return arr"},
    {"input": "写一个快速排序", "output": "def quick_sort(arr):\n    if len(arr) <= 1:\n        return arr\n    pivot = arr[len(arr) // 2]\n    left = [x for x in arr if x < pivot]\n    middle = [x for x in arr if x == pivot]\n    right = [x for x in arr if x > pivot]\n    return quick_sort(left) + middle + quick_sort(right)"}
]

# 3. 数据预处理
def preprocess(examples):
    inputs = [f"{item['input']}\n{item['output']}" for item in examples]
    encodings = tokenizer(inputs, truncation=True, padding=True, max_length=512)

    # 准备 labels
    labels = []
    for encoding in encodings['input_ids']:
        labels.append([l if l != tokenizer.pad_token_id else -100 for l in encoding])

    encodings['labels'] = labels
    return encodings

# 创建 dataset
dataset = Dataset.from_list(data)
tokenized_dataset = dataset.map(
    preprocess,
    batched=True,
    remove_columns=dataset.column_names
)

# 4. 配置训练参数
training_args = TrainingArguments(
    output_dir="./results",
    num_train_epochs=3,
    per_device_train_batch_size=2,
    learning_rate=2e-5,
    logging_steps=10,
    save_steps=100
)

# 5. 创建 Trainer
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=tokenized_dataset,
    tokenizer=tokenizer
)

# 6. 训练
trainer.train()

# 7. 保存模型
trainer.save_model("./fine_tuned_model")
tokenizer.save_pretrained("./fine_tuned_model")
```

---

### 9. 使用微调后的模型

```python
from transformers import AutoModelForCausalLM, AutoTokenizer

# 加载微调模型
model = AutoModelForCausalLM.from_pretrained("./fine_tuned_model")
tokenizer = AutoTokenizer.from_pretrained("./fine_tuned_model")

# 生成
input_text = "写一个插入排序"
inputs = tokenizer(input_text, return_tensors="pt")

outputs = model.generate(
    **inputs,
    max_length=200,
    temperature=0.7,
    do_sample=True
)

result = tokenizer.decode(outputs[0], skip_special_tokens=True)
print(result)
```

---

## 📖 第二部分：10分钟 - 计算机视觉基础

---

### 什么是计算机视觉？

**CV = Computer Vision**

**定义**：让计算机"看懂"图像和视频

---

### 计算机视觉核心任务

```
计算机视觉
├── 图像分类
│   ├── 二分类（猫/狗）
│   └── 多分类（ImageNet 1000类）
├── 目标检测
│   ├── 边界框检测（YOLO, Faster R-CNN）
│   └── 实例分割（Mask R-CNN）
├── 语义分割
│   └── 像素级分类
└── 图像生成
    ├── GAN
    └── 扩散模型
```

---

### 图像基础知识

#### 1. 像素和颜色

```
图像 = 像素矩阵

RGB 图像：
    R   G   B
  [[255, 0, 0],   红色像素
   [0, 255, 0],   绿色像素
   [0, 0, 255]]   蓝色像素
```

**代码示例**：

```python
import numpy as np
from PIL import Image

# 创建图像
image = np.array([
    [[255, 0, 0], [0, 255, 0]],
    [[0, 0, 255], [255, 255, 0]]
], dtype=np.uint8)

# 转换为 PIL Image
pil_image = Image.fromarray(image)
pil_image.show()

# 形状：(高度, 宽度, 颜色通道)
print(f"图像形状: {image.shape}")  # (2, 2, 3)
```

---

#### 2. 卷积（Convolution）

**核心操作**：用卷积核（kernel）在图像上滑动

```
图像:         卷积核:
1 2 3        1 1
4 5 6        1 1
7 8 9

计算: 1×1 + 2×1 + 4×1 + 5×1 = 12
```

**作用**：
- 提取特征（边缘、纹理）
- 降采样

---

#### 3. CNN（卷积神经网络）

**结构**：

```
输入图像 (224×224×3)
    ↓
卷积层 (提取特征)
    ↓
池化层 (降维)
    ↓
卷积层 (提取更高级特征)
    ↓
池化层
    ↓
展平
    ↓
全连接层 (分类)
    ↓
输出 (1000类)
```

---

### 经典模型

| 模型 | 特点 |
|------|------|
| **LeNet** | 第一个 CNN (1998) |
| **AlexNet** | 深度学习突破 (2012) |
| **VGG** | 简单、易理解 |
| **ResNet** | 残差连接，解决梯度消失 |
| **MobileNet** | 轻量级，移动端 |
| **EfficientNet** | 高效平衡 |

---

## ✅ 今天你学到了什么

| 部分 | 知识点 | 掌握程度 |
|------|--------|---------|
| Fine-tuning | Fine-tuning 定义 | 🎯 已理解 |
| | 微调 vs RAG | 🎯 已掌握 |
| | LoRA | 🎯 已理解 |
| | 微调流程 | 🎯 已了解 |
| | 数据准备 | 🎯 已了解 |
| | Hugging Face 训练 | 🎯 已了解 |
| 计算机视觉 | CV 定义 | 🎯 已理解 |
| | 核心任务 | 🎯 已了解 |
| | 像素和颜色 | 🎯 已掌握 |
| | 卷积 | 🎯 已理解 |
| | CNN 结构 | 🎯 已了解 |
| | 经典模型 | 🎯 已了解 |

---

## 📝 今天的作业

1. ✅ 理解 Fine-tuning 的原理
2. ✅ 了解 LoRA 的优势
3. ✅ 理解计算机视觉的基础概念

---

## 🚀 明天预告

| 时间 | 主题 |
|------|------|
| 🕐 20分钟 | 高级 Prompt 技巧 |
| 🕐 10分钟 | 大语言模型原理 |

---

**学习进度**：📊 第4天/第3周

---

*最后更新：2026-03-27*
