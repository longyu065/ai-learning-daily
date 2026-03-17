# AI 知识学习日报

> 每天学习时间：30分钟 | 模式：20分钟大模型应用 + 10分钟机器学习基础 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-19 周三 - 第3天（混合学习版）

---

### 🕐 第一部分：20分钟 - RAG（检索增强生成）基础

#### 🎯 今天目标：让 AI 读懂你的文档

---

#### 📖 什么是 RAG？

**RAG = Retrieval-Augmented Generation = 检索增强生成**

**核心问题**：GPT 的知识是固定的，它不知道你的私有数据（公司文档、产品手册、个人笔记）

**RAG 的解决方案**：
1. **存储**：把你的文档存入向量数据库
2. **检索**：用户提问时，从数据库找到相关片段
3. **生成**：把找到的片段喂给 GPT，让它基于这些内容回答

---

#### 🔄 RAG 工作流程

```
用户问题 → 检索相关文档片段 → 提供给 GPT → GPT 基于片段回答
```

**类比**：
- 没有 RAG：就像让 GPT 答题，但它没看过教材
- 有 RAG：考试时允许翻书，GPT 翻书后答题

---

#### 🔧 环境准备（3分钟）

```bash
# 安装依赖
pip install chromadb openai tiktoken
```

**依赖说明**：
- `chromadb`：向量数据库（存储文档）
- `openai`：调用 GPT
- `tiktoken`：文本分词（计算 tokens）

---

#### 💻 简单 RAG 示例（15分钟）

**创建文件** `simple_rag.py`：

```python
import chromadb
from chromadb.config import Settings
from openai import OpenAI
import tiktoken

# ========== 第一步：创建向量数据库 ==========
print("📚 创建向量数据库...")

client = chromadb.Client(Settings())

# 创建集合（类似数据库表）
collection = client.create_collection(
    name="my_documents",
    metadata={"hnsw:space": "cosine"}  # 使用余弦相似度
)

# ========== 第二步：添加文档 ==========
print("📝 添加文档...")

documents = [
    {
        "id": "doc1",
        "content": "Copaw 是一个 AI 智能助手平台，支持飞书、钉钉等多渠道接入。",
        "metadata": {"category": "产品介绍"}
    },
    {
        "id": "doc2",
        "content": "Copaw 支持 Python 和 TypeScript SDK，方便开发者集成到现有项目中。",
        "metadata": {"category": "开发文档"}
    },
    {
        "id": "doc3",
        "content": "使用 Copaw 的 Agent 功能，可以自动化完成复杂任务，如数据分析和报告生成。",
        "metadata": {"category": "功能说明"}
    },
    {
        "id": "doc4",
        "content": "Copaw 的定时任务功能支持 Cron 表达式，可以精确到每分钟执行。",
        "metadata": {"category": "功能说明"}
    }
]

# 添加到向量数据库
collection.add(
    ids=[doc["id"] for doc in documents],
    documents=[doc["content"] for doc in documents],
    metadatas=[doc["metadata"] for doc in documents]
)

print(f"✅ 已添加 {len(documents)} 个文档")

# ========== 第三步：检索相关文档 ==========
print("\n🔍 检索相关文档...")

user_question = "Copaw 有什么功能？"

# 查询最相似的文档
results = collection.query(
    query_texts=[user_question],
    n_results=2  # 返回最相关的 2 个文档
)

print(f"问题：{user_question}")
print("找到的相关文档：")
for i, doc in enumerate(results['documents'][0], 1):
    print(f"{i}. {doc}")

# ========== 第四步：基于检索内容生成回答 ==========
print("\n💬 生成回答...")

openai_client = OpenAI()

# 构造提示词
retrieved_docs = results['documents'][0]
prompt = f"""
你是一个 AI 助手。请基于以下文档回答用户问题。

文档内容：
{chr(10).join([f"- {doc}" for doc in retrieved_docs])}

用户问题：{user_question}

请用简洁明了的语言回答，只基于提供的文档内容。
"""

response = openai_client.chat.completions.create(
    model="gpt-4o-mini",
    messages=[
        {"role": "system", "content": "你是一个友好的 AI 助手。"},
        {"role": "user", "content": prompt}
    ],
    temperature=0.5,
    max_tokens=200
)

answer = response.choices[0].message.content

print("\n" + "=" * 50)
print("RAG 回答：")
print(answer)
print("=" * 50)

print(f"\n💰 Token 使用：输入 {response.usage.prompt_tokens}，输出 {response.usage.completion_tokens}")
```

**运行**：
```bash
python simple_rag.py
```

**预期输出**：
```
📚 创建向量数据库...
📝 添加文档...
✅ 已添加 4 个文档

🔍 检索相关文档...
问题：Copaw 有什么功能？
找到的相关文档：
1. 使用 Copaw 的 Agent 功能，可以自动化完成复杂任务，如数据分析和报告生成。
2. Copaw 的定时任务功能支持 Cron 表达式，可以精确到每分钟执行。

💬 生成回答...

==================================================
RAG 回答：
Copaw 主要提供以下功能：
1. Agent 智能体 - 可以自动化完成复杂任务，如数据分析和报告生成
2. 定时任务 - 支持 Cron 表达式，可以精确到每分钟执行
==================================================

💰 Token 使用：输入 95，输出 45
```

---

#### 🎯 RAG 的优势

| 传统问答 | RAG 问答 |
|---------|---------|
| ❌ 不知道你的数据 | ✅ 基于你的数据回答 |
| ❌ 回答可能不准确 | ✅ 有文档支撑，更可靠 |
| ❌ 无法更新知识 | ✅ 更新文档即可 |
| ❌ 容易产生幻觉 | ✅ 减少幻觉 |

---

#### 🚀 进阶：添加 PDF 文档

```python
# 安装 PDF 解析库
# pip install pypdf PyPDF2

from pypdf import PdfReader

def add_pdf_to_collection(pdf_path, collection):
    """读取 PDF 并添加到向量数据库"""
    reader = PdfReader(pdf_path)
    text = ""
    
    # 提取所有页面的文本
    for page in reader.pages:
        text += page.extract_text() + "\n"
    
    # 按段落分割（每 500 字一段）
    chunks = []
    chunk_size = 500
    for i in range(0, len(text), chunk_size):
        chunk = text[i:i+chunk_size]
        chunks.append(chunk)
    
    # 添加到数据库
    collection.add(
        ids=[f"pdf_{i}" for i in range(len(chunks))],
        documents=chunks,
        metadatas=[{"source": pdf_path}] * len(chunks)
    )
    
    print(f"✅ 已添加 PDF，共 {len(chunks)} 个片段")

# 使用示例
# add_pdf_to_collection("产品手册.pdf", collection)
```

---

### 🕐 第二部分：10分钟 - 常用算法详解

---

#### 📖 今天深入学习 3 个核心算法

---

#### 算法1：逻辑回归（Logistic Regression）

**核心**：用 Sigmoid 函数将线性输出映射到 0-1 之间

**Sigmoid 函数**：
```
sigmoid(z) = 1 / (1 + e^(-z))
```

**工作原理**：
```
输入 → 线性组合 → Sigmoid → 概率输出
X  →   wx+b  →  1/(1+e^(-z)) → 0.75 (75%概率为正类)
```

**代码示例**：

```python
from sklearn.linear_model import LogisticRegression
import numpy as np

# 模拟数据：身高 vs 性别
X_train = [[160], [165], [170], [175], [180]]  # 身高
y_train = [0, 0, 0, 1, 1]  # 0=女，1=男

# 训练
model = LogisticRegression()
model.fit(X_train, y_train)

# 预测
height = 172
prob = model.predict_proba([[height]])[0][1]
prediction = model.predict([[height]])[0]

print(f"身高 {height}cm：")
print(f"- 男性概率：{prob:.2%}")
print(f"- 预测结果：{'男' if prediction == 1 else '女'}")
```

**输出**：
```
身高 172cm：
- 男性概率：68%
- 预测结果：男
```

---

#### 算法2：决策树（Decision Tree）

**核心**：通过一系列"是/否"问题，把数据分类

**决策树示例**：
```
是否有编程经验？
├─ 是 → 是否了解 Python？
│      ├─ 是 → 高级学员
│      └─ 否 → 中级学员
└─ 否 → 初级学员
```

**代码示例**：

```python
from sklearn.tree import DecisionTreeClassifier
from sklearn import tree
import matplotlib.pyplot as plt

# 模拟数据：[编程经验, Python基础, AI兴趣]
X_train = [
    [1, 1, 1],  # 有经验，懂Python，感兴趣 → 高级
    [1, 0, 1],  # 有经验，不懂Python，感兴趣 → 中级
    [0, 0, 1],  # 无经验，不懂Python，感兴趣 → 初级
    [1, 1, 0],  # 有经验，懂Python，不感兴趣 → 中级
]
y_train = ["高级", "中级", "初级", "中级"]

# 训练
model = DecisionTreeClassifier()
model.fit(X_train, y_train)

# 预测
new_student = [1, 0, 0]  # 有经验，不懂Python，不感兴趣
prediction = model.predict([[*new_student]])[0]

print(f"新学生特征：经验=有，Python=无，兴趣=无")
print(f"预测等级：{prediction}")

# 可视化决策树
plt.figure(figsize=(8, 6))
tree.plot_tree(model, 
               feature_names=["编程经验", "Python基础", "AI兴趣"],
               class_names=["初级", "中级", "高级"],
               filled=True)
plt.show()
```

---

#### 算法3：K-Means 聚类

**核心**：把相似的数据自动分组

**算法步骤**：
1. 随机选择 K 个中心点
2. 把每个数据分配到最近的中心点
3. 计算每个簇的新中心点
4. 重复 2-3，直到中心点不再变化

**代码示例**：

```python
from sklearn.cluster import KMeans
import numpy as np

# 模拟客户数据：[消费金额，购买频率]
customers = [
    [100, 5],   # 低价值
    [150, 6],
    [500, 20],  # 高价值
    [600, 25],
    [120, 4],
    [550, 22],
]

# K-Means 聚类
model = KMeans(n_clusters=2, random_state=42)
labels = model.fit_predict(customers)

# 显示结果
print("客户聚类结果：")
for i, (customer, label) in enumerate(zip(customers, labels), 1):
    category = "高价值客户" if label == 1 else "普通客户"
    print(f"客户{i}: 消费{customer[0]}元/月，频率{customer[1]}次/月 → {category}")

print(f"\n聚类中心：")
for i, center in enumerate(model.cluster_centers_):
    print(f"簇{i}中心: 消费{center[0]:.0f}元/月，频率{center[1]:.0f}次/月")
```

**输出**：
```
客户聚类结果：
客户1: 消费100元/月，频率5次/月 → 普通客户
客户2: 消费150元/月，频率6次/月 → 普通客户
客户3: 消费500元/月，频率20次/月 → 高价值客户
客户4: 消费600元/月，频率25次/月 → 高价值客户
客户5: 消耗120元/月，频率4次/月 → 普通客户
客户6: 消费550元/月，频率22次/月 → 高价值客户

聚类中心：
簇0中心: 消耗123元/月，频率5次/月
簇1中心: 消耗550元/月，频率22次/月
```

---

## ✅ 今天您学到了什么

| 部分 | 知识点 | 掌握程度 |
|------|--------|---------|
| **大模型应用** | 理解 RAG 原理 | 🎯 已理解 |
| **大模型应用** | 使用 ChromaDB 存储文档 | 🎯 已掌握 |
| **大模型应用** | 实现简单的 RAG 系统 | 🎯 已掌握 |
| **机器学习基础** | 逻辑回归的原理和实现 | 🎯 已理解 |
| **机器学习基础** | 决策树的原理和实现 | 🎯 已理解 |
| **机器学习基础** | K-Means 聚类的原理和实现 | 🎯 已理解 |

---

## 📝 今天的作业（5分钟）

1. ✅ 运行 `simple_rag.py`，用自己的问题测试
2. ✅ 尝试添加更多文档到向量数据库
3. ✅ 运行 3 个算法示例代码

---

## 🚀 下一步

```
第1天 → 第2天 → 第3天（今天）
Prompt → API → RAG ✅
基础 → 监督/无监督 → 算法详解 ✅
```

---

## 📅 明天预告（第4天）

| 时间 | 内容 |
|------|------|
| 20分钟 | **大模型应用**：Fine-tuning 入门 - 训练自己的模型 |
| 10分钟 | **机器学习基础**：评估指标 - 准确率、精确率、召回率、F1 |

**明天您将**：
- ✅ 了解 Fine-tuning 是什么
- ✅ 学习模型评估的核心指标
- ✅ 理解精确率和召回率的权衡

---

**学习进度**：📊 3/7 天完成

---

*最后更新：2026-03-19*
