# AI 知识学习日报

> 每天学习时间：30分钟 | 模式：20分钟大模型应用 + 10分钟机器学习基础 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-20 周四 - 第4天（混合学习版）

---

### 🕐 第一部分：20分钟 - Fine-tuning 入门

#### 🎯 今天目标：了解如何训练自己的 GPT 模型

---

#### 📖 什么是 Fine-tuning？

**Fine-tuning = 微调**：在预训练模型的基础上，用你的数据继续训练

**类比**：
- **预训练**：就像让 AI 读完了互联网上所有的书（通用知识）
- **Fine-tuning**：让 AI 读你的专业资料（专业知识）

**对比 RAG**：

| 维度 | RAG | Fine-tuning |
|------|-----|-------------|
| **工作方式** | 检索文档后回答 | 模型"记住"知识 |
| **适用场景** | 文档查询、问答 | 特定领域、特定风格 |
| **成本** | 低（每次调用才查询） | 高（需要训练一次） |
| **更新速度** | 快（更新文档即可） | 慢（需要重新训练） |
| **知识容量** | 无限制 | 受模型容量限制 |

---

#### 🔄 Fine-tuning 工作流程

```
准备数据 → 转换格式 → 上传到 OpenAI → 开始训练 → 获取模型 ID → 使用新模型
```

**数据示例**：

```json
{"messages": [
  {"role": "system", "content": "你是一个 Java 代码助手"},
  {"role": "user", "content": "怎么实现单例模式？"},
  {"role": "assistant", "content": "单例模式确保一个类只有一个实例...\n\n```java\npublic class Singleton {\n    private static Singleton instance = new Singleton();\n    private Singleton() {}\n    public static Singleton getInstance() {\n        return instance;\n    }\n}\n```"}
]}
```

---

#### 💻 准备 Fine-tuning 数据（10分钟）

**创建文件** `prepare_finetuning_data.py`：

```python
import json

# ========== 第一步：创建训练数据 ==========
print("📝 创建训练数据...")

training_data = [
    # 示例1：单例模式
    {
        "messages": [
            {"role": "system", "content": "你是一个专业的 Java 开发专家，擅长提供简洁、带注释的代码示例。"},
            {"role": "user", "content": "写一个线程安全的单例模式"},
            {"role": "assistant", "content": "这是线程安全的单例模式（双重检查锁）：\n\n```java\npublic class Singleton {\n    private static volatile Singleton instance;\n\n    private Singleton() {}\n\n    public static Singleton getInstance() {\n        if (instance == null) {\n            synchronized (Singleton.class) {\n                if (instance == null) {\n                    instance = new Singleton();\n                }\n            }\n        }\n        return instance;\n    }\n}\n```\n\n**关键点**：\n- `volatile` 防止指令重排\n- 双重检查提高性能\n- 同步块保证线程安全"}
        ]
    },
    # 示例2：工厂模式
    {
        "messages": [
            {"role": "system", "content": "你是一个专业的 Java 开发专家，擅长提供简洁、带注释的代码示例。"},
            {"role": "user", "content": "写一个简单的工厂模式"},
            {"role": "assistant", "content": "这是简单的工厂模式示例：\n\n```java\n// 产品接口\ninterface Shape {\n    void draw();\n}\n\n// 具体产品\nclass Circle implements Shape {\n    public void draw() {\n        System.out.println(\"画圆形\");\n    }\n}\n\n// 工厂类\nclass ShapeFactory {\n    public static Shape getShape(String type) {\n        if (type.equals(\"circle\")) {\n            return new Circle();\n        }\n        return null;\n    }\n}\n\n// 使用\nShape circle = ShapeFactory.getShape(\"circle\");\ncircle.draw();\n```\n\n**优点**：解耦对象的创建和使用"}
        ]
    },
    # 示例3：观察者模式
    {
        "messages": [
            {"role": "system", "content": "你是一个专业的 Java 开发专家，擅长提供简洁、带注释的代码示例。"},
            {"role": "user", "content": "写一个观察者模式的例子"},
            {"role": "assistant", "content": "这是观察者模式示例（事件监听）：\n\n```java\n// 观察者接口\ninterface Observer {\n    void update(String message);\n}\n\n// 被观察者\nclass Publisher {\n    private List<Observer> observers = new ArrayList<>();\n    \n    public void subscribe(Observer o) {\n        observers.add(o);\n    }\n    \n    public void publish(String message) {\n        for (Observer o : observers) {\n            o.update(message);\n        }\n    }\n}\n\n// 使用\nPublisher pub = new Publisher();\npub.subscribe(msg -> System.out.println(\"收到: \" + msg));\npub.publish(\"新文章发布！\");\n```\n\n**应用**：Vue/React 的状态管理就是观察者模式"}
        ]
    },
    # 添加更多示例...
]

# ========== 第二步：保存为 JSONL 格式 ==========
print("💾 保存为 JSONL 格式...")

output_file = "finetuning_data.jsonl"
with open(output_file, 'w', encoding='utf-8') as f:
    for item in training_data:
        f.write(json.dumps(item, ensure_ascii=False) + '\n')

print(f"✅ 已保存 {len(training_data)} 条训练数据到 {output_file}")

# ========== 第三步：验证数据格式 ==========
print("\n🔍 验证数据格式...")

for i, item in enumerate(training_data[:3], 1):  # 只显示前3条
    print(f"\n示例 {i}:")
    print(f"系统消息: {item['messages'][0]['content'][:50]}...")
    print(f"用户消息: {item['messages'][1]['content']}")
    print(f"助手回复: {item['messages'][2]['content'][:100]}...")
```

**运行**：
```bash
python prepare_finetuning_data.py
```

**输出**：
```
📝 创建训练数据...
💾 保存为 JSONL 格式...
✅ 已保存 3 条训练数据到 finetuning_data.jsonl

🔍 验证数据格式...

示例 1:
系统消息: 你是一个专业的 Java 开发专家...
用户消息: 写一个线程安全的单例模式
助手回复: 这是线程安全的单例模式（双重检查锁）：...

示例 2:
系统消息: 你是一个专业的 Java 开发专家...
用户消息: 写一个简单的工厂模式
助手回复: 这是简单的工厂模式示例：...
```

---

#### 🚀 开始 Fine-tuning（命令行方式）

**上传数据**：
```bash
# 注意：这需要实际的 API Key 和余额
openai api files.create -f finetuning_data.jsonl -m fine-tune
```

**开始训练**：
```bash
openai api fine_tunes.create -t <FILE_ID> -m gpt-3.5-turbo
```

**查看状态**：
```bash
openai api fine_tunes.get <FINE_TUNE_ID>
```

**训练完成后**，你会获得一个类似 `ft:gpt-3.5-turbo:my-org:custom_suffix:abc123` 的模型 ID

---

#### 💻 使用 Fine-tuned 模型

```python
from openai import OpenAI

client = OpenAI()

# 使用你的自定义模型
response = client.chat.completions.create(
    model="ft:gpt-3.5-turbo:my-org:custom_suffix:abc123",  # 替换为你的模型 ID
    messages=[
        {"role": "user", "content": "写一个单例模式"}
    ]
)

print(response.choices[0].message.content)
```

**你会看到**：模型的回答风格和内容都更符合你的训练数据！

---

#### 💡 Fine-tuning 最佳实践

| 实践 | 说明 |
|------|------|
| **数据质量 > 数据数量** | 100 条高质量数据 > 1000 条低质量数据 |
| **统一风格** | 所有示例的回答风格保持一致 |
| **多样性** | 覆盖不同的场景和问题类型 |
| **System 提示词** | 在训练数据中始终包含 System 消息 |

---

### 🕐 第二部分：10分钟 - 评估指标详解

---

#### 📖 评估模型好坏的标准

---

#### 指标1：准确率（Accuracy）

**定义**：预测正确的样本占总样本的比例

```
准确率 = (预测正确数量) / (总数量)
```

**代码示例**：

```python
from sklearn.metrics import accuracy_score

# 真实标签
y_true = [0, 1, 0, 1, 0, 1]
# 预测标签
y_pred = [0, 1, 1, 1, 0, 1]

# 计算准确率
acc = accuracy_score(y_true, y_pred)
print(f"准确率：{acc:.2%}")
print(f"解释：6个样本中，5个预测正确，准确率 = 5/6 = 83.33%")
```

**问题**：当数据不平衡时，准确率会误导

**例子**：100 人中 1 个是病人，模型预测所有人健康
- 准确率 = 99%（看似很好）
- 但完全漏掉了病人（很糟糕！）

---

#### 指标2：精确率（Precision）& 召回率（Recall）

**混淆矩阵**：

| | 预测正类 | 预测负类 |
|---|---------|---------|
| **实际正类** | TP（真正例） | FN（假反例） |
| **实际负类** | FP（假正例） | TN（真反例） |

**精确率**：预测为正类的样本中，真正是正类的比例

```
精确率 = TP / (TP + FP)
含义：预测是"阳性"的，有多少是真的"阳性"
```

**召回率**：实际为正类的样本中，被正确预测为正类的比例

```
召回率 = TP / (TP + FN)
含义：所有"阳性"中，有多少被正确找出来了
```

**代码示例**：

```python
from sklearn.metrics import precision_score, recall_score

y_true = [1, 1, 0, 1, 0, 1, 0, 0]  # 1=阳性，0=阴性
y_pred = [1, 1, 1, 0, 0, 1, 0, 0]

precision = precision_score(y_true, y_pred)
recall = recall_score(y_true, y_pred)

print(f"精确率：{precision:.2%}")
print(f"召回率：{recall:.2%}")
print("\n解释：")
print("- 精确率 75%：预测为阳性的 4 个中，有 3 个真的是阳性")
print("- 召回率 60%：实际 4 个阳性中，只找到了 3 个，漏了 1 个")
```

---

#### 指标3：F1 分数

**定义**：精确率和召回率的调和平均数

```
F1 = 2 × (精确率 × 召回率) / (精确率 + 召回率)
```

**为什么用调和平均？**：平衡精确率和召回率，避免一个高一个低

**代码示例**：

```python
from sklearn.metrics import f1_score

y_true = [1, 1, 0, 1, 0, 1, 0, 0]
y_pred = [1, 1, 1, 0, 0, 1, 0, 0]

f1 = f1_score(y_true, y_pred)
precision = precision_score(y_true, y_pred)
recall = recall_score(y_true, y_pred)

print(f"精确率：{precision:.2%}")
print(f"召回率：{recall:.2%}")
print(f"F1 分数：{f1:.2%}")

print(f"\n计算验证：F1 = 2 × ({precision:.2} × {recall:.2}) / ({precision:.2} + {recall:.2}) = {f1:.2}")
```

---

#### 🎯 不同场景的优先级

| 场景 | 优先指标 | 原因 |
|------|---------|------|
| **癌症检测** | 召回率 > 精确率 | 宁可误报，不可漏诊 |
| **垃圾邮件过滤** | 精确率 > 召回率 | 宁可漏判，不可误删正常邮件 |
| **搜索引擎** | 召回率 + 精确率 | 需要平衡 |
| **推荐系统** | 精确率 | 用户喜欢看到的才推荐 |

---

## ✅ 今天您学到了什么

| 部分 | 知识点 | 掌握程度 |
|------|--------|---------|
| **大模型应用** | 理解 Fine-tuning 原理 | 🎯 已理解 |
| **大模型应用** | 准备 Fine-tuning 数据 | 🎯 已掌握 |
| **大模型应用** | RAG vs Fine-tuning 对比 | 🎯 已理解 |
| **机器学习基础** | 准确率、精确率、召回率 | 🎯 已理解 |
| **机器学习基础** | F1 分数的计算和意义 | 🎯 已理解 |
| **机器学习基础** | 不同场景的指标选择 | 🎯 已理解 |

---

## 📝 今天的作业（5分钟）

1. ✅ 运行 `prepare_finetuning_data.py`，生成训练数据
2. ✅ 添加更多 Java 模式示例到训练数据
3. ✅ 计算不同场景的精确率和召回率

---

## 🚀 下一步

```
第1天 → 第2天 → 第3天 → 第4天（今天）
Prompt → API → RAG → Fine-tuning ✅
基础 → 监督/无监督 → 算法 → 评估指标 ✅
```

---

## 📅 明天预告（第5天）

| 时间 | 内容 |
|------|------|
| 20分钟 | **大模型应用**：LangChain 基础 - 构建复杂 AI 应用 |
| 10分钟 | **机器学习基础**：过拟合 vs 欠拟合 - 模型的常见问题 |

**明天您将**：
- ✅ 了解 LangChain 是什么
- ✅ 用 LangChain 构建链式应用
- ✅ 理解过拟合和欠拟合的区别及解决方法

---

**学习进度**：📊 4/7 天完成

---

*最后更新：2026-03-20*
