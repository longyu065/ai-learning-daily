# AI 知识学习日报

> 每天学习时间：30分钟 | 模式：20分钟大模型应用 + 10分钟机器学习基础 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-22 周六 - 第6天（混合学习版）

---

### 🕐 第一部分：20分钟 - Agent 智能体基础

#### 🎯 今天目标：让 AI 自主思考和行动

---

#### 📖 什么是 Agent？

**Agent（智能体）**：能够自主感知、思考、决策并行动的 AI 系统

**核心差异**：

| 普通聊天机器人 | Agent 智能体 |
|-------------|-------------|
| ❌ 只能被动回答 | ✅ 主动思考和行动 |
| ❌ 无法使用工具 | ✅ 可以调用各种工具 |
| ❌ 单轮对话 | ✅ 多步骤复杂任务 |
| ❌ 无记忆或简单记忆 | ✅ 有完整的记忆和规划能力 |

**类比**：
- 普通聊天机器人：像客服，你问什么答什么
- Agent：像私人助理，可以自主安排任务、查资料、发邮件等

---

#### 🔄 Agent 工作流程

```
用户任务 → 感知 → 思考 → 规划 → 执行（调用工具）→ 反馈 → 迭代
```

**举例**：用户说"帮我分析昨天的销售数据"
1. **感知**：用户想要分析销售数据
2. **思考**：需要哪些数据？用什么工具？
3. **规划**：先获取数据，再用 Python 分析
4. **执行**：调用数据库工具获取数据
5. **执行**：调用代码工具分析数据
6. **反馈**：把分析结果返回给用户

---

#### 💻 LangChain Agent 示例（15分钟）

**创建文件** `simple_agent.py`：

```python
from langchain_openai import ChatOpenAI
from langchain.agents import initialize_agent, AgentType
from langchain.tools import Tool
from langchain_community.utilities import SerpAPIWrapper  # 搜索工具
from langchain_experimental.tools import PythonREPLTool   # 代码执行工具

# ========== 第一步：创建 LLM ==========
print("🤖 创建 Agent...")

llm = ChatOpenAI(
    model="gpt-4o-mini",
    temperature=0
)

# ========== 第二步：准备工具 ==========
print("🔧 准备工具...")

# 工具1：搜索
search = SerpAPIWrapper()

# 工具2：Python 代码执行
python_tool = PythonREPLTool()

# 工具列表
tools = [
    Tool(
        name="Search",
        func=search.run,
        description="用于搜索最新信息，输入：搜索关键词，输出：搜索结果"
    ),
    Tool(
        name="Python_REPL",
        func=python_tool.run,
        description="用于执行 Python 代码并返回结果，输入：Python 代码，输出：执行结果"
    )
]

# ========== 第三步：创建 Agent ==========
print("🧠 初始化 Agent...")

agent = initialize_agent(
    tools=tools,
    llm=llm,
    agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION,  # 最常用的 Agent 类型
    verbose=True  # 显示思考过程
)

# ========== 第四步：测试 Agent ==========
print("\n" + "=" * 50)
print("🎯 测试 Agent")
print("=" * 50)

# 任务1：搜索 + 计算
print("\n【任务1】查询当前比特币价格，并计算 100 美元能买多少")
print("-" * 50)

result1 = agent.run("""
查询当前比特币价格（美元），然后计算 100 美元能买多少个比特币？
保留 8 位小数。
""")

print(f"\n结果：{result1}")

# 任务2：数据分析
print("\n" + "=" * 50)
print("【任务2】分析一组数据")
print("-" * 50)

result2 = agent.run("""
有如下销售数据：
产品A: 100, 150, 200, 180, 220
产品B: 80, 90, 85, 95, 100

请用 Python 计算每个产品的平均销量，并输出哪个产品销量更好。
""")

print(f"\n结果：{result2}")
```

**运行**：
```bash
# 需要设置 SerpAPI 的 API Key
# 注册地址：https://serper.dev/

export SERPAPI_API_KEY=你的key
python simple_agent.py
```

**输出示例**：
```
🤖 创建 Agent...
🔧 准备工具...
🧠 初始化 Agent...

==================================================
🎯 测试 Agent
==================================================

【任务1】查询当前比特币价格，并计算 100 美元能买多少
--------------------------------------------------

> Entering new AgentExecutor chain...
I need to search for the current Bitcoin price in USD.
Action: Search
Action Input: current Bitcoin price USD
Observation: $67,432.18
Thought: Now I need to calculate how many Bitcoin can be bought with $100.
Action: Python_REPL
Action Input: print(100 / 67432.18)
Observation: 0.00148312
Thought: I now know the final answer.
Final Answer: 100美元可以购买约0.00148312个比特币

> Finished chain.

结果：100美元可以购买约0.00148312个比特币
```

---

#### 🎯 Agent 的核心概念

| 概念 | 说明 |
|------|------|
| **Tool（工具）** | Agent 可以调用的外部功能（搜索、代码、API 等） |
| **Chain（链）** | 固定的步骤序列（预设好每一步做什么） |
| **Agent（代理）** | 根据情况自主选择工具和步骤（动态决策） |

**Chain vs Agent**：

| 场景 | 用 Chain | 用 Agent |
|------|---------|---------|
| 1. 先总结，再翻译 | ✅ 固定步骤 | ❌ 不需要自主决策 |
| 2. 用户不确定需求 | ❌ 不确定步骤 | ✅ Agent 根据用户输入决策 |
| 3. 需要搜索 + 分析 + 总结 | ❌ 步骤太多 | ✅ Agent 自主规划 |

---

#### 🚀 实战：创建一个"学习助手 Agent"

**创建文件** `study_agent.py`：

```python
from langchain_openai import ChatOpenAI
from langchain.agents import initialize_agent, AgentType, Tool
from langchain.memory import ConversationBufferMemory
from langchain.prompts import PromptTemplate

# 创建 LLM
llm = ChatOpenAI(model="gpt-4o-mini")

# 创建记忆
memory = ConversationBufferMemory(
    memory_key="chat_history",
    return_messages=True
)

# 工具：解释概念
def explain_concept(concept):
    """解释一个技术概念"""
    from openai import OpenAI
    client = OpenAI()
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "你是技术老师，用简洁的语言解释概念。"},
            {"role": "user", "content": f"解释：{concept}"}
        ],
        max_tokens=200
    )
    return response.choices[0].message.content

# 工具：生成学习计划
def create_plan(topic, days=7):
    """生成学习计划"""
    from openai import OpenAI
    client = OpenAI()
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "你是学习规划专家，制定{days}天的学习计划。"},
            {"role": "user", "content": f"为{topic}制定{days}天的学习计划，每天要有具体任务。"}
        ],
        max_tokens=500
    )
    return response.choices[0].message.content

# 工具：推荐学习资源
def recommend_resources(topic):
    """推荐学习资源"""
    from openai import OpenAI
    client = OpenAI()
    response = client.chat.completions.create(
        model="gpt-4o-mini",
        messages=[
            {"role": "system", "content": "推荐高质量学习资源。"},
            {"role": "user", "content": f"推荐{topic}的学习资源，包括书籍、视频、网站。"}
        ],
        max_tokens=300
    )
    return response.choices[0].message.content

# 工具列表
tools = [
    Tool(
        name="Explain",
        func=explain_concept,
        description="解释技术概念，输入：概念名称，输出：通俗解释"
    ),
    Tool(
        name="CreatePlan",
        func=create_plan,
        description="生成学习计划，输入：主题和天数，输出：详细计划"
    ),
    Tool(
        name="Recommend",
        func=recommend_resources,
        description="推荐学习资源，输入：主题，输出：资源列表"
    )
]

# 创建 Agent
agent = initialize_agent(
    tools=tools,
    llm=llm,
    agent=AgentType.ZERO_SHOT_REACT_DESCRIPTION,
    memory=memory,
    verbose=True,
    agent_kwargs={
        "system_message": "你是一个专业的 AI 学习助手，帮助学生制定学习计划、解释概念、推荐资源。"
    }
)

# 交互式对话
print("🎓 学习助手 Agent 已启动！")
print("输入 'quit' 退出\n")

while True:
    user_input = input("你：")
    if user_input.lower() == "quit":
        print("再见！")
        break

    print("\nAgent 思考中...\n")
    response = agent.run(user_input)
    print(f"\n助手：{response}\n")
```

**运行**：
```bash
python study_agent.py
```

**对话示例**：
```
你：我想学机器学习

Agent 思考中...

> Entering new AgentExecutor chain...
用户想学机器学习，我应该：
1. 解释什么是机器学习
2. 制定学习计划
3. 推荐学习资源

Action: Explain
Action Input: 机器学习
Observation: 机器学习是让计算机从数据中学习规律...
Thought: 好的，已经解释了机器学习。现在制定学习计划。

Action: CreatePlan
Action Input: 机器学习, 7
Observation: 第1天：机器学习基础概念...
第2天：监督学习...
...
Thought: 计划制定好了。现在推荐资源。

Action: Recommend
Action Input: 机器学习
Observation: 1. 吴恩达《机器学习》课程...
2. 《机器学习实战》...
...

Thought: 我已经完成了所有任务，现在给用户总结。

Final Answer: 太棒了！机器学习是一个很棒的选择。
...
（详细的回答）
> Finished chain.

助手：太棒了！机器学习是一个很棒的选择。
...
```

---

### 🕐 第二部分：10分钟 - 正则化

---

#### 📖 防止过拟合的利器

---

#### 什么是正则化？

**定义**：在损失函数中加入"惩罚项"，限制模型复杂度

**原理**：让模型不仅追求"拟合训练数据"，还要保持"简单"

**数学形式**：

**普通损失函数**：
```
Loss = 训练误差
```

**L1 正则化（Lasso）**：
```
Loss = 训练误差 + λ × |权重|
```

**L2 正则化（Ridge）**：
```
Loss = 训练误差 + λ × 权重²
```

其中：
- `λ`：正则化强度（越大，模型越简单）
- `|权重|`：权重的绝对值
- `权重²`：权重的平方

---

#### L1 vs L2 正则化

| 特性 | L1 正则化 | L2 正则化 |
|------|----------|----------|
| **惩罚项** | 权重的绝对值 | 权重的平方 |
| **效果** | 产生稀疏解（很多权重为0） | 权重都变小，但不为0 |
| **应用** | 特征选择 | 防止过拟合 |
| **例子** | 100 个特征 → 选出 10 个重要 | 100 个特征 → 所有特征都保留，但权重减小 |

**类比**：
- L1 正则化：像"断舍离"，只保留重要的东西
- L2 正则化：像"减肥"，让所有东西都变轻一点

---

#### 💻 代码示例

```python
from sklearn.linear_model import Ridge, Lasso
from sklearn.datasets import make_regression
from sklearn.model_selection import train_test_split
import numpy as np

# 生成数据（100 个特征，但只有 10 个是有效的）
np.random.seed(42)
X, y = make_regression(n_samples=100, n_features=100, n_informative=10, noise=10)

# 拆分数据
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

# 模型1：普通线性回归（无正则化）
from sklearn.linear_model import LinearRegression
model_no_reg = LinearRegression()
model_no_reg.fit(X_train, y_train)

# 模型2：L2 正则化（Ridge）
model_l2 = Ridge(alpha=1.0)  # alpha 就是 λ
model_l2.fit(X_train, y_train)

# 模型3：L1 正则化（Lasso）
model_l1 = Lasso(alpha=1.0)
model_l1.fit(X_train, y_train)

# 评估
print("模型对比：")
print("-" * 50)
print(f"无正则化 - 训练误差: {model_no_reg.score(X_train, y_train):.3f}, 测试误差: {model_no_reg.score(X_test, y_test):.3f}")
print(f"L2 正则化 - 训练误差: {model_l2.score(X_train, y_train):.3f}, 测试误差: {model_l2.score(X_test, y_test):.3f}")
print(f"L1 正则化 - 训练误差: {model_l1.score(X_train, y_train):.3f}, 测试误差: {model_l1.score(X_test, y_test):.3f}")

# 查看 L1 正则化的特征选择效果
print("\nL1 正则化后的权重分布：")
non_zero_weights = np.sum(model_l1.coef_ != 0)
print(f"非零权重数量：{non_zero_weights} / 100")
print(f"解释：L1 正则化从 100 个特征中自动选择了 {non_zero_weights} 个重要的")
```

**输出示例**：
```
模型对比：
--------------------------------------------------
无正则化 - 训练误差: 1.000, 测试误差: 0.650  （过拟合！）
L2 正则化 - 训练误差: 0.950, 测试误差: 0.720  （正常）
L1 正则化 - 训练误差: 0.890, 测试误差: 0.780  （特征选择）

L1 正则化后的权重分布：
非零权重数量：12 / 100
解释：L1 正则化从 100 个特征中自动选择了 12 个重要的
```

---

#### 🎯 正则化参数 λ 的选择

| λ 过小 | λ 合适 | λ 过大 |
|--------|--------|--------|
| 无正则化效果 | 防止过拟合 | 欠拟合 |

**代码：交叉验证选择 λ**：

```python
from sklearn.linear_model import RidgeCV

# 使用交叉验证自动选择最佳的 λ
model_cv = RidgeCV(
    alphas=[0.01, 0.1, 1.0, 10.0, 100.0],  # 候选的 λ 值
    cv=5  # 5 折交叉验证
)
model_cv.fit(X_train, y_train)

print(f"自动选择的最佳 λ：{model_cv.alpha_}")
print(f"测试集准确率：{model_cv.score(X_test, y_test):.3f}")
```

---

## ✅ 今天您学到了什么

| 部分 | 知识点 | 掌握程度 |
|------|--------|---------|
| **大模型应用** | Agent 智能体的概念 | 🎯 已理解 |
| **大模型应用** | LangChain Agent 使用 | 🎯 已掌握 |
| **大模型应用** | 创建学习助手 Agent | 🎯 已掌握 |
| **机器学习基础** | 正则化的原理 | 🎯 已理解 |
| **机器学习基础** | L1 vs L2 正则化区别 | 🎯 已理解 |
| **机器学习基础** | 如何选择正则化参数 | 🎯 已理解 |

---

## 📝 今天的作业（5分钟）

1. ✅ 运行 `simple_agent.py`，测试 Agent 的工具调用能力
2. ✅ 修改 `study_agent.py`，添加更多工具
3. ✅ 对比 L1 和 L2 正则化的效果

---

## 🚀 最后一天预告！

```
第6天（今天）
├─ Agent 智能体 ✅
└─ 正则化 ✅

第7天（明天 - 周日）
└─ 🎉 综合实战项目 + 面试复习
```

---

## 📅 明天预告（第7天 - 最后一天！）

| 时间 | 内容 |
|------|------|
| 20分钟 | **大模型应用**：综合实战项目 - 用所学知识构建完整应用 |
| 10分钟 | **机器学习基础**：面试题复习 - 梳理一周重点知识 |

**明天您将**：
- ✅ 综合运用 Prompt、API、RAG、LangChain、Agent
- ✅ 构建一个完整的 AI 应用
- ✅ 复习所有核心知识点
- ✅ 准备面试！

---

**学习进度**：📊 6/7 天完成！明天就是最后一天了！ 🎉

---

*最后更新：2026-03-22*
