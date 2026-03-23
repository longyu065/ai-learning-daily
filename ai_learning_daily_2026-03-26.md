# AI 知识学习日报

> 每天学习时间：30分钟 | 学习主题：NLP（自然语言处理） | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-26 周三 - 第3天

---

### 🎯 今日主题：自然语言处理（NLP）

---

## 📖 第一部分：NLP 基础（15分钟）

---

### 1. NLP 任务全景图

```
NLP
├── 文本分类
│   ├── 情感分析
│   ├── 垃圾邮件检测
│   └── 新闻分类
├── 序列标注
│   ├── 命名实体识别（NER）
│   └── 词性标注
├── 生成
│   ├── 机器翻译
│   ├── 摘要生成
│   └── 对话系统
└── 理解
    ├── 问答系统
    ├── 语义分析
    └── 意图识别
```

---

### 2. 文本预处理

**常用步骤**：

| 步骤 | 说明 | 示例 |
|------|------|------|
| **分词** | 将文本切分成单词 | "Hello World" → ["Hello", "World"] |
| **小写化** | 统一大小写 | "HELLO" → "hello" |
| **去停用词** | 去除无意义词 | "the", "a", "is" |
| **词干提取** | 提取词干 | "running" → "run" |

**代码示例**：

```python
import nltk
from nltk.corpus import stopwords
from nltk.tokenize import word_tokenize
from nltk.stem import PorterStemmer

# 下载必要资源
nltk.download('punkt')
nltk.download('stopwords')

# 示例文本
text = "Hello! This is an example of NLP preprocessing."

# 分词
tokens = word_tokenize(text)
print(f"分词结果: {tokens}")

# 小写化
tokens = [t.lower() for t in tokens]
print(f"小写化: {tokens}")

# 去停用词
stop_words = set(stopwords.words('english'))
tokens = [t for t in tokens if t.isalpha() and t not in stop_words]
print(f"去停用词: {tokens}")

# 词干提取
stemmer = PorterStemmer()
tokens = [stemmer.stem(t) for t in tokens]
print(f"词干提取: {tokens}")
```

---

### 3. 词向量

#### 词袋模型（Bag of Words）

```python
from sklearn.feature_extraction.text import CountVectorizer

# 文本集合
documents = [
    "I love programming",
    "Programming is fun",
    "I love AI"
]

# 创建词袋模型
vectorizer = CountVectorizer()
bow = vectorizer.fit_transform(documents)

# 词汇表
print("词汇表:", vectorizer.get_feature_names_out())

# BoW 表示
print("\nBoW 表示:")
print(bow.toarray())
```

**输出**：
```
词汇表: ['ai' 'fun' 'is' 'love' 'programming']

BoW 表示:
[[0 0 0 1 1]
 [0 1 1 0 1]
 [1 0 0 1 0]]
```

#### TF-IDF

```python
from sklearn.feature_extraction.text import TfidfVectorizer

# 创建 TF-IDF 模型
tfidf = TfidfVectorizer()
tfidf_matrix = tfidf.fit_transform(documents)

print("TF-IDF 表示:")
print(tfidf_matrix.toarray())
```

#### Word2Vec

```python
from gensim.models import Word2Vec

# 语料库
sentences = [
    ['i', 'love', 'programming'],
    ['programming', 'is', 'fun'],
    ['i', 'love', 'ai']
]

# 训练 Word2Vec
model = Word2Vec(sentences, vector_size=100, window=5, min_count=1)

# 获取词向量
print("编程的词向量:", model.wv['programming'])

# 计算相似度
print("\n'programming' 与 'fun' 的相似度:",
      model.wv.similarity('programming', 'fun'))
```

---

### 4. 经典 NLP 模型

#### Word2Vec 架构

| 架构 | 说明 |
|------|------|
| **CBOW** | 用上下文预测中心词 |
| **Skip-gram** | 用中心词预测上下文 |

**对比**：
- CBOW：更快，适合常见词
- Skip-gram：更准确，适合罕见词

---

#### GloVe（Global Vectors）

**结合**：局部上下文 + 全局共现矩阵

**优势**：词向量反映语义关系

**示例**：
```
King - Man + Woman ≈ Queen
```

---

### 5. 预训练模型

#### BERT

**BERT = Bidirectional Encoder Representations from Transformers**

**特点**：
- 双向上下文
- Transformer 编码器
- 预训练 + 微调

**架构**：

```python
from transformers import BertTokenizer, BertModel

# 加载预训练模型
tokenizer = BertTokenizer.from_pretrained('bert-base-uncased')
model = BertModel.from_pretrained('bert-base-uncased')

# 编码文本
text = "Hello, I am learning NLP!"
inputs = tokenizer(text, return_tensors='pt', padding=True)

# 获取表示
outputs = model(**inputs)
last_hidden_states = outputs.last_hidden_state

print(f"输入形状: {inputs['input_ids'].shape}")
print(f"输出形状: {last_hidden_states.shape}")
```

---

#### GPT

**GPT = Generative Pre-trained Transformer**

**特点**：
- 单向（从左到右）
- Transformer 解码器
- 生成能力强

**使用 GPT**：

```python
from transformers import GPT2Tokenizer, GPT2LMHeadModel

# 加载模型
tokenizer = GPT2Tokenizer.from_pretrained('gpt2')
model = GPT2LMHeadModel.from_pretrained('gpt2')

# 生成文本
prompt = "The future of AI is"
input_ids = tokenizer.encode(prompt, return_tensors='pt')

output = model.generate(input_ids, max_length=50, num_return_sequences=1)

generated_text = tokenizer.decode(output[0], skip_special_tokens=True)
print(generated_text)
```

---

### 6. 典型 NLP 任务

#### 情感分析

```python
from transformers import pipeline

# 创建情感分析流水线
sentiment_analyzer = pipeline("sentiment-analysis")

# 分析
texts = [
    "I love this product!",
    "This is terrible.",
    "It's okay, not great."
]

for text in texts:
    result = sentiment_analyzer(text)[0]
    print(f"文本: {text}")
    print(f"情感: {result['label']}, 置信度: {result['score']:.2%}\n")
```

---

#### 命名实体识别（NER）

```python
ner = pipeline("ner", aggregation_strategy="simple")

text = "Elon Musk founded SpaceX in 2002 and lives in California."

entities = ner(text)
for entity in entities:
    print(f"{entity['entity_group']}: {entity['word']}")
```

---

#### 机器翻译

```python
translator = pipeline("translation", model="Helsinki-NLP/opus-mt-en-zh")

text = "Hello, how are you?"
translation = translator(text)[0]['translation_text']
print(f"原文: {text}")
print(f"译文: {translation}")
```

---

### 7. RAG（检索增强生成）

**RAG = Retrieval-Augmented Generation**

**流程**：
```
1. 文档切片 → 向量化 → 向量数据库
        ↓
2. 用户问题 → 检索相关文档
        ↓
3. 问题 + 文档 → LLM 生成答案
```

**简单实现**：

```python
from transformers import RagTokenizer, RagRetriever, RagSequenceForGeneration

# 加载模型
tokenizer = RagTokenizer.from_pretrained("facebook/rag-sequence-base")
retriever = RagRetriever.from_pretrained(
    "facebook/rag-sequence-base",
    index_name="exact",
    use_dummy_dataset=True
)
model = RagSequenceForGeneration.from_pretrained(
    "facebook/rag-sequence-base",
    retriever=retriever
)

# 问答
question = "What is the capital of France?"
input_dict = tokenizer(question, return_tensors="pt")
generated = model.generate(**input_dict)
answer = tokenizer.batch_decode(generated, skip_special_tokens=True)[0]
print(f"答案: {answer}")
```

---

## 💻 第二部分：实战练习（15分钟）

---

### 练习1：使用 Hugging Face 进行情感分析

```python
from transformers import pipeline

# 创建流水线
classifier = pipeline("sentiment-analysis")

# 分析不同情感
reviews = [
    "This is the best movie I've ever seen!",
    "I'm very disappointed with the service.",
    "The product works as expected.",
    "Absolutely terrible experience.",
    "Highly recommended!"
]

results = classifier(reviews)
for review, result in zip(reviews, results):
    emoji = "😊" if result['label'] == 'POSITIVE' else "😔"
    print(f"{emoji} {review}")
    print(f"   {result['label']}: {result['score']:.2%}\n")
```

---

### 练习2：文本分类

```python
from transformers import AutoTokenizer, AutoModelForSequenceClassification
import torch

# 加载模型
model_name = "distilbert-base-uncased-finetuned-sst-2-english"
tokenizer = AutoTokenizer.from_pretrained(model_name)
model = AutoModelForSequenceClassification.from_pretrained(model_name)

# 预测
texts = ["I love this!", "This is bad."]

for text in texts:
    inputs = tokenizer(text, return_tensors="pt")
    with torch.no_grad():
        outputs = model(**inputs)

    predictions = torch.nn.functional.softmax(outputs.logits, dim=-1)
    sentiment = "POSITIVE" if predictions[0][1] > predictions[0][0] else "NEGATIVE"
    confidence = max(predictions[0]).item()

    print(f"文本: {text}")
    print(f"情感: {sentiment}")
    print(f"置信度: {confidence:.2%}\n")
```

---

### 练习3：文本生成

```python
from transformers import pipeline

# 文本生成
generator = pipeline("text-generation", model="gpt2")

prompt = "In the future, AI will"
output = generator(prompt, max_length=50, num_return_sequences=2)

for i, gen in enumerate(output, 1):
    print(f"版本 {i}: {gen['generated_text']}\n")
```

---

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| NLP 任务分类 | 🎯 已理解 |
| 文本预处理 | 🎯 已掌握 |
| 词向量（BoW, TF-IDF, Word2Vec） | 🎯 已掌握 |
| 预训练模型（BERT, GPT） | 🎯 已理解 |
| 情感分析 | 🎯 已掌握 |
| 命名实体识别 | 🎯 已掌握 |
| 机器翻译 | 🎯 已了解 |
| RAG 原理 | 🎯 已了解 |

---

## 📝 今天的作业

1. ✅ 尝试不同的情感分析文本
2. ✅ 使用不同的预训练模型
3. ✅ 理解 RAG 的应用场景

---

## 🚀 本周学习进度

| 天数 | 日期 | 主题 | 状态 |
|------|------|------|------|
| 第1天 | 2026-03-24 | 机器学习基础 | ✅ |
| 第2天 | 2026-03-25 | 深度学习 | ✅ |
| 第3天 | 2026-03-26 | NLP | ✅ |
| 第4天 | 2026-03-27 | 计算机视觉 | 📅 |
| 第5天 | 2026-03-28 | 大语言模型 | ⏳ |
| 第6天 | 2026-03-29 | AI工程化 | ⏳ |
| 第7天 | 2026-03-30 | 综合面试题 | ⏳ |

---

**学习进度**：📊 第3天/第2周

---

*最后更新：2026-03-26*
