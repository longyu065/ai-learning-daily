# AI 知识学习日报

> 每天学习时间：30分钟 | 模式：20分钟大模型应用 + 10分钟机器学习基础 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-21 周五 - 第5天（混合学习版）

---

### 🕐 第一部分：20分钟 - LangChain 基础

#### 🎯 今天目标：用 LangChain 构建链式 AI 应用

---

#### 📖 什么是 LangChain？

**LangChain**：一个用于构建 LLM 应用的框架

**核心价值**：
- 统一的接口：不同模型用相同的方式调用
- 链式调用：把多个步骤串联起来
- 丰富的组件：记忆、工具、代理等

**类比**：
- 没有 LangChain：像手动拼装电脑，需要自己连接各个部件
- 有 LangChain：像用乐高积木，即插即用

---

#### 🔄 LangChain 核心概念

| 概念 | 说明 | 类比 |
|------|------|------|
| **Chain（链）** | 把多个步骤串联起来 | 工厂流水线 |
| **Memory（记忆）** | 保存对话历史 | 记忆本 |
| **Tool（工具）** | 让 AI 调用外部工具 | 工具箱 |
| **Agent（代理）** | 让 AI 自主决策和行动 | 智能管家 |

---

#### 🔧 环境准备（2分钟）

```bash
pip install langchain langchain-openai
```

---

#### 💻 第一个 Chain：简单链（5分钟）

**创建文件** `simple_chain.py`：

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

# ========== 第一步：创建 LLM ==========
print("🔌 连接 GPT...")

llm = ChatOpenAI(
    model="gpt-4o-mini",
    temperature=0.7
)

# ========== 第二步：创建提示词模板 ==========
print("📝 创建提示词模板...")

prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个{role}，擅长用{style}风格写作。"),
    ("user", "{input}")
])

# ========== 第三步：创建输出解析器 ==========
parser = StrOutputParser()

# ========== 第四步：组装链 ==========
print("🔗 组装链...")

chain = prompt | llm | parser  # | 符号表示链式调用

# ========== 第五步：运行链 ==========
print("\n运行链...\n")

result = chain.invoke({
    "role": "Java 技术专家",
    "style": "幽默风趣",
    "input": "解释什么是接口"
})

print("AI 回答：")
print("=" * 50)
print(result)
print("=" * 50)
```

**运行**：
```bash
python simple_chain.py
```

**输出示例**：
```
🔌 连接 GPT...
📝 创建提示词模板...
🔗 组装链...

运行链...

AI 回答：
==================================================
接口就像一份"工作说明书"或"合同"。

比如说，公司发布一个"高级工程师"的职位接口，上面写明：
- 必须会 Java
- 必须有 3 年经验
- 必须能带团队

这就是接口！不管你是张三还是李四，只要你满足这些要求（实现了接口），就能胜任这个职位。

好处是什么？
- 面试官（调用者）不需要知道你是谁，只要看说明书
- 可以随时换人，不影响系统运行
- 多个人可以实现同一个接口（多态）

简单说：接口 = "我要你做什么"，具体怎么做由你决定！
==================================================
```

---

#### 💻 第二个 Chain：带记忆的链（8分钟）

**创建文件** `memory_chain.py`：

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser
from langchain_core.messages import HumanMessage, AIMessage

# ========== 第一步：创建 LLM ==========
llm = ChatOpenAI(model="gpt-4o-mini")

# ========== 第二步：定义提示词模板 ==========
prompt = ChatPromptTemplate.from_messages([
    ("system", "你是一个友好的 AI 助手，记住用户的对话历史。"),
    # 占位符：chat_history 会被替换为对话历史
    ("placeholder", "{chat_history}"),
    ("human", "{input}")
])

# ========== 第三步：创建链 ==========
chain = prompt | llm | StrOutputParser()

# ========== 第四步：模拟多轮对话 ==========
print("💬 开始对话...\n")

# 对话历史
chat_history = []

# 第一轮对话
print("【用户】：你好，我叫小明，是一名程序员")
response1 = chain.invoke({
    "chat_history": chat_history,
    "input": "你好，我叫小明，是一名程序员"
})
print(f"【AI】：{response1}\n")

# 保存到历史
chat_history.extend([
    HumanMessage(content="你好，我叫小明，是一名程序员"),
    AIMessage(content=response1)
])

# 第二轮对话
print("【用户】：我想学 AI")
response2 = chain.invoke({
    "chat_history": chat_history,
    "input": "我想学 AI"
})
print(f"【AI】：{response2}\n")

# 第三轮对话
print("【用户】：推荐一些学习资源")
response3 = chain.invoke({
    "chat_history": chat_history,
    "input": "推荐一些学习资源"
})
print(f"【AI】：{response3}\n")

print("=" * 50)
print("对话总结：")
print(f"- AI 记住了你是：程序员小明")
print(f"- AI 知道你想学：AI")
print(f"- AI 推荐了：学习资源")
print("=" * 50)
```

**运行**：
```bash
python memory_chain.py
```

**输出示例**：
```
💬 开始对话...

【用户】：你好，我叫小明，是一名程序员
【AI】：你好小明！很高兴认识你！作为程序员，你对技术肯定很感兴趣吧？有什么我可以帮你的吗？

【用户】：我想学 AI
【AI】：太棒了！作为程序员，学 AI 有天然优势。你可以从 Python、机器学习基础开始，然后接触深度学习和大模型。想了解具体的学习路线吗？

【用户】：推荐一些学习资源
【AI】：好的小明，根据你的程序员背景，我推荐：

1. **吴恩达的机器学习课程**（Coursera）- 基础必学
2. **Fast.ai** - 实战导向
3. **Hugging Face 文档** - 学习使用模型
4. **OpenAI API 文档** - 快速上手应用
5. **LangChain 教程** - 构建复杂应用

建议从 1 和 4 开始，边学边实践！

==================================================
对话总结：
- AI 记住了你是：程序员小明
- AI 知道你想学：AI
- AI 推荐了：学习资源
==================================================
```

---

#### 🚀 实战：构建一个"代码解释链"

**创建文件** `code_explainer_chain.py`：

```python
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.output_parsers import StrOutputParser

llm = ChatOpenAI(model="gpt-4o-mini")

# 第一步：代码总结
summarize_prompt = ChatPromptTemplate.from_template("""
用一句话总结以下代码的功能：
```{language}
{code}
```
只输出总结，不要多余内容。
""")

# 第二步：解释代码
explain_prompt = ChatPromptTemplate.from_template("""
你是编程老师，请用{style}风格解释以下{language}代码：

代码功能：{summary}

代码：
```{language}
{code}
```

要求：
- 解释每行关键代码的作用
- 用{style}风格
- 适合{level}水平的读者
""")

# 第三步：生成测试用例
test_prompt = ChatPromptTemplate.from_template("""
为以下{language}代码生成3个测试用例：

代码功能：{summary}

代码：
```{language}
{code}
```

要求：
- 包含正常情况
- 包含边界情况
- 包含异常情况
输出格式：
1. 用例名称
   输入：...
   期望输出：...
""")

# 创建链
summarize_chain = summarize_prompt | llm | StrOutputParser()
explain_chain = explain_prompt | llm | StrOutputParser()
test_chain = test_prompt | llm | StrOutputParser()

# 组合链
full_chain = {
    "summary": summarize_chain,
    "code": lambda x: x["code"],
    "language": lambda x: x["language"],
    "style": lambda x: x["style"],
    "level": lambda x: x["level"]
} | {
    "explanation": explain_chain,
    "tests": test_chain
}

# 运行
java_code = """
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }

    public int divide(int a, int b) {
        if (b == 0) {
            throw new IllegalArgumentException("除数不能为0");
        }
        return a / b;
    }
}
"""

print("🔍 代码分析...\n")
result = full_chain.invoke({
    "code": java_code,
    "language": "Java",
    "style": "幽默风趣",
    "level": "初级"
})

print("=" * 50)
print("📝 代码解释：")
print(result["explanation"])
print("\n" + "=" * 50)
print("🧪 测试用例：")
print(result["tests"])
print("=" * 50)
```

---

### 🕐 第二部分：10分钟 - 过拟合 vs 欠拟合

---

#### 📖 模型的两个极端问题

---

#### 什么是过拟合（Overfitting）？

**定义**：模型在训练数据上表现很好，但在新数据上表现很差

**类比**：
- 就像学生背题，把练习题答案都背下来了
- 考试换了一道类似的题，就不会做了

**原因**：
- 模型太复杂（记住了噪音）
- 训练数据太少
- 训练时间过长

**代码示例**：

```python
import numpy as np
import matplotlib.pyplot as plt
from sklearn.preprocessing import PolynomialFeatures
from sklearn.linear_model import LinearRegression
from sklearn.pipeline import make_pipeline

# 生成数据（正弦函数 + 噪音）
np.random.seed(42)
x = np.random.uniform(-3, 3, 20)
y = np.sin(x) + np.random.normal(0, 0.1, 20)

# 模拟过拟合（使用 10 次多项式）
model_overfit = make_pipeline(
    PolynomialFeatures(10),  # 太复杂
    LinearRegression()
)
model_overfit.fit(x.reshape(-1, 1), y)

# 绘制结果
x_plot = np.linspace(-3, 3, 100)
y_overfit = model_overfit.predict(x_plot.reshape(-1, 1))

plt.figure(figsize=(12, 4))

plt.subplot(1, 3, 1)
plt.scatter(x, y, label="真实数据")
plt.plot(x_plot, y_overfit, 'r', label="过拟合模型")
plt.title("过拟合：模型太复杂")
plt.legend()

# 正常拟合
model_good = make_pipeline(
    PolynomialFeatures(3),  # 合适复杂度
    LinearRegression()
)
model_good.fit(x.reshape(-1, 1), y)
y_good = model_good.predict(x_plot.reshape(-1, 1))

plt.subplot(1, 3, 2)
plt.scatter(x, y)
plt.plot(x_plot, y_good, 'g', label="正常模型")
plt.title("正常：模型复杂度合适")
plt.legend()

# 欠拟合
model_underfit = make_pipeline(
    PolynomialFeatures(1),  # 太简单
    LinearRegression()
)
model_underfit.fit(x.reshape(-1, 1), y)
y_underfit = model_underfit.predict(x_plot.reshape(-1, 1))

plt.subplot(1, 3, 3)
plt.scatter(x, y)
plt.plot(x_plot, y_underfit, 'b', label="欠拟合模型")
plt.title("欠拟合：模型太简单")
plt.legend()

plt.tight_layout()
plt.show()
```

**你会看到**：
- 左图：红线扭曲，试图通过每个点（过拟合）
- 中图：绿线平滑，捕捉趋势（正常）
- 右图：蓝线是直线，无法捕捉曲线（欠拟合）

---

#### 如何判断过拟合/欠拟合？

| 指标 | 过拟合 | 正常 | 欠拟合 |
|------|--------|------|--------|
| **训练误差** | 很低 | 低 | 高 |
| **测试误差** | 很高 | 低 | 高 |
| **差距** | 很大 | 小 | 小 |

**代码检测**：

```python
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error

# 生成数据
np.random.seed(42)
x = np.random.uniform(-3, 3, 100)
y = np.sin(x) + np.random.normal(0, 0.1, 100)

# 拆分数据
x_train, x_test, y_train, y_test = train_test_split(x, y, test_size=0.2)

# 模型1：欠拟合（线性）
model_under = LinearRegression()
model_under.fit(x_train.reshape(-1, 1), y_train)
train_error_under = mean_squared_error(y_train, model_under.predict(x_train.reshape(-1, 1)))
test_error_under = mean_squared_error(y_test, model_under.predict(x_test.reshape(-1, 1)))

# 模型2：过拟合（高次多项式）
model_over = make_pipeline(
    PolynomialFeatures(15),
    LinearRegression()
)
model_over.fit(x_train.reshape(-1, 1), y_train)
train_error_over = mean_squared_error(y_train, model_over.predict(x_train.reshape(-1, 1)))
test_error_over = mean_squared_error(y_test, model_over.predict(x_test.reshape(-1, 1)))

print("模型诊断：")
print("-" * 40)
print(f"欠拟合模型：训练误差 {train_error_under:.3f}, 测试误差 {test_error_under:.3f}")
print(f"过拟合模型：训练误差 {train_error_over:.3f}, 测试误差 {test_error_over:.3f}")
print()
print("分析：")
print("- 欠拟合：训练和测试误差都高（模型太简单）")
print("- 过拟合：训练误差很低，测试误差很高（记住了噪音）")
```

---

#### 🎯 解决方法

| 问题 | 解决方法 |
|------|---------|
| **过拟合** | 减少模型复杂度<br>增加训练数据<br>使用正则化（明天讲）<br>早停（Early Stopping） |
| **欠拟合** | 增加模型复杂度<br>添加更多特征<br>减少正则化强度 |

---

## ✅ 今天您学到了什么

| 部分 | 知识点 | 掌握程度 |
|------|--------|---------|
| **大模型应用** | LangChain 的核心概念 | 🎯 已理解 |
| **大模型应用** | 创建简单的 Chain | 🎯 已掌握 |
| **大模型应用** | 带记忆的 Chain | 🎯 已掌握 |
| **大模型应用** | 构建代码解释链 | 🎯 已掌握 |
| **机器学习基础** | 过拟合的定义和原因 | 🎯 已理解 |
| **机器学习基础** | 欠拟合的定义和原因 | 🎯 已理解 |
| **机器学习基础** | 如何诊断和解决 | 🎯 已理解 |

---

## 📝 今天的作业（5分钟）

1. ✅ 运行 3 个 Chain 示例
2. ✅ 修改代码解释链，支持 Python 代码
3. ✅ 画出过拟合/欠拟合的可视化图

---

## 🚀 下一步

```
第1-5天：基础阶段 ✅
├─ Prompt Engineering
├─ API 调用
├─ RAG
├─ Fine-tuning
└─ LangChain

第6-7天：进阶阶段
├─ Agent 智能体
└─ 综合实战
```

---

## 📅 明天预告（第6天）

| 时间 | 内容 |
|------|------|
| 20分钟 | **大模型应用**：Agent 智能体 - 让 AI 自主行动 |
| 10分钟 | **机器学习基础**：正则化 - 防止过拟合的利器 |

**明天您将**：
- ✅ 了解 Agent 是什么
- ✅ 用 LangChain 构建简单 Agent
- ✅ 学习 L1/L2 正则化

---

**学习进度**：📊 5/7 天完成

---

*最后更新：2026-03-21*
