# AI 知识学习日报

> 每天学习时间：30分钟 | 学习主题：大语言模型（LLM） | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-04-04 周六 - 第5天

---

### 🎯 今日主题：大语言模型（LLM）

---

## 📖 学习内容（30分钟）

---

### 1. 什么是 LLM？

**LLM = Large Language Model**

**定义**：在大规模文本数据上训练的大型神经网络，能够理解和生成自然语言

**核心特点**：
- 📏 **大**：数十亿到万亿参数
- 🌐 **通用**：理解多种语言和任务
- 🎯 **涌现能力**：未显式训练的能力
- 🔄 **上下文学习**：从示例中学习

---

### 2. LLM 训练三阶段

#### 阶段1：预训练（Pre-training）

**目标**：学习语言规律

**方法**：预测下一个 token

**示例**：
```
输入: "人工智能的"
输出: "未来" (概率: 30%)
       "发展" (概率: 25%)
       ...
```

**数据规模**：
- 文本：万亿级 token
- 计算量：数千 GPU 天

---

#### 阶段2：指令微调（Instruction Fine-tuning）

**目标**：理解并遵循指令

**数据格式**：
```
指令: 用一句话解释什么是机器学习。
输入: (可选)
输出: 机器学习是让计算机从数据中学习规律的AI分支。
```

**流程**：
1. 收集指令-响应对
2. 继续训练模型
3. 模型学会"遵循指令"

---

#### 阶段3：RLHF（基于人类反馈的强化学习）

**目标**：对齐人类偏好

**三步骤**：

```
步骤1: 收集人类偏好数据
       → 比较 A 和 B 的输出

步骤2: 训练奖励模型（Reward Model）
       → 学习人类偏好

步骤3: 使用 PPO 微调 LLM
       → 最大化奖励
```

---

### 3. Transformer 架构

**核心公式**：
```
Attention(Q, K, V) = softmax(QK^T / √d_k)V
```

**核心组件**：

| 组件 | 作用 |
|------|------|
| **Self-Attention** | 计算词间关系 |
| **Multi-Head** | 并行多个注意力头 |
| **Feed-Forward** | 非线性变换 |
| **Layer Norm** | 层归一化 |
| **Positional Encoding** | 位置信息 |

---

### 4. LLM 的关键能力

#### 涌现能力（Emergent Abilities）

| 能力 | 出现参数规模 | 说明 |
|------|------------|------|
| **上下文学习** | ~100B+ | 从示例中学习 |
| **思维链** | ~100B+ | 分步推理 |
| **代码生成** | ~10B+ | 编写代码 |
| **多语言** | ~10B+ | 理解多种语言 |

---

#### 上下文学习示例

```python
# 给 LLM 几个示例
prompt = """
任务：将句子翻译成中文

示例1: "Hello" -> "你好"
示例2: "Good morning" -> "早上好"
示例3: "Thank you" -> "谢谢"

输入: "How are you?"
输出: """

# LLM 会根据示例学习模式
# 输出可能是: "你好吗？"
```

---

#### 思维链示例

```python
# 不使用思维链
prompt = "15 + 37 = ?"
# LLM 可能直接给出错误答案

# 使用思维链
prompt = """
15 + 37 = ?

让我们一步步计算：
1. 先算 5 + 7 = 12
2. 写下 2，进位 1
3. 再算 1 + 3 + 1 = 5
4. 结果是 52

所以 15 + 37 = 52
"""
```

---

### 5. LLM 的关键参数

#### Temperature（温度）

| 值 | 效果 | 适用场景 |
|----|------|---------|
| **0.1-0.3** | 更确定、一致 | 编程、翻译 |
| **0.5-0.7** | 平衡 | 通用问答 |
| **0.8-1.2** | 更随机、有创意 | 创意写作 |
| **1.5+** | 非常随机 | 不太使用 |

```python
import torch.nn.functional as F

def sample_with_temperature(logits, temperature=0.7):
    """带温度的采样"""
    logits = logits / temperature
    probs = F.softmax(logits, dim=-1)
    next_token = torch.multinomial(probs, num_samples=1)
    return next_token
```

---

#### Context Window（上下文窗口）

**含义**：模型能处理的最大 token 数

| 模型 | 上下文窗口 |
|------|-----------|
| **GPT-3.5** | 16K |
| **GPT-4** | 128K |
| **Claude 3** | 200K |
| **Gemini 1.5** | 1M |

**使用技巧**：

```python
import tiktoken

def count_tokens(text, model="gpt-3.5-turbo"):
    encoding = tiktoken.encoding_for_model(model)
    return len(encoding.encode(text))

text = "Hello, world!"
print(f"Token 数量: {count_tokens(text)}")

# 截断到上下文窗口
def truncate_text(text, max_tokens=4096, model="gpt-3.5-turbo"):
    encoding = tiktoken.encoding_for_model(model)
    tokens = encoding.encode(text)
    if len(tokens) > max_tokens:
        tokens = tokens[:max_tokens]
    return encoding.decode(tokens)
```

---

### 6. RAG（检索增强生成）

**RAG = Retrieval-Augmented Generation**

**原理**：
```
问题 → 向量检索 → 相关文档 → LLM 生成答案
```

**完整流程**：

```python
from sentence_transformers import SentenceTransformer
import numpy as np

# 1. 知识库
knowledge_base = [
    "机器学习是 AI 的子领域",
    "深度学习使用神经网络",
    "NLP 处理自然语言数据"
]

# 2. 编码器
encoder = SentenceTransformer('all-MiniLM-L6-v2')

# 3. 计算嵌入
doc_embeddings = encoder.encode(knowledge_base)

# 4. 查询
query = "什么是深度学习？"
query_embedding = encoder.encode([query])

# 5. 计算相似度（余弦相似度）
similarities = np.dot(query_embedding, doc_embeddings.T)[0]

# 6. 获取最相关的文档
top_idx = np.argmax(similarities)
print(f"查询: {query}")
print(f"最相关的文档: {knowledge_base[top_idx]}")
print(f"相似度: {similarities[top_idx]:.4f}")
```

---

### 7. Fine-tuning vs RAG

| 维度 | Fine-tuning | RAG |
|------|------------|-----|
| **更新知识** | 需要重新训练 | 直接添加文档 |
| **训练成本** | 高 | 低 |
| **知识时效性** | 差 | 好 |
| **幻觉问题** | 仍存在 | 减少幻觉 |
| **适用场景** | 领域特定任务 | 问答、知识检索 |

---

## 💻 实战练习（15分钟）

---

### 练习1：使用 OpenAI API

```python
from openai import OpenAI

client = OpenAI(api_key="your-api-key")

# 聊天补全
response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "system", "content": "你是一个有帮助的助手。"},
        {"role": "user", "content": "用一句话解释什么是大语言模型。"}
    ],
    temperature=0.7,
    max_tokens=100
)

print(response.choices[0].message.content)
```

---

### 练习2：计算 Token 数量

```python
import tiktoken

def analyze_text_tokens(text, model="gpt-3.5-turbo"):
    encoding = tiktoken.encoding_for_model(model)
    tokens = encoding.encode(text)

    print(f"文本: {text}")
    print(f"Token 数量: {len(tokens)}")
    print(f"Tokens: {tokens}")
    print(f"解码后: {encoding.decode(tokens)}")

# 示例
text = "Hello, world! How are you today?"
analyze_text_tokens(text)
```

---

### 练习3：简单的 RAG 实现

```python
from sentence_transformers import SentenceTransformer
import numpy as np

# 知识库
knowledge_base = [
    "机器学习是 AI 的一个子领域",
    "深度学习使用多层神经网络",
    "NLP 处理自然语言数据",
    "计算机视觉处理图像数据"
]

# 编码器
encoder = SentenceTransformer('all-MiniLM-L6-v2')

# 计算嵌入
doc_embeddings = encoder.encode(knowledge_base)

# 查询
query = "什么是深度学习？"
query_embedding = encoder.encode([query])

# 计算相似度（余弦相似度）
similarities = np.dot(query_embedding, doc_embeddings.T)[0]

# 获取最相关的文档
top_idx = np.argmax(similarities)
print(f"查询: {query}")
print(f"最相关的文档: {knowledge_base[top_idx]}")
print(f"相似度: {similarities[top_idx]:.4f}")
```

---

### 练习4：Temperature 对比

```python
from openai import OpenAI

client = OpenAI(api_key="your-api-key")

prompt = "写一个关于 AI 的简短故事。"

# 不同温度
for temp in [0.3, 0.7, 1.0]:
    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[{"role": "user", "content": prompt}],
        temperature=temp,
        max_tokens=100
    )

    print(f"\n温度 {temp}:")
    print(response.choices[0].message.content)
```

---

## 🎯 面试精选

---

### Q1: Transformer 自注意力机制

**问题**：解释 Transformer 的自注意力机制

**答案**：

**核心公式**：
```
Attention(Q, K, V) = softmax(QK^T / √d_k)V
```

**流程**：
1. **Q, K, V** = Input × W_Q, W_K, W_V
2. **注意力分数** = Q × K^T / √d_k
3. **Softmax** 归一化
4. **加权求和** = attention × V

---

### Q2: Positional Encoding 作用

**问题**：Transformer 为什么需要 Positional Encoding？

**答案**：

**原因**：Self-Attention 是位置无关的，无法知道词的顺序

**解决方案**：给每个位置添加位置编码

**公式**：
```
PE(pos, 2i) = sin(pos/10000^(2i/d))
PE(pos, 2i+1) = cos(pos/10000^(2i/d))
```

---

### Q3: RLHF 三个步骤

**问题**：RLHF 的三个步骤是什么？

**答案**：

1. **收集偏好数据**：让人类标注两个输出的优劣
2. **训练奖励模型**：学习人类偏好
3. **PPO 微调**：使用强化学习最大化奖励

---

### Q4: RAG vs Fine-tuning 选择

**问题**：何时使用 RAG？何时使用 Fine-tuning？

**答案**：

| 场景 | 选择 |
|------|------|
| 需要最新信息 | RAG |
| 领域任务 | Fine-tuning |
| 降低幻觉 | RAG |
| 改变输出格式 | Fine-tuning |
| 成本敏感 | RAG |

---

### Q5: Temperature 参数的作用

**问题**：LLM 中 Temperature 的作用？

**答案**：

**作用**：控制输出的随机性和确定性

| 温度 | 效果 |
|------|------|
| **低（0.1-0.3）** | 更确定、一致 |
| **中（0.5-0.7）** | 平衡 |
| **高（0.8-1.2）** | 更随机、有创意 |

---

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| LLM 定义和特点 | 🎯 已理解 |
| Transformer 架构 | 🎯 已掌握 |
| LLM 训练三阶段 | 🎯 已理解 |
| 涌现能力 | 🎯 已了解 |
| Temperature | 🎯 已掌握 |
| Context Window | 🎯 已掌握 |
| RAG 原理和实现 | 🎯 已掌握 |
| Fine-tuning vs RAG | 🎯 已理解 |

---

## 📝 今天的作业

1. ✅ 尝试使用 OpenAI API
2. ✅ 计算不同文本的 token 数量
3. ✅ 实现一个简单的 RAG 系统
4. ✅ 对比不同 temperature 的效果

---

## 🚀 本周学习进度

| 天数 | 日期 | 主题 | 状态 |
|------|------|------|------|
| 第1天 | 2026-03-31 | 机器学习基础 | ✅ |
| 第2天 | 2026-04-01 | 深度学习 | ✅ |
| 第3天 | 2026-04-02 | NLP | ✅ |
| 第4天 | 2026-04-03 | 计算机视觉 | ✅ |
| 第5天 | 2026-04-04 | 大语言模型 | ✅ |
| 第6天 | 2026-04-05 | AI工程化 | 📅 |
| 第7天 | 2026-04-06 | 综合面试题 | ⏳ |

---

**学习进度**：📊 第5天

---

*最后更新：2026-04-04*
