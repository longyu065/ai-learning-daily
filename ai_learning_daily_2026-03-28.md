# AI 知识学习日报

> 每天学习时间：30分钟 | 模式：20分钟大模型应用 + 10分钟机器学习基础 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-28 周五 - 第5天

---

### 🎯 今日学习目标

| 部分 | 时间 | 主题 |
|------|------|------|
| 🕐 20分钟 | **大模型应用** | 高级 Prompt 技巧 |
| 🕐 10分钟 | **机器学习基础** | 大语言模型原理 |

---

## 📖 第一部分：20分钟 - 高级 Prompt 技巧

---

### 1. Few-shot Learning（少样本学习）

**定义**：通过几个示例教会模型如何完成任务

**为什么有效**：
- 模型从示例中学习模式
- 比纯文字描述更清晰
- 减少歧义

---

#### ✅ 基础示例

```python
prompt = """
任务：将句子转换成幽默版本

示例1:
输入: "今天天气不错。"
输出: "今天天气好到我想去外太空散步！"

示例2:
输入: "这道菜有点辣。"
输出: "这道菜辣到我的舌头都要着火了！"

输入: "这个功能很实用。"
输出:
"""

# 模型会根据示例模式生成幽默版本
```

---

#### 🎯 Few-shot 分类

```python
prompt = """
任务：判断评论的情感（正面/负面）

示例:
"这个产品非常好，强烈推荐！" -> 正面
"质量太差了，退货！" -> 负面
"还行，没什么特别的。" -> 中性
"客服态度很差，不推荐。" -> 负面
"超出预期，性价比很高！" -> 正面

"这个APP经常崩溃，用得我很烦。" ->
"""

# 输出：负面
```

---

#### 🎯 Few-shot 代码生成

```python
prompt = """
任务：将 SQL 查询转换为 Python 代码

示例1:
SQL: SELECT * FROM users WHERE age > 18
Python:
import sqlite3
conn = sqlite3.connect('db.sqlite')
cursor = conn.cursor()
cursor.execute("SELECT * FROM users WHERE age > 18")
results = cursor.fetchall()

示例2:
SQL: INSERT INTO orders (user_id, amount) VALUES (1, 99.99)
Python:
import sqlite3
conn = sqlite3.connect('db.sqlite')
cursor = conn.cursor()
cursor.execute("INSERT INTO orders (user_id, amount) VALUES (1, 99.99)")
conn.commit()

SQL: UPDATE users SET status = 'active' WHERE id = 1
Python:
"""
```

---

### 2. Chain-of-Thought（思维链）

**定义**：让模型分步骤思考

**适用场景**：
- 数学计算
- 逻辑推理
- 复杂问题

---

#### 📊 数学计算

```python
# ❌ 直接提问
prompt = "计算 23 × 45 + 67 ÷ 3"
# 可能出错

# ✅ 思维链
prompt = """
计算：23 × 45 + 67 ÷ 3

请分步骤计算：
步骤1：计算 23 × 45
步骤2：计算 67 ÷ 3
步骤3：将步骤1和步骤2的结果相加
步骤4：给出最终答案

请按步骤输出：
步骤1：...
步骤2：...
步骤3：...
步骤4：...
"""

# 输出：
# 步骤1：23 × 45 = 1035
# 步骤2：67 ÷ 3 ≈ 22.333
# 步骤3：1035 + 22.333 = 1057.333
# 步骤4：最终答案 ≈ 1057.333
```

---

#### 🔍 代码调试

```python
prompt = """
以下代码在处理大数据时很慢，请帮我分析并优化：

```python
def get_users_by_ids(ids):
    users = []
    for user_id in ids:
        user = db.query(f"SELECT * FROM users WHERE id = {user_id}")
        users.append(user)
    return users
```

请按以下步骤分析：
步骤1：找出代码的性能问题
步骤2：解释为什么慢
步骤3：提供优化方案
步骤4：给出优化后的代码
"""
```

---

### 3. Few-shot CoT（少样本 + 思维链）

**结合两者，效果更好**

```python
prompt = """
任务：解决数学应用题

示例1:
问题：小明有15个苹果，吃了3个，又买了5个，现在有多少个？
思考：
- 初始：15个
- 吃了：15 - 3 = 12个
- 买了：12 + 5 = 17个
答案：17个

示例2:
问题：一个长方形的长是10cm，宽是5cm，面积是多少？
思考：
- 面积公式：长 × 宽
- 计算：10 × 5 = 50
答案：50平方厘米

问题：张三有3本书，李四的书是张三的2倍，王五的书比李四多2本，三人一共有多少本？
思考：
"""
```

---

### 4. Self-Consistency（自洽性）

**定义**：生成多个答案，选择最一致的

**步骤**：
1. 生成多个推理路径
2. 统计每个答案的出现次数
3. 选择最频繁的答案

---

#### 📊 示例

```python
# 多次采样
responses = []
for _ in range(5):
    response = generate(prompt, temperature=0.8)  # 高温度增加多样性
    responses.append(extract_answer(response))

# 统计
answer_counts = Counter(responses)
most_common = answer_counts.most_common(1)[0][0]

print(f"最终答案: {most_common}")
```

---

### 5. Generated Knowledge（生成知识）

**定义**：先生成相关知识，再回答问题

**优势**：
- 补充背景知识
- 提高答案质量

---

#### 📚 示例

```python
prompt = """
任务：回答以下问题

步骤1：生成与问题相关的知识
步骤2：基于知识回答问题

问题：为什么深度学习需要GPU？

请按步骤回答：

步骤1（相关知识）：
"""
```

**模型输出**：
```
步骤1（相关知识）：
- GPU 有更多核心，适合并行计算
- 深度学习需要大量矩阵运算，可以并行化
- GPU 专用显存，速度快
- 深度学习框架（PyTorch, TensorFlow）针对GPU优化

步骤2（回答）：
深度学习需要GPU的原因：
1. 并行计算能力强：GPU有数千个核心，可同时处理大量矩阵运算
2. 大规模矩阵运算：深度学习的核心是矩阵乘法，GPU效率是CPU的10-100倍
3. 专用显存：GPU有高速显存，减少数据传输延迟
4. 框架优化：PyTorch、TensorFlow等框架针对GPU优化，充分利用硬件
```

---

### 6. Refinement（迭代优化）

**定义**：先生成初稿，再逐步改进

**适用于**：
- 长文本生成
- 代码生成
- 文章写作

---

#### 📝 示例

```python
# 第一轮：生成初稿
prompt1 = """
写一段介绍机器学习的文字，大约200字。
"""
draft = generate(prompt1)

# 第二轮：优化
prompt2 = f"""
以下是我写的初稿：

{draft}

请帮我优化：
1. 使语言更简洁
2. 使用更准确的术语
3. 增加一个具体例子

优化后的文本：
"""
refined = generate(prompt2)

# 第三轮：格式化
prompt3 = f"""
以下文本需要格式化为Markdown格式：

{refined}

请添加：
- 小标题
- 列表
- 重点标记

格式化后的文本：
"""
final = generate(prompt3)
```

---

### 7. 模板：高质量 Prompt 结构

```python
def create_high_quality_prompt(task, context, examples, format, constraints):
    """创建高质量 Prompt"""
    prompt = f"""
## 角色设定
你是一位 {role}，擅长 {expertise}。

## 任务描述
{task}

## 上下文信息
{context}

## 示例
{examples}

## 输出格式
{format}

## 约束条件
{constraints}

## 注意事项
1. ...
2. ...
3. ...

现在请完成任务：
"""
    return prompt

# 使用
prompt = create_high_quality_prompt(
    role="Java 代码审查专家",
    expertise="识别性能问题和安全隐患",
    task="审查以下代码，找出所有问题",
    context="这是一个高并发的电商系统代码",
    examples="[示例1...]\n[示例2...]",
    format="| 问题 | 严重程度 | 说明 | 修复方案 |",
    constraints="- 必须检查并发问题\n- 必须检查SQL注入\n- 必须检查空指针"
)
```

---

## 📖 第二部分：10分钟 - 大语言模型原理

---

### 什么是 LLM？

**LLM = Large Language Model**

**定义**：在大规模文本数据上训练的大型神经网络

---

### LLM 训练三阶段

```
阶段1: 预训练（Pre-training）
  └─ 学习语言规律
  └─ 需要海量数据

阶段2: 指令微调（Instruction Fine-tuning）
  └─ 学习遵循指令
  └─ 需要指令-响应对

阶段3: RLHF（基于人类反馈的强化学习）
  └─ 学习人类偏好
  └─ 需要人类偏好数据
```

---

### 1. 预训练（Pre-training）

**任务**：预测下一个词

```
输入: "人工智能的"
输出: "未来" (概率: 30%)
       "发展" (概率: 25%)
       ...
```

**训练过程**：
1. 收集海量文本（万亿级token）
2. 每次让模型预测下一个词
3. 如果预测正确，调整参数
4. 重复数十亿次

---

### 2. 指令微调（Instruction Fine-tuning）

**目的**：让模型理解并遵循指令

**数据格式**：
```
指令: 用一句话解释什么是机器学习。
输入: (可选)
输出: 机器学习是让计算机从数据中学习规律的AI分支。
```

**流程**：
1. 收集指令数据
2. 继续训练模型
3. 模型学会"遵循指令"

---

### 3. RLHF

**目的**：对齐人类偏好

**三步骤**：

```
步骤1: 收集人类偏好
       → 比较两个回答（A vs B）
       → 标注哪个更好

步骤2: 训练奖励模型
       → 学习人类偏好

步骤3: PPO 微调
       → 最大化奖励
```

---

### Transformer 架构

**核心**：自注意力机制

```
Attention(Q, K, V) = softmax(QK^T / √d_k)V
```

**优势**：
- 并行计算
- 捕捉长距离依赖
- 可解释性强

---

## ✅ 今天你学到了什么

| 部分 | 知识点 | 掌握程度 |
|------|--------|---------|
| 高级 Prompt | Few-shot Learning | 🎯 已掌握 |
| | Chain-of-Thought | 🎯 已掌握 |
| | Few-shot CoT | 🎯 已了解 |
| | Self-Consistency | 🎯 已了解 |
| | Generated Knowledge | 🎯 已了解 |
| | Refinement | 🎯 已了解 |
| | 高质量 Prompt 结构 | 🎯 已掌握 |
| LLM 原理 | LLM 定义 | 🎯 已理解 |
| | 训练三阶段 | 🎯 已理解 |
| | 预训练 | 🎯 已理解 |
| | 指令微调 | 🎯 已理解 |
| | RLHF | 🎯 已了解 |
| | Transformer | 🎯 已了解 |

---

## 📝 今天的作业

1. ✅ 使用 Few-shot Learning 让模型完成特定任务
2. ✅ 尝试 Chain-of-Thought 解决复杂问题
3. ✅ 理解 LLM 的训练过程

---

## 🚀 明天预告

| 时间 | 主题 |
|------|------|
| 🕐 20分钟 | Agent 智能体 |
| 🕐 10分钟 | AI 工程化 |

---

**学习进度**：📊 第5天/第3周

---

*最后更新：2026-03-28*
