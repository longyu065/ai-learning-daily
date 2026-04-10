# AI 知识学习日报

> 每天学习时间：30分钟 | 学习主题：NLP（自然语言处理） | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-04-09 周四 - 第3天

---

### 🎯 今日主题：NLP（自然语言处理）

---

## 📖 学习内容（30分钟）

---

### 1. 什么是 NLP？

**NLP = Natural Language Processing**

**定义**：让计算机理解、生成和处理人类语言的技术

**核心任务**：
- 📝 **文本分类**：情感分析、垃圾邮件检测
- 🔍 **信息抽取**：实体识别、关系抽取
- 💬 **对话系统**：聊天机器人、问答系统
- 🔄 **机器翻译**：多语言翻译
- 📝 **文本生成**：摘要、写作助手

---

### 2. 文本预处理

#### 分词

```python
import nltk
from nltk.tokenize import word_tokenize, sent_tokenize

# 下载必要的数据
nltk.download('punkt')

text = "I love natural language processing!"

# 词级分词
words = word_tokenize(text)
print(f"词级分词: {words}")

# 句级分词
sentences = sent_tokenize(text)
print(f"句级分词: {sentences}")
```

---

#### 去除停用词

```python
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize

# 下载停用词
nltk.download('stopwords')

text = "This is a sample sentence, showing off the stop words filtration."

# 分词
words = word_tokenize(text)

# 去除停用词
stop_words = set(stopwords.words('english'))
filtered_words = [w for w in words if w.lower() not in stop_words]

print(f"原始词: {words}")
print(f"去除停用词后: {filtered_words}")
```

---

#### 词干提取和词形还原

```python
from nltk.stem import PorterStemmer, WordNetLemmatizer
from nltk.tokenize import word_tokenize

# 下载数据
nltk.download('wordnet')

text = "The runners are running quickly"

# 分词
words = word_tokenize(text)

# 词干提取
stemmer = PorterStemmer()
stems = [stemmer.stem(w) for w in words]
print(f"词干提取: {stems}")

# 词形还原
lemmatizer = WordNetLemmatizer()
lemmas = [lemmatizer.lemmatize(w) for w in words]
print(f"词形还原: {lemmas}")
```

---

### 3. Word Embedding（词嵌入）

#### Word2Vec

**思想**：相似语义的词在向量空间中距离近

```python
from gensim.models import Word2Vec

# 训练数据
sentences = [
    ['I', 'love', 'natural', 'language', 'processing'],
    ['NLP', 'is', 'fascinating'],
    ['Deep', 'learning', 'improves', 'NLP']
]

# 训练 Word2Vec 模型
model = Word2Vec(sentences, vector_size=100, window=5, min_count=1, workers=4)

# 获取词向量
vector = model.wv['NLP']
print(f"NLP 的词向量: {vector[:5]}...")

# 计算相似度
similarity = model.wv.similarity('NLP', 'language')
print(f"NLP 和 language 的相似度: {similarity:.4f}")

# 找最相似的词
similar_words = model.wv.most_similar('NLP', topn=3)
print(f"与 NLP 最相似的词: {similar_words}")
```

---

#### GloVe

**思想**：结合局部上下文和全局统计信息

```python
# GloVe 模型通常预训练，这里展示加载方式
# from gensim.models import KeyedVectors

# # 加载预训练的 GloVe 模型
# glove_model = KeyedVectors.load_word2vec_format('glove.6B.100d.txt', binary=False)

# # 获取词向量
# vector = glove_model['king']
```

---

### 4. 情感分析

```python
from textblob import TextBlob

text = "I love this product! It's amazing."

# 分析情感
blob = TextBlob(text)
polarity = blob.sentiment.polarity
subjectivity = blob.sentiment.subjectivity

print(f"文本: {text}")
print(f"情感极性: {polarity:.4f} (-1:负面, 0:中性, 1:正面)")
print(f"主观性: {subjectivity:.4f} (0:客观, 1:主观)")

# 中文情感分析
# from snownlp import SnowNLP
# text = "这个产品真的很好用！"
# s = SnowNLP(text)
# print(f"情感分数: {s.sentiments:.4f}")
```

---

### 5. 文本分类（TF-IDF + 机器学习）

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.naive_bayes import MultinomialNB
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score, classification_report

# 准备数据
texts = [
    "I love this movie, it's great!",
    "This film is amazing and wonderful",
    "I hate this movie, it's terrible",
    "This film is awful and boring"
]
labels = [1, 1, 0, 0]  # 1: 正面, 0: 负面

# 特征提取（TF-IDF）
vectorizer = TfidfVectorizer(max_features=1000)
X = vectorizer.fit_transform(texts)

# 分割数据
X_train, X_test, y_train, y_test = train_test_split(X, labels, test_size=0.25, random_state=42)

# 训练模型
model = MultinomialNB()
model.fit(X_train, y_train)

# 预测
y_pred = model.predict(X_test)

# 评估
accuracy = accuracy_score(y_test, y_pred)
print(f"准确率: {accuracy:.4f}")
print("\n分类报告:")
print(classification_report(y_test, y_pred))

# 预测新文本
new_text = ["This is a great movie"]
new_X = vectorizer.transform(new_text)
prediction = model.predict(new_X)
print(f"\n新文本预测: {'正面' if prediction[0] == 1 else '负面'}")
```

---

### 6. Transformer 架构

#### Self-Attention

**核心公式**：
```
Attention(Q, K, V) = softmax(QK^T / √d_k)V
```

**代码实现**：

```python
import torch
import torch.nn as nn
import torch.nn.functional as F

class SelfAttention(nn.Module):
    def __init__(self, embed_dim, num_heads):
        super().__init__()
        self.embed_dim = embed_dim
        self.num_heads = num_heads
        self.head_dim = embed_dim // num_heads

        assert self.head_dim * num_heads == embed_dim, "embed_dim 必须能被 num_heads 整除"

        # Q, K, V 线性变换
        self.q_linear = nn.Linear(embed_dim, embed_dim)
        self.k_linear = nn.Linear(embed_dim, embed_dim)
        self.v_linear = nn.Linear(embed_dim, embed_dim)

        # 输出层
        self.out_linear = nn.Linear(embed_dim, embed_dim)

    def forward(self, x):
        batch_size, seq_len, embed_dim = x.shape

        # 计算 Q, K, V
        Q = self.q_linear(x).view(batch_size, seq_len, self.num_heads, self.head_dim).transpose(1, 2)
        K = self.k_linear(x).view(batch_size, seq_len, self.num_heads, self.head_dim).transpose(1, 2)
        V = self.v_linear(x).view(batch_size, seq_len, self.num_heads, self.head_dim).transpose(1, 2)

        # 计算注意力分数
        scores = torch.matmul(Q, K.transpose(-2, -1)) / (self.head_dim ** 0.5)
        attention_weights = F.softmax(scores, dim=-1)

        # 应用注意力权重
        out = torch.matmul(attention_weights, V)

        # 合并多头
        out = out.transpose(1, 2).contiguous().view(batch_size, seq_len, embed_dim)

        # 输出层
        return self.out_linear(out)

# 使用示例
attention = SelfAttention(embed_dim=512, num_heads=8)
x = torch.randn(32, 10, 512)  # (batch_size, seq_len, embed_dim)
output = attention(x)
print(f"输出形状: {output.shape}")
```

---

### 7. BERT 和 GPT

#### BERT vs GPT

| 模型 | 架构 | 训练目标 | 适用场景 |
|------|------|---------|---------|
| **BERT** | Encoder | Masked LM, Next Sentence Prediction | 理解任务 |
| **GPT** | Decoder | Next Token Prediction | 生成任务 |

---

#### 使用预训练的 BERT

```python
from transformers import BertTokenizer, BertModel

# 加载预训练的 BERT 模型
tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
model = BertModel.from_pretrained('bert-base-uncased')

# 输入文本
text = "Natural Language Processing is fascinating."

# Tokenization
inputs = tokenizer(text, return_tensors="pt", padding=True, truncation=True)

# 获取 BERT 输出
with torch.no_grad():
    outputs = model(**inputs)

# 获取词嵌入
last_hidden_states = outputs.last_hidden_state
print(f"输出形状: {last_hidden_states.shape}")

# 获取 [CLS] token 的嵌入（用于分类任务）
cls_embedding = last_hidden_states[:, 0, :]
print(f"[CLS] 嵌入形状: {cls_embedding.shape}")
```

---

## 💻 实战练习（15分钟）

---

### 练习1：文本预处理流程

```python
import nltk
from nltk.tokenize import word_tokenize
from nltk.corpus import stopwords
from nltk.stem import WordNetLemmatizer

# 下载必要的数据
nltk.download('punkt')
nltk.download('stopwords')
nltk.download('wordnet')

def preprocess_text(text):
    """文本预处理流程"""
    # 1. 转小写
    text = text.lower()

    # 2. 分词
    words = word_tokenize(text)

    # 3. 去除停用词
    stop_words = set(stopwords.words('english'))
    words = [w for w in words if w not in stop_words]

    # 4. 去除标点符号
    words = [w for w in words if w.isalnum()]

    # 5. 词形还原
    lemmatizer = WordNetLemmatizer()
    words = [lemmatizer.lemmatize(w) for w in words]

    return ' '.join(words)

# 测试
text = "The quick brown foxes are jumping over the lazy dogs!"
processed = preprocess_text(text)
print(f"原始文本: {text}")
print(f"处理后: {processed}")
```

---

### 练习2：Word2Vec 相似词查询

```python
from gensim.models import Word2Vec

# 训练数据
sentences = [
    ['king', 'man', 'queen', 'woman', 'apple', 'fruit'],
    ['king', 'queen', 'royal', 'throne'],
    ['apple', 'banana', 'orange', 'fruit'],
    ['man', 'woman', 'person']
]

# 训练 Word2Vec
model = Word2Vec(sentences, vector_size=100, window=3, min_count=1, workers=4)

# 查找相似词
word = 'king'
similar_words = model.wv.most_similar(word, topn=3)
print(f"与 '{word}' 最相似的词:")
for word, score in similar_words:
    print(f"  {word}: {score:.4f}")

# 计算词向量相似度
similarity = model.wv.similarity('king', 'queen')
print(f"\n'king' 和 'queen' 的相似度: {similarity:.4f}")
```

---

### 练习3：情感分析

```python
from textblob import TextBlob

texts = [
    "I absolutely love this product!",
    "This is terrible, I hate it.",
    "It's okay, nothing special.",
    "Amazing experience, highly recommended!",
    "Worst purchase ever."
]

for text in texts:
    blob = TextBlob(text)
    polarity = blob.sentiment.polarity

    # 判断情感
    if polarity > 0.3:
        sentiment = "正面"
    elif polarity < -0.3:
        sentiment = "负面"
    else:
        sentiment = "中性"

    print(f"文本: {text}")
    print(f"情感: {sentiment} ({polarity:.4f})\n")
```

---

### 练习4：简单文本分类

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.svm import SVC
from sklearn.model_selection import train_test_split
from sklearn.metrics import accuracy_score

# 准备数据
texts = [
    "Great movie, loved it!",
    "Best film ever",
    "Terrible movie, hated it",
    "Worst film ever",
    "Excellent performance",
    "Awful experience",
    "Outstanding work",
    "Complete disaster"
]
labels = [1, 1, 0, 0, 1, 0, 1, 0]  # 1: 正面, 0: 负面

# 特征提取
vectorizer = TfidfVectorizer()
X = vectorizer.fit_transform(texts)

# 分割数据
X_train, X_test, y_train, y_test = train_test_split(X, labels, test_size=0.25, random_state=42)

# 训练模型
model = SVC(kernel='linear')
model.fit(X_train, y_train)

# 预测
y_pred = model.predict(X_test)
accuracy = accuracy_score(y_test, y_pred)

print(f"准确率: {accuracy:.4f}")

# 预测新文本
new_texts = ["This is great!", "This is bad"]
new_X = vectorizer.transform(new_texts)
predictions = model.predict(new_X)

for text, pred in zip(new_texts, predictions):
    print(f"\n'{text}' -> {'正面' if pred == 1 else '负面'}")
```

---

## 🎯 面试精选

---

### Q1: Word2Vec 原理

**问题**：Word2Vec 的两种训练方法？

**答案**：

| 方法 | 原理 | 特点 |
|------|------|------|
| **CBOW** | 用上下文预测中心词 | 快、适合小数据 |
| **Skip-gram** | 用中心词预测上下文 | 慢、效果更好 |

---

### Q2: Transformer 自注意力

**问题**：自注意力机制的原理？

**答案**：

**核心公式**：
```
Attention(Q, K, V) = softmax(QK^T / √d_k)V
```

**计算步骤**：
1. Q, K, V = Input × W_Q, W_K, W_V
2. 注意力分数 = Q × K^T / √d_k
3. Softmax 归一化
4. 加权求和 = attention × V

---

### Q3: BERT vs GPT

**问题**：BERT 和 GPT 的区别？

**答案**：

| 模型 | 架构 | 训练目标 | 适用场景 |
|------|------|---------|---------|
| **BERT** | Encoder | Masked LM, NSP | 理解任务 |
| **GPT** | Decoder | Next Token Prediction | 生成任务 |

---

### Q4: TF-IDF 原理

**问题**：TF-IDF 的计算方法？

**答案**：

**公式**：
```
TF(t, d) = 词 t 在文档 d 中的出现次数
IDF(t) = log(文档总数 / 包含词 t 的文档数)
TF-IDF(t, d) = TF(t, d) × IDF(t)
```

**含义**：
- **TF**：词在文档中的重要性
- **IDF**：词在整个语料库中的重要性
- **TF-IDF**：词在文档中的综合重要性

---

### Q5: 文本预处理步骤

**问题**：文本预处理的主要步骤？

**答案**：

1. **分词**：将文本切分成词或句子
2. **去除停用词**：去除无意义的词（如 "the", "is"）
3. **词干提取**：将词还原到词根
4. **词形还原**：将词还原到基本形式
5. **去除标点**：去除标点符号
6. **转小写**：统一大小写

---

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| NLP 定义和核心任务 | 🎯 已掌握 |
| 文本预处理（分词、去停用词、词形还原） | 🎯 已掌握 |
| Word Embedding（Word2Vec、GloVe） | 🎯 已掌握 |
| 情感分析 | 🎯 已掌握 |
| 文本分类（TF-IDF） | 🎯 已掌握 |
| Transformer 自注意力机制 | 🎯 已掌握 |
| BERT 和 GPT | 🎯 已理解 |

---

## 📝 今天的作业

1. ✅ 完成文本预处理练习
2. ✅ 使用 Word2Vec 查找相似词
3. ✅ 实现情感分析
4. ✅ 完成简单文本分类

---

## 🚀 本周学习进度

| 天数 | 日期 | 主题 | 状态 |
|------|------|------|------|
| 第1天 | 2026-04-07 | 机器学习基础 | ✅ |
| 第2天 | 2026-04-08 | 深度学习 | ✅ |
| 第3天 | 2026-04-09 | NLP | ✅ |
| 第4天 | 2026-04-10 | 计算机视觉 | 📅 |
| 第5天 | 2026-04-11 | 大语言模型 | ⏳ |
| 第6天 | 2026-04-12 | AI工程化 | ⏳ |
| 第7天 | 2026-04-13 | 综合面试题 | ⏳ |

---

**学习进度**：📊 第3天

---

*最后更新：2026-04-09*
