# AI 知识学习日报

> 每天学习时间：30分钟 | 模式：20分钟大模型应用 + 10分钟机器学习基础 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-26 周三 - 第3天

---

### 🎯 今日学习目标

| 部分 | 时间 | 主题 |
|------|------|------|
| 🕐 20分钟 | **大模型应用** | RAG 检索增强生成 |
| 🕐 10分钟 | **机器学习基础** | NLP 基础 |

---

## 📖 第一部分：20分钟 - RAG 检索增强生成

---

### 1. 什么是 RAG？

**RAG = Retrieval-Augmented Generation**

**定义**：通过检索相关文档来增强大模型的生成能力

**解决的核心问题**：
- ❌ **幻觉**：大模型生成不准确的内容
- ❌ **知识过时**：训练数据有截止日期，无法获取最新信息
- ❌ **缺乏上下文**：私有领域知识不在训练集中

---

### 2. RAG 工作原理

```
┌─────────────┐
│   用户问题   │
└──────┬──────┘
       │
       ├──────────────────┐
       │                  │
       ↓                  ↓
┌──────────────┐    ┌──────────────┐
│  向量检索    │    │  LLM 生成   │
│              │    │              │
│ 文档 → 向量  │    │ 问题+文档    │
│ 相似度计算   │    │ → 答案       │
└──────────────┘    └──────────────┘
       │                  │
       └────────┬─────────┘
                ↓
         ┌────────────┐
         │  最终答案  │
         └────────────┘
```

**核心步骤**：

| 步骤 | 说明 |
|------|------|
| 1. **文档切片** | 将长文档切分成小块 |
| 2. **向量化** | 将文档转换成向量 |
| 3. **存储** | 将向量存储到向量数据库 |
| 4. **检索** | 根据问题检索相关文档 |
| 5. **生成** | 将问题+文档输入 LLM 生成答案 |

---

### 3. 向量化基础

#### 什么是向量？

**向量 = 文本的数学表示**

```
文本："机器学习很有趣"
向量：[0.23, -0.45, 0.67, ...]
```

**特点**：
- 语义相近的文本，向量距离近
- 可以用余弦相似度计算相关性

**示例**：

| 文本 | 向量（简化） |
|------|-------------|
| "猫" | [0.9, 0.1, 0.2] |
| "狗" | [0.8, 0.2, 0.3] |
| "汽车" | [0.1, 0.9, 0.8] |

"猫"和"狗"更相近（余弦相似度高）
"猫"和"汽车"差异大

---

#### 余弦相似度

**公式**：
```
相似度 = (A · B) / (|A| × |B|)
```

**Python 实现**：

```python
from sklearn.metrics.pairwise import cosine_similarity
import numpy as np

# 示例向量
vec1 = np.array([[0.9, 0.1, 0.2]])  # "猫"
vec2 = np.array([[0.8, 0.2, 0.3]])  # "狗"
vec3 = np.array([[0.1, 0.9, 0.8]])  # "汽车"

# 计算相似度
sim1 = cosine_similarity(vec1, vec2)[0][0]  # 猫 vs 狗
sim2 = cosine_similarity(vec1, vec3)[0][0]  # 猫 vs 汽车

print(f"猫 vs 狗: {sim1:.3f}")  # 0.997（很高）
print(f"猫 vs 汽车: {sim2:.3f}")  # 0.286（很低）
```

---

### 4. 简单 RAG 实现

#### 📚 步骤1：准备文档

```python
# 知识库文档
documents = [
    "机器学习是 AI 的一个子领域，通过算法让计算机从数据中学习。",
    "深度学习是机器学习的一种，使用多层神经网络。",
    "自然语言处理（NLP）是研究计算机如何理解和生成人类语言。",
    "计算机视觉是让计算机理解和分析图像内容。",
    "Transformer 是一种深度学习模型，广泛用于 NLP 任务。",
    "GPT 是基于 Transformer 的生成式预训练模型。",
    "RAG（检索增强生成）通过检索文档来增强 LLM 的生成能力。"
]
```

---

#### 📚 步骤2：向量化文档

```python
from sentence_transformers import SentenceTransformer
import numpy as np

# 加载预训练模型
encoder = SentenceTransformer('all-MiniLM-L6-v2')

# 将文档转换为向量
document_embeddings = encoder.encode(documents)

print(f"文档数量: {len(documents)}")
print(f"向量维度: {document_embeddings.shape[1]}")
print(f"向量示例: {document_embeddings[0][:5]}...")
```

---

#### 📚 步骤3：检索相关文档

```python
def retrieve_documents(query, documents, embeddings, top_k=3):
    """检索最相关的文档"""
    # 将查询转换为向量
    query_embedding = encoder.encode([query])

    # 计算相似度
    similarities = cosine_similarity(query_embedding, embeddings)[0]

    # 获取 top-k 索引
    top_indices = similarities.argsort()[-top_k:][::-1]

    # 返回相关文档
    results = []
    for idx in top_indices:
        results.append({
            'document': documents[idx],
            'similarity': similarities[idx]
        })

    return results

# 测试
query = "什么是 RAG？"
results = retrieve_documents(query, documents, document_embeddings)

print(f"\n查询: {query}")
for i, result in enumerate(results, 1):
    print(f"{i}. [{result['similarity']:.3f}] {result['document']}")
```

---

#### 📚 步骤4：使用 LLM 生成答案

```python
from openai import OpenAI

client = OpenAI()

def generate_answer(query, retrieved_docs):
    """生成答案"""
    # 构建提示词
    context = "\n".join([doc['document'] for doc in retrieved_docs])

    prompt = f"""
基于以下信息回答问题：

{context}

问题: {query}

如果信息不足以回答，请说明。
"""

    # 调用 LLM
    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[
            {"role": "system", "content": "你是一个有帮助的助手，根据提供的信息回答问题。"},
            {"role": "user", "content": prompt}
        ],
        temperature=0.3
    )

    return response.choices[0].message.content

# 测试
query = "什么是 RAG？"
retrieved_docs = retrieve_documents(query, documents, document_embeddings)
answer = generate_answer(query, retrieved_docs)

print(f"\n问题: {query}")
print(f"\n答案: {answer}")
```

---

### 5. 完整 RAG 系统

```python
from sentence_transformers import SentenceTransformer
from sklearn.metrics.pairwise import cosine_similarity
from openai import OpenAI
import numpy as np

class SimpleRAG:
    """简单的 RAG 系统"""

    def __init__(self, model_name='all-MiniLM-L6-v2'):
        self.encoder = SentenceTransformer(model_name)
        self.client = OpenAI()
        self.documents = []
        self.embeddings = None

    def add_documents(self, docs):
        """添加文档"""
        self.documents.extend(docs)
        self._update_embeddings()

    def _update_embeddings(self):
        """更新向量"""
        self.embeddings = self.encoder.encode(self.documents)

    def retrieve(self, query, top_k=3):
        """检索文档"""
        query_embedding = self.encoder.encode([query])
        similarities = cosine_similarity(query_embedding, self.embeddings)[0]

        top_indices = similarities.argsort()[-top_k:][::-1]

        return [
            {'document': self.documents[i], 'similarity': similarities[i]}
            for i in top_indices
        ]

    def generate(self, query, top_k=3):
        """生成答案"""
        retrieved = self.retrieve(query, top_k)
        context = "\n".join([r['document'] for r in retrieved])

        prompt = f"""
基于以下信息回答问题：

{context}

问题: {query}
"""

        response = self.client.chat.completions.create(
            model="gpt-3.5-turbo",
            messages=[
                {"role": "system", "content": "你是一个有帮助的助手，根据提供的信息回答问题。"},
                {"role": "user", "content": prompt}
            ],
            temperature=0.3
        )

        return {
            'answer': response.choices[0].message.content,
            'sources': retrieved
        }

# 使用
rag = SimpleRAG()
rag.add_documents([
    "Python 是一种高级编程语言，语法简洁易读。",
    "Java 是一种面向对象的编程语言，跨平台。",
    "JavaScript 是 Web 开发的脚本语言。"
])

result = rag.generate("Python 的特点是什么？")
print(f"答案: {result['answer']}")
```

---

### 6. RAG vs Fine-tuning

| 维度 | RAG | Fine-tuning |
|------|-----|-------------|
| **知识更新** | 添加文档 | 重新训练 |
| **成本** | 低 | 高 |
| **时效性** | 好 | 差 |
| **幻觉** | 减少 | 仍存在 |
| **适用场景** | 问答、知识检索 | 领域任务 |

---

## 📖 第二部分：10分钟 - NLP 基础

---

### 什么是 NLP？

**NLP = Natural Language Processing（自然语言处理）**

**定义**：让计算机理解、解释和生成人类语言

---

### NLP 核心任务

```
NLP
├── 理解
│   ├── 情感分析
│   ├── 命名实体识别（NER）
│   └── 文本分类
├── 生成
│   ├── 机器翻译
│   ├── 摘要生成
│   └── 对话系统
└── 结构化
    ├── 词性标注
    └── 依存句法分析
```

---

### 基础概念

#### 1. 分词（Tokenization）

**定义**：将文本切分成有意义的单元

```python
from nltk.tokenize import word_tokenize
import nltk
nltk.download('punkt')

text = "Hello, world! How are you?"
tokens = word_tokenize(text)
print(tokens)
# ['Hello', ',', 'world', '!', 'How', 'are', 'you', '?']
```

---

#### 2. 停用词

**定义**：无实际意义的常见词

```python
from nltk.corpus import stopwords
import nltk
nltk.download('stopwords')

# 英文停用词
stop_words = set(stopwords.words('english'))
print(stop_words)
# {'i', 'me', 'my', 'myself', 'we', 'our', 'ours', ...}

# 过滤停用词
tokens = word_tokenize("I love natural language processing")
filtered = [t for t in tokens if t.lower() not in stop_words]
print(filtered)
# ['love', 'natural', 'language', 'processing']
```

---

#### 3. 词干提取

**定义**：将词还原为词根

```python
from nltk.stem import PorterStemmer

stemmer = PorterStemmer()

words = ["running", "ran", "runs"]
for word in words:
    print(f"{word} -> {stemmer.stem(word)}")
# running -> run
# ran -> ran
# runs -> run
```

---

#### 4. 词向量

**定义**：词的数值表示

**优势**：
- 捕捉语义关系
- 可以计算相似度

**示例**：
```
king - man + woman ≈ queen
```

---

## ✅ 今天你学到了什么

| 部分 | 知识点 | 掌握程度 |
|------|--------|---------|
| RAG | RAG 定义和原理 | 🎯 已理解 |
| | 向量基础 | 🎯 已掌握 |
| | 余弦相似度 | 🎯 已掌握 |
| | 简单 RAG 实现 | 🎯 已掌握 |
| | 完整 RAG 系统 | 🎯 已理解 |
| | RAG vs Fine-tuning | 🎯 已了解 |
| NLP | NLP 定义 | 🎯 已理解 |
| | 核心任务 | 🎯 已了解 |
| | 分词 | 🎯 已掌握 |
| | 停用词 | 🎯 已掌握 |
| | 词干提取 | 🎯 已掌握 |
| | 词向量 | 🎯 已了解 |

---

## 📝 今天的作业

1. ✅ 实现一个简单的 RAG 系统
2. ✅ 尝试用自己的文档构建知识库
3. ✅ 理解 NLP 的基础概念

---

## 🚀 明天预告

| 时间 | 主题 |
|------|------|
| 🕐 20分钟 | Fine-tuning 微调 |
| 🕐 10分钟 | 计算机视觉基础 |

---

**学习进度**：📊 第3天/第3周

---

*最后更新：2026-03-26*
