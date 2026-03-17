# AI 知识学习日报

> 每天学习时间：30分钟 | 模式：20分钟大模型应用 + 10分钟机器学习基础 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-18 周二 - 第2天（混合学习版）

---

### 🕐 第一部分：20分钟 - OpenAI API 调用基础

#### 🎯 今天目标：用 Python 调用 GPT API

---

#### 📝 获取 API Key

**步骤**：
1. 打开 https://platform.openai.com/
2. 注册/登录账号
3. 点击右上角头像 → **API Keys**
4. 点击 **Create new secret key**
5. 复制生成的 Key（形如：`sk-proj-xxxxxxxx`）

**⚠️ 重要**：
- API Key 只显示一次，务必保存好
- 不要把 Key 提交到 GitHub（用环境变量）

---

#### 🔧 环境准备（5分钟）

**安装依赖**：

```bash
# 创建虚拟环境（推荐）
python -m venv venv

# 激活虚拟环境
# Windows:
venv\Scripts\activate
# Mac/Linux:
source venv/bin/activate

# 安装 OpenAI 库
pip install openai
```

**验证安装**：
```bash
python -c "import openai; print(openai.__version__)"
```

应该看到版本号，如 `1.0.0` 或更高。

---

#### 💻 第一个 API 调用（10分钟）

**创建文件** `first_api_call.py`：

```python
import os
from openai import OpenAI

# 方式1：直接设置 API Key（仅用于测试）
# client = OpenAI(api_key="sk-proj-你的key")

# 方式2：使用环境变量（推荐）
# Windows: set OPENAI_API_KEY=sk-proj-你的key
# Mac/Linux: export OPENAI_API_KEY=sk-proj-你的key
client = OpenAI()

# 方式3：从 .env 文件读取（生产环境推荐）
# 需要安装 python-dotenv: pip install python-dotenv
# from dotenv import load_dotenv
# load_dotenv()

print("🔌 连接 OpenAI API...")

# 调用 GPT-4o 模型
response = client.chat.completions.create(
    model="gpt-4o-mini",  # 使用性价比高的 mini 版本
    messages=[
        {
            "role": "system",
            "content": "你是一个Java开发专家。"
        },
        {
            "role": "user",
            "content": "用简单的话解释什么是接口？"
        }
    ],
    temperature=0.7,  # 控制随机性：0=固定，1=随机
    max_tokens=150     # 限制输出长度
)

# 提取回复内容
answer = response.choices[0].message.content

print("✅ API 调用成功！")
print("-" * 50)
print("GPT 回答：")
print(answer)
print("-" * 50)

# 查看消耗的 token
usage = response.usage
print(f"💰 Token 使用：输入 {usage.prompt_tokens}，输出 {usage.completion_tokens}，总计 {usage.total_tokens}")
```

**运行方式**：

```bash
# Windows（CMD）
set OPENAI_API_KEY=sk-proj-你的key
python first_api_call.py

# Windows（PowerShell）
$env:OPENAI_API_KEY="sk-proj-你的key"
python first_api_call.py

# Mac/Linux
export OPENAI_API_KEY=sk-proj-你的key
python first_api_call.py
```

**预期输出**：
```
🔌 连接 OpenAI API...
✅ API 调用成功！
--------------------------------------------------
GPT 回答：
接口就像一份合同或说明书。它定义了某个对象必须具备哪些方法，但不实现具体逻辑。

例如，"动物"接口规定动物必须有"叫声"方法，但具体怎么叫由实现它的类决定。狗实现为"汪汪"，猫实现为"喵喵"。
--------------------------------------------------
💰 Token 使用：输入 35，输出 82，总计 117
```

---

#### 🎯 参数说明（5分钟）

| 参数 | 说明 | 常用值 |
|------|------|--------|
| **model** | 使用的模型 | `gpt-4o-mini`（便宜）、`gpt-4o`（强大） |
| **messages** | 对话历史 | `[{role, content}]` 数组 |
| **temperature** | 随机性 | 0（确定性）、0.7（平衡）、1（创造性） |
| **max_tokens** | 最大输出长度 | 150（短）、500（中）、2000（长） |

**消息角色（role）**：

| 角色 | 说明 | 示例 |
|------|------|------|
| `system` | 设定 AI 的身份和行为 | "你是一个 Java 老师" |
| `user` | 用户的问题 | "解释什么是接口" |
| `assistant` | AI 的回答（用于多轮对话） | "接口就像一份合同..." |

---

#### 🚀 实战练习：让 AI 写 Java 代码

**创建文件** `java_coder.py`：

```python
from openai import OpenAI

client = OpenAI()

def ask_gpt_to_write_java(topic, difficulty="初级"):
    """让 GPT 帮忙写 Java 代码"""

    prompt = f"""
    你是一个 Java 开发专家。

    任务：写一个关于"{topic}"的 Java 程序
    难度：{difficulty}
    要求：
    1. 代码完整可运行
    2. 添加中文注释
    3. 包含 main 方法
    4. 用简洁的代码实现
    """

    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "你是一个专业的 Java 开发工程师。"},
            {"role": "user", "content": prompt}
        ],
        temperature=0.3,  # 代码类任务用较低温度
        max_tokens=500
    )

    return response.choices[0].message.content

# 示例1：单例模式
print("示例1：单例模式")
print("=" * 50)
code1 = ask_gpt_to_write_java("单例模式", "初级")
print(code1)
print()

# 示例2：快速排序
print("示例2：快速排序")
print("=" * 50)
code2 = ask_gpt_to_write_java("快速排序算法", "中级")
print(code2)
```

**运行**：
```bash
python java_coder.py
```

**你会看到**：GPT 自动生成带注释的 Java 代码！

---

### 🕐 第二部分：10分钟 - 机器学习基础深入

---

#### 📖 今日基础概念：监督学习 vs 无监督学习

---

#### 监督学习（有老师教）

**定义**：给模型提供数据的同时，也提供正确答案（标签）

**核心要素**：

| 要素 | 说明 | 示例 |
|------|------|------|
| **特征（Features）** | 输入变量 | 邮件内容、图片像素 |
| **标签（Label）** | 正确答案 | 垃圾/正常、猫/狗 |
| **训练集** | 用来学习的数据 | 80% 的数据 |
| **测试集** | 用来评估的数据 | 20% 的数据 |

**用 Java 类比**：

```java
// 训练数据
List<Email> trainingData = [
    Email("点击领奖", "垃圾"),
    Email("项目汇报", "正常"),
    Email("发票下载", "正常"),
    Email("中奖通知", "垃圾"),
    // ...更多数据
];

// 训练模型（就像给学生讲题 + 答案）
Model model = train(trainingData);

// 测试模型
Email newEmail = Email("恭喜中奖", "未知");
String prediction = model.predict(newEmail);
// 输出：垃圾
```

**常用监督学习算法**：

| 算法 | 类型 | 典型应用 |
|------|------|----------|
| **逻辑回归** | 二分类 | 垃圾邮件检测、广告点击预测 |
| **支持向量机（SVM）** | 分类/回归 | 图像分类、文本分类 |
| **决策树** | 分类/回归 | 信用评分、医疗诊断 |
| **随机森林** | 分类/回归 | 推荐系统、欺诈检测 |
| **线性回归** | 回归 | 房价预测、销量预测 |

**代码示例（逻辑回归）**：

```python
from sklearn.linear_model import LogisticRegression
from sklearn.datasets import load_breast_cancer
from sklearn.model_selection import train_test_split

# 加载数集（乳腺癌检测：0=良性，1=恶性）
data = load_breast_cancer()
X = data.data
y = data.target

# 拆分数据
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

# 训练模型
model = LogisticRegression()
model.fit(X_train, y_train)

# 评估
accuracy = model.score(X_test, y_test)
print(f"准确率: {accuracy * 100:.1f}%")

# 预测新样本
new_sample = [15.0, 20.0, 80.0, 500.0]  # 示例特征
prediction = model.predict([new_sample])
result = "恶性" if prediction[0] == 1 else "良性"
print(f"预测结果: {result}")
```

---

#### 无监督学习（自学）

**定义**：给模型提供数据，但不提供答案，让模型自己发现数据中的规律

**核心要素**：

| 要素 | 说明 | 示例 |
|------|------|------|
| **特征（Features）** | 输入变量 | 用户行为、商品属性 |
| **无标签** | 没有正确答案 | 不知道用户属于哪个群体 |
| **聚类** | 把相似的数据分在一起 | 用户分群、商品分类 |
| **降维** | 减少特征数量 | 100个特征 → 10个特征 |

**用 Java 类比**：

```java
// 只有数据，没有答案
List<User> users = [
    User("张三", 25, "程序员"),
    User("李四", 30, "产品经理"),
    User("王五", 35, "程序员"),
    // ...更多用户
];

// 无监督学习（就像让学生自己分组）
List<Group> groups = cluster(users);

// 结果：
// Group1: [张三, 王五] → 程序员群体
// Group2: [李四] → 产品经理群体
```

**常用无监督学习算法**：

| 算法 | 类型 | 典型应用 |
|------|------|----------|
| **K-Means** | 聚类 | 客户细分、文档分组 |
| **DBSCAN** | 聚类 | 异常检测、空间聚类 |
| **PCA** | 降维 | 数据可视化、特征压缩 |
| **Autoencoder** | 降维 | 图像压缩、特征学习 |

**代码示例（K-Means 聚类）**：

```python
from sklearn.cluster import KMeans
import matplotlib.pyplot as plt

# 生成随机数据（模拟两类客户）
import numpy as np
group1 = np.random.randn(50, 2) + [2, 2]   # 高价值客户
group2 = np.random.randn(50, 2) + [-2, -2] # 普通客户
X = np.vstack([group1, group2])

# K-Means 聚类
model = KMeans(n_clusters=2)
labels = model.fit_predict(X)

# 可视化
plt.scatter(X[:, 0], X[:, 1], c=labels)
plt.title("K-Means 客户聚类")
plt.xlabel("消费金额")
plt.ylabel("购买频率")
plt.show()
```

**你会看到**：数据自动分成两类（红点和蓝点）

---

#### 🎯 两种学习方式对比

| 维度 | 监督学习 | 无监督学习 |
|------|---------|-----------|
| **数据** | 有标签（答案） | 无标签（只有数据） |
| **目标** | 预测/分类 | 发现规律/分组 |
| **难度** | 较容易（有答案指导） | 较难（需要自己摸索） |
| **应用** | 图像识别、文本分类、推荐 | 客户分群、异常检测、降维 |
| **类比** | 学生做练习题有答案 | 学生自己总结规律 |

---

## ✅ 今天您学到了什么

| 部分 | 知识点 | 掌握程度 |
|------|--------|---------|
| **大模型应用** | 获取 OpenAI API Key | 🎯 已掌握 |
| **大模型应用** | 用 Python 调用 GPT API | 🎯 已掌握 |
| **大模型应用** | 理解 model、temperature、max_tokens 参数 | 🎯 已掌握 |
| **大模型应用** | 让 AI 写 Java 代码 | 🎯 已掌握 |
| **机器学习基础** | 监督学习：有标签，预测/分类 | 🎯 已理解 |
| **机器学习基础** | 无监督学习：无标签，发现规律 | 🎯 已理解 |
| **机器学习基础** | 常用算法：LR、SVM、决策树、K-Means | 🎯 已理解 |

---

## 📝 今天的作业（5分钟）

1. ✅ **获取 API Key**，完成第一个 API 调用
2. ✅ **修改 java_coder.py**，让 AI 写你感兴趣的内容（如"工厂模式"、"冒泡排序"）
3. ✅ **运行 K-Means 聚类代码**，观察聚类结果

---

## 🚀 下一步行动

```
第1天（已完成）
├─ Prompt Engineering
└─ 机器学习基础概念

第2天（今天）
├─ OpenAI API 调用（已完成？）
└─ 监督学习 vs 无监督学习（已完成？）

第3天（明天）
├─ RAG（检索增强生成）基础
└─ 常用算法详解
```

---

## 📅 明天预告（第3天）

| 时间 | 内容 |
|------|------|
| 20分钟 | **大模型应用**：RAG（检索增强生成）基础 - 让 AI 读懂你的文档 |
| 10分钟 | **机器学习基础**：常用算法详解 - 逻辑回归、决策树、K-Means |

**明天您将**：
- ✅ 了解 RAG 是什么
- ✅ 用向量数据库搭建简单 RAG 系统
- ✅ 深入理解 3 个核心算法的原理

---

**学习进度**：📊 2/7 天完成

---

**现在，去试试调用 OpenAI API 吧！有什么问题随时问我！** 🚀

---

*最后更新：2026-03-18*
