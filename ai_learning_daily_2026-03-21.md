# AI 知识学习日报

> 每天学习时间：30分钟 | 学习主题：大语言模型 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-21 周五 - 第5天

---

### 🎯 今日主题：大语言模型（LLM）

---

## 📖 第一部分：基础知识（15分钟）

---

### 1. 什么是大语言模型？

**大语言模型（Large Language Model，LLM）**：基于深度学习的自然语言处理模型，能够理解和生成人类语言

**核心特点**：
- ✅ 参数量大：从几十亿到数千亿参数
- ✅ 训练数据量大：使用互联网海量文本
- ✅ 通用能力强：一个模型可以完成多种任务

**代表模型**：
| 模型 | 参数量 | 公司 | 特点 |
|------|--------|------|------|
| GPT-4 | ~1.8T | OpenAI | 强大的推理能力 |
| Claude 3 | ~400B | Anthropic | 长上下文、安全性 |
| LLaMA 3 | 70B-405B | Meta | 开源、高性能 |
| Gemini Pro | 1.5T | Google | 多模态能力 |

---

### 2. LLM 的核心原理

#### Transformer 架构

**Transformer**：LLM 的核心架构

**关键组件**：
1. **自注意力机制（Self-Attention）**
   - 捕捉词与词之间的关系
   - 例如："苹果"是水果还是公司？需要看上下文

2. **多头注意力（Multi-Head Attention）**
   - 从不同角度理解文本
   - 同时关注语法、语义、情感等

3. **位置编码（Positional Encoding）**
   - 记录词的顺序信息
   - "我爱你" ≠ "你爱我"

**代码示例**：使用 Hugging Face 加载模型

```python
from transformers import AutoTokenizer, AutoModel

# 加载预训练模型
model_name = "bert-base-chinese"

tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModel.from_pretrained(model_name)

# 编码文本
text = "人工智能正在改变世界"
inputs = tokenizer(text, return_tensors="pt")

# 获取特征
outputs = model(**inputs)

print("输入形状:", inputs["input_ids"].shape)
print("输出形状:", outputs.last_hidden_state.shape)
```

---

### 3. LLM 的训练过程

#### 训练三阶段

**阶段1：预训练（Pre-training）**
- 目标：学习语言的一般知识
- 数据：互联网海量文本
- 任务：预测下一个词
- 时间：数周到数月

**阶段2：指令微调（Instruction Fine-tuning）**
- 目标：让模型遵循指令
- 数据：问答、对话、任务指令
- 时间：数天

**阶段3：人类反馈强化学习（RLHF）**
- 目标：对齐人类价值观
- 方法：人类评分、奖励模型、PPO 算法
- 时间：数周

**类比**：
- 预训练：读完所有书（博学）
- 指令微调：学会做题（应试）
- RLHF：变得有礼貌（品德）

---

### 4. LLM 的能力边界

#### LLM 能做什么？

| 能力类型 | 示例 |
|---------|------|
| **文本生成** | 写文章、代码、邮件 |
| **文本理解** | 摘要、分类、情感分析 |
| **对话交互** | 问答、客服、助手 |
| **代码生成** | 写代码、调试、解释代码 |
| **多语言** | 翻译、跨语言理解 |
| **逻辑推理** | 数学题、逻辑推理 |

#### LLM 不能做什么？

| 限制 | 说明 |
|------|------|
| **实时信息** | 训练数据截止后的信息不知道 |
| **精确计算** | 大数字计算可能出错 |
| **完美可靠** | 可能产生幻觉（编造信息） |
| **隐私保护** | 可能泄露训练数据中的信息 |
| **完全自主** | 需要人类监督和控制 |

---

### 5. LLM 的关键技术

#### Tokenization（分词）

**Token**：LLM 处理文本的基本单位

**分词方式**：
1. **字符级**：每个字符一个 token
   - "Hello" → 5 tokens
   - 问题：序列太长

2. **词级**：每个词一个 token
   - "Hello world" → 2 tokens
   - 问题：词表太大

3. **子词级（BPE/WordPiece）**：折中方案
   - "unhappiness" → "un", "happi", "ness" (3 tokens)
   - 最常用

**代码示例**：

```python
from transformers import AutoTokenizer

tokenizer = AutoTokenizer.from_pretrained("gpt2")

text = "Hello, world!"
tokens = tokenizer.tokenize(text)
ids = tokenizer.convert_tokens_to_ids(tokens)

print("原文:", text)
print("Tokens:", tokens)
print("Token IDs:", ids)
print("Token 数量:", len(tokens))
```

**输出**：
```
原文: Hello, world!
Tokens: ['Hello', ',', 'world', '!']
Token IDs: [15496, 11, 995, 0]
Token 数量: 4
```

#### Context Window（上下文窗口）

**Context Window**：模型能同时处理的文本长度

| 模型 | 上下文长度 |
|------|-----------|
| GPT-3.5 | 4K tokens |
| GPT-4 | 8K tokens |
| GPT-4 Turbo | 128K tokens |
| Claude 3 | 200K tokens |

**影响**：
- 长文档处理
- 长对话记忆
- 代码文件理解

#### Temperature（温度）

**Temperature**：控制生成的随机性

| 温度 | 效果 | 适用场景 |
|------|------|---------|
| 0 | 确定性、可重复 | 测试、代码生成 |
| 0.3-0.7 | 平衡 | 通用对话 |
| 1.0 | 较随机 | 创意写作 |
| >1.0 | 非常随机 | 生成多样样本 |

**代码示例**：

```python
import openai

response = openai.ChatCompletion.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "写一个故事"}],
    temperature=0.7  # 平衡创造性和连贯性
)
```

---

### 6. RAG（检索增强生成）

**问题**：LLM 知识有限，不知道你的私有数据

**RAG 解决方案**：
1. **检索**：从文档库中找到相关内容
2. **增强**：将检索内容提供给 LLM
3. **生成**：LLM 基于检索内容回答

**优势**：
- ✅ 知识可更新（更新文档即可）
- ✅ 减少幻觉（有文档支撑）
- ✅ 可解释性（知道答案来源）

**架构图**：
```
用户问题
    ↓
[向量检索]
    ↓
相关文档片段
    ↓
[LLM生成]
    ↓
回答 + 引用
```

---

### 7. Fine-tuning（微调）

**Fine-tuning**：在预训练模型基础上继续训练

**何时需要 Fine-tuning**：
- ✅ 特定领域（医疗、法律）
- ✅ 特定风格（公司内部文档）
- ✅ 特定任务（分类、抽取）

**与 RAG 对比**：

| 维度 | RAG | Fine-tuning |
|------|-----|-------------|
| 成本 | 低 | 高 |
| 更新速度 | 快 | 慢 |
| 知识容量 | 无限 | 受限制 |
| 适用场景 | 文档问答 | 领域适配 |

---

## 💻 第二部分：实战练习（15分钟）

---

### 练习1：使用 OpenAI API

**任务**：调用 GPT API 完成任务

```python
import openai

# 设置 API Key
openai.api_key = "your-api-key"

# 调用 GPT
response = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=[
        {
            "role": "system",
            "content": "你是一个 Python 编程助手"
        },
        {
            "role": "user",
            "content": "写一个计算斐波那契数列的函数"
        }
    ],
    temperature=0.3,
    max_tokens=500
)

answer = response.choices[0].message.content
print(answer)
```

---

### 练习2：Token 计算

**任务**：计算文本的 token 数量

```python
import tiktoken

# 加载 tokenizer
encoding = tiktoken.encoding_for_model("gpt-3.5-turbo")

text = "大语言模型正在改变世界！"
tokens = encoding.encode(text)

print(f"文本: {text}")
print(f"Token 数量: {len(tokens)}")
print(f"Tokens: {tokens}")

# 计算 cost
input_tokens = len(tokens)
cost_per_1k = 0.0015  # gpt-3.5-turbo 输入价格
total_cost = (input_tokens / 1000) * cost_per_1k

print(f"成本: ${total_cost:.6f}")
```

---

### 练习3：简单的 RAG

**任务**：基于文档问答

```python
from openai import OpenAI
import numpy as np

# 文档
documents = [
    "Copaw 是一个 AI 智能助手平台",
    "Copaw 支持飞书、钉钉等多渠道接入",
    "Copaw 的 Agent 功能可以自动化任务",
]

# 用户问题
question = "Copaw 有什么功能？"

# 简单的关键词匹配（实际应使用向量检索）
best_doc = max(documents, key=lambda d: len(set(d.split()) & set(question.split())))

print(f"检索到的文档: {best_doc}")

# 使用 LLM 回答
client = OpenAI()

response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[
        {
            "role": "system",
            "content": "你是一个 AI 助手，请基于提供的文档回答问题"
        },
        {
            "role": "user",
            "content": f"""
文档内容：
{best_doc}

用户问题：
{question}

请基于文档内容回答问题。
"""
        }
    ]
)

print("\n回答:")
print(response.choices[0].message.content)
```

---

### 练习4：Temperature 对比

**任务**：比较不同温度的生成效果

```python
import openai

prompts = ["写一个短故事"]

temperatures = [0.0, 0.5, 1.0, 1.5]

for temp in temperatures:
    response = openai.ChatCompletion.create(
        model="gpt-3.5-turbo",
        messages=[
            {"role": "user", "content": prompts[0]}
        ],
        temperature=temp,
        max_tokens=100
    )

    print(f"\nTemperature: {temp}")
    print("-" * 50)
    print(response.choices[0].message.content)
```

---

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| LLM 的定义和特点 | 🎯 已理解 |
| Transformer 架构 | 🎯 已了解 |
| 训练三阶段 | 🎯 已理解 |
| LLM 的能力边界 | 🎯 已理解 |
| Tokenization | 🎯 已掌握 |
| Context Window | 🎯 已理解 |
| Temperature | 🎯 已掌握 |
| RAG 原理 | 🎯 已理解 |
| Fine-tuning vs RAG | 🎯 已理解 |

---

## 📝 今天的作业（5分钟）

1. ✅ 尝试调用 OpenAI API
2. ✅ 计算一段文本的 token 数量
3. ✅ 对比不同 temperature 的效果

---

## 📅 明天预告（第6天）

| 时间 | 内容 |
|------|------|
| 15分钟 | **AI 工程化**：模型部署、监控、优化 |
| 15分钟 | **实战练习**：构建 LLM 应用 |

**明天你将**：
- ✅ 学习 LLM 部署方案
- ✅ 了解性能优化技巧
- ✅ 掌握监控和日志

---

## 💡 常见面试题

### Q1: Transformer 的自注意力机制是如何工作的？

**答案**：
自注意力机制通过计算 Query、Key、Value 三个矩阵，计算词与词之间的相关性：

```
Attention(Q, K, V) = softmax(QK^T / √d_k) V
```

- Q（Query）：查询向量
- K（Key）：键向量
- V（Value）：值向量
- 每个词通过 Query 与其他词的 Key 计算 attention，加权求和 Value

**举例**："苹果很好吃"
- "苹果"的 Query 与"好吃"的 Key 相似度高，attention 大
- 因此"苹果"的表示会融入"好吃"的信息

---

### Q2: 为什么需要 Positional Encoding？

**答案**：
Transformer 的自注意力机制是位置无关的，无法区分词的顺序。

例如："我爱你"和"你爱我"有完全相同的 attention 权重，但意义完全不同。

Positional Encoding 通过为每个位置添加固定的编码向量，让模型能感知词的顺序。

---

### Q3: RLHF 的三个步骤是什么？

**答案**：
1. **SFT（监督微调）**：用人类写的对话数据微调模型
2. **RM（奖励模型）**：训练一个模型来预测人类的偏好
3. **PPO（近端策略优化）**：用奖励模型优化 LLM

---

### Q4: RAG 和 Fine-tuning 如何选择？

**答案**：
- **优先 RAG**：
  - 文档问答
  - 需要频繁更新知识
  - 成本敏感

- **选择 Fine-tuning**：
  - 特定领域适配
  - 需要特定风格
  - 预算充足

- **结合使用**：
  - 先 Fine-tuning 做领域适配
  - 再 RAG 做文档检索

---

**学习进度**：📊 5/7 天完成

---

*最后更新：2026-03-21*
