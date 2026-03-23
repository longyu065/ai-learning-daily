# AI 知识学习日报

> 每天学习时间：30分钟 | 学习主题：大语言模型（LLM） | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-28 周五 - 第5天

---

### 🎯 今日主题：大语言模型（LLM）

---

## 📖 第一部分：LLM 核心概念（15分钟）

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

### 2. Transformer 架构回顾

**核心组件**：

| 组件 | 作用 | 公式 |
|------|------|------|
| **Self-Attention** | 计算词间关系 | Attention(Q, K, V) = softmax(QK^T/√d)V |
| **Multi-Head** | 多头注意力 | 并行多个注意力头 |
| **Feed-Forward** | 非线性变换 | FFN(x) = ReLU(xW1 + b1)W2 + b2 |
| **Layer Norm** | 层归一化 | 稳定训练 |
| **Positional Encoding** | 位置信息 | PE(pos, 2i) = sin(pos/10000^(2i/d)) |

**代码示例**：

```python
import torch
import torch.nn as nn
import math

class MultiHeadAttention(nn.Module):
    def __init__(self, d_model, num_heads):
        super().__init__()
        self.d_model = d_model
        self.num_heads = num_heads
        self.d_k = d_model // num_heads

        self.W_q = nn.Linear(d_model, d_model)
        self.W_k = nn.Linear(d_model, d_model)
        self.W_v = nn.Linear(d_model, d_model)
        self.W_o = nn.Linear(d_model, d_model)

    def scaled_dot_product_attention(self, Q, K, V):
        scores = torch.matmul(Q, K.transpose(-2, -1)) / math.sqrt(self.d_k)
        attention_weights = torch.softmax(scores, dim=-1)
        output = torch.matmul(attention_weights, V)
        return output

    def forward(self, x):
        batch_size, seq_len, d_model = x.shape

        Q = self.W_q(x).view(batch_size, seq_len, self.num_heads, self.d_k).transpose(1, 2)
        K = self.W_k(x).view(batch_size, seq_len, self.num_heads, self.d_k).transpose(1, 2)
        V = self.W_v(x).view(batch_size, seq_len, self.num_heads, self.d_k).transpose(1, 2)

        attention = self.scaled_dot_product_attention(Q, K, V)
        attention = attention.transpose(1, 2).contiguous().view(batch_size, seq_len, d_model)

        return self.W_o(attention)
```

---

### 3. LLM 训练三阶段

#### 阶段1：预训练（Pre-training）

**目标**：学习语言规律

**方法**：预测下一个 token

```python
# 简化的语言模型训练
def language_model_loss(logits, targets):
    """
    logits: (batch, seq_len, vocab_size)
    targets: (batch, seq_len)
    """
    batch_size, seq_len, vocab_size = logits.shape

    # 重塑以便计算交叉熵
    logits = logits.view(-1, vocab_size)
    targets = targets.view(-1)

    loss = nn.functional.cross_entropy(logits, targets)
    return loss

# 示例
batch_size, seq_len, vocab_size = 4, 32, 50000
logits = torch.randn(batch_size, seq_len, vocab_size)
targets = torch.randint(0, vocab_size, (batch_size, seq_len))

loss = language_model_loss(logits, targets)
print(f"语言模型损失: {loss.item():.4f}")
```

---

#### 阶段2：指令微调（Instruction Fine-tuning）

**目标**：理解并遵循指令

**数据格式**：

```
指令: 请用一句话总结这篇文章。
输入: [文章内容]
输出: 这篇文章讨论了气候变化对农业的影响。
```

**训练代码**：

```python
def supervised_fine_tune(model, instruction_data):
    """
    instruction_data: list of (instruction, input, output)
    """
    optimizer = torch.optim.AdamW(model.parameters(), lr=1e-5)

    for epoch in range(3):
        total_loss = 0
        for batch in instruction_data:
            instruction, input_text, target_output = batch

            # 拼接输入
            prompt = f"指令: {instruction}\n输入: {input_text}\n输出: "

            # 生成
            outputs = model.generate(
                prompt,
                max_new_tokens=128,
                labels=target_output
            )

            loss = outputs.loss
            loss.backward()
            optimizer.step()
            optimizer.zero_grad()

            total_loss += loss.item()

        print(f"Epoch {epoch}, Loss: {total_loss/len(instruction_data):.4f}")
```

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

**RLHF 伪代码**：

```python
# 1. 收集偏好数据
preferences = collect_human_preferences(model, prompts)

# 2. 训练奖励模型
reward_model = train_reward_model(preferences)

# 3. PPO 微调
def ppo_update(policy_model, reward_model, prompts):
    for epoch in range(10):
        # 生成响应
        responses = policy_model.generate(prompts)

        # 计算奖励
        rewards = reward_model(prompts, responses)

        # PPO 更新
        policy_loss = compute_ppo_loss(policy_model, responses, rewards)
        policy_loss.backward()
        optimizer.step()
```

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
import torch
import torch.nn.functional as F

def sample_with_temperature(logits, temperature=0.7):
    """带温度的采样"""
    logits = logits / temperature
    probs = F.softmax(logits, dim=-1)
    next_token = torch.multinomial(probs, num_samples=1)
    return next_token

# 示例
logits = torch.tensor([1.0, 2.0, 1.5, 0.5])

# 不同温度
for temp in [0.1, 0.5, 1.0, 2.0]:
    token = sample_with_temperature(logits, temp)
    print(f"温度 {temp}: token = {token.item()}")
```

---

#### Top-p / Top-k

**Top-k**：从概率最高的 k 个中采样
**Top-p**：从累积概率达到 p 的词中采样

```python
def top_k_sampling(logits, k=50):
    """Top-k 采样"""
    top_k_logits, top_k_indices = torch.topk(logits, k)

    # 只考虑 top-k 的词
    logits_filtered = torch.full_like(logits, float('-inf'))
    logits_filtered.scatter_(1, top_k_indices, top_k_logits)

    probs = F.softmax(logits_filtered, dim=-1)
    return torch.multinomial(probs, num_samples=1)

def top_p_sampling(logits, p=0.9):
    """Top-p（nucleus）采样"""
    sorted_logits, sorted_indices = torch.sort(logits, descending=True)
    cumulative_probs = torch.cumsum(F.softmax(sorted_logits, dim=-1), dim=-1)

    # 移除累积概率超过 p 的词
    sorted_indices_to_remove = cumulative_probs > p
    sorted_indices_to_remove[:, 1:] = sorted_indices_to_remove[:, :-1].clone()
    sorted_indices_to_remove[:, 0] = 0

    sorted_logits[sorted_indices_to_remove] = float('-inf')

    probs = F.softmax(sorted_logits, dim=-1)
    sampled_indices = torch.multinomial(probs, num_samples=1)

    return torch.gather(sorted_indices, 1, sampled_indices)
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
# 检查 token 数量
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
import faiss
import numpy as np

# 1. 文档嵌入
class DocumentRetriever:
    def __init__(self):
        self.encoder = SentenceTransformer('all-MiniLM-L6-v2')
        self.index = None
        self.documents = []

    def add_documents(self, docs):
        """添加文档到索引"""
        embeddings = self.encoder.encode(docs)
        self.documents.extend(docs)

        if self.index is None:
            self.index = faiss.IndexFlatL2(embeddings.shape[1])
        self.index.add(embeddings.astype('float32'))

    def retrieve(self, query, k=3):
        """检索最相关的文档"""
        query_embedding = self.encoder.encode([query])
        distances, indices = self.index.search(query_embedding.astype('float32'), k)

        results = []
        for i, (dist, idx) in enumerate(zip(distances[0], indices[0])):
            results.append({
                'doc': self.documents[idx],
                'score': dist,
                'rank': i + 1
            })

        return results

# 使用
retriever = DocumentRetriever()
docs = [
    "Python 是一种高级编程语言",
    "机器学习是 AI 的子领域",
    "深度学习使用神经网络"
]
retriever.add_documents(docs)

# 检索
query = "什么是深度学习？"
results = retriever.retrieve(query)

for r in results:
    print(f"Rank {r['rank']}: {r['doc']}")
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

## 💻 第二部分：实战练习（15分钟）

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
        {"role": "user", "content": "用一句话解释什么是机器学习。"}
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
| 第1天 | 2026-03-24 | 机器学习基础 | ✅ |
| 第2天 | 2026-03-25 | 深度学习 | ✅ |
| 第3天 | 2026-03-26 | NLP | ✅ |
| 第4天 | 2026-03-27 | 计算机视觉 | ✅ |
| 第5天 | 2026-03-28 | 大语言模型 | ✅ |
| 第6天 | 2026-03-29 | AI工程化 | 📅 |
| 第7天 | 2026-03-30 | 综合面试题 | ⏳ |

---

**学习进度**：📊 第5天/第2周

---

*最后更新：2026-03-28*
