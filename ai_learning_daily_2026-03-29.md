# AI 知识学习日报

> 每天学习时间：30分钟 | 模式：20分钟大模型应用 + 10分钟机器学习基础 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-29 周六 - 第6天

---

### 🎯 今日学习目标

| 部分 | 时间 | 主题 |
|------|------|------|
| 🕐 20分钟 | **大模型应用** | Agent 智能体 |
| 🕐 10分钟 | **机器学习基础** | AI 工程化 |

---

## 📖 第一部分：20分钟 - Agent 智能体

---

### 1. 什么是 Agent？

**Agent = 智能体**

**定义**：能够感知环境、思考决策、执行行动的 AI 系统

**核心能力**：
```
感知 → 思考 → 行动 → 反馈
  ↓      ↓      ↓      ↓
输入   LLM   工具调用   结果
```

---

### 2. Agent vs 传统 AI

| 维度 | 传统 AI | Agent |
|------|---------|-------|
| **输入** | 用户输入 | 环境感知 + 用户输入 |
| **处理** | 单次推理 | 多轮思考 |
| **输出** | 文本回答 | 工具调用 + 文字 |
| **能力** | 问答 | 自动化任务 |
| **自主性** | 低 | 高 |

---

### 3. Agent 的核心组件

```
┌─────────────────────────────┐
│         Agent 架构            │
├─────────────────────────────┤
│  1. 记忆（Memory）           │
│     ├─ 短期记忆              │
│     └─ 长期记忆              │
│                              │
│  2. 规划（Planning）         │
│     ├─ 任务分解              │
│     └─ 步骤规划              │
│                              │
│  3. 工具（Tools）            │
│     ├─ 搜索引擎              │
│     ├─ 代码执行              │
│     └─ API 调用              │
│                              │
│  4. 反思（Reflection）       │
│     └─ 自我纠正              │
└─────────────────────────────┘
```

---

### 4. 简单 Agent 实现

#### 🤖 代码助手 Agent

```python
from openai import OpenAI
import subprocess
import json

class CodeAgent:
    """代码助手 Agent"""

    def __init__(self):
        self.client = OpenAI()
        self.memory = []

    def remember(self, key, value):
        """保存记忆"""
        self.memory.append({"key": key, "value": value})

    def think(self, task):
        """思考任务"""
        # 构建包含记忆的 prompt
        memory_str = "\n".join([f"- {m['key']}: {m['value']}" for m in self.memory])

        prompt = f"""
你是一个智能代码助手。

记住的信息：
{memory_str}

任务：{task}

请分析任务并选择行动：
1. 如果需要搜索信息，输出: SEARCH: [查询词]
2. 如果需要运行代码，输出: RUN: [代码]
3. 如果需要解释概念，输出: EXPLAIN: [内容]
4. 如果完成任务，输出: DONE: [结果]

你的回答：
"""

        response = self.client.chat.completions.create(
            model="gpt-3.5-turbo",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.3
        )

        return response.choices[0].message.content

    def act(self, thought):
        """执行行动"""
        if thought.startswith("SEARCH:"):
            query = thought[7:].strip()
            result = self.search(query)
            self.remember(f"搜索结果: {query}", result)
            return result

        elif thought.startswith("RUN:"):
            code = thought[4:].strip()
            result = self.run_code(code)
            self.remember(f"代码执行", result)
            return result

        elif thought.startswith("EXPLAIN:"):
            explanation = thought[8:].strip()
            self.remember("解释", explanation)
            return explanation

        elif thought.startswith("DONE:"):
            result = thought[5:].strip()
            return result

        return thought

    def search(self, query):
        """搜索信息（模拟）"""
        # 实际应用中可以调用搜索引擎或 RAG
        return f"搜索到关于 '{query}' 的信息..."

    def run_code(self, code):
        """运行代码"""
        try:
            result = subprocess.run(
                ["python", "-c", code],
                capture_output=True,
                text=True,
                timeout=10
            )
            return result.stdout or result.stderr
        except Exception as e:
            return str(e)

    def execute(self, task):
        """执行任务"""
        print(f"\n📋 任务: {task}")

        iteration = 0
        while iteration < 5:  # 最多5次迭代
            iteration += 1
            print(f"\n🧠 思考 {iteration}:")

            # 思考
            thought = self.think(task)
            print(f"   {thought}")

            # 执行
            if thought.startswith("DONE:"):
                return thought[5:].strip()

            result = self.act(thought)
            print(f"   ✅ 执行结果: {result}")

            # 更新任务
            task += f"\n\n之前的尝试: {thought}\n结果: {result}"

        return "任务未完成，请提供更多信息"

# 使用
agent = CodeAgent()
result = agent.execute("计算斐波那契数列的前10项")
print(f"\n🎯 最终结果: {result}")
```

---

### 5. 高级 Agent：规划 + 工具

```python
from openai import OpenAI
import json

class AdvancedAgent:
    """高级 Agent，支持规划和工具调用"""

    def __init__(self):
        self.client = OpenAI()
        self.tools = {
            "search": self.search,
            "calculator": self.calculator,
            "code_executor": self.code_executor
        }

    def search(self, query):
        """搜索工具"""
        return f"搜索 '{query}' 的结果: ..."

    def calculator(self, expression):
        """计算器工具"""
        try:
            result = eval(expression)
            return f"计算结果: {result}"
        except Exception as e:
            return f"计算错误: {e}"

    def code_executor(self, code):
        """代码执行工具"""
        try:
            exec_globals = {}
            exec(code, exec_globals)
            return "代码执行成功"
        except Exception as e:
            return f"执行错误: {e}"

    def plan(self, task):
        """规划任务"""
        prompt = f"""
分析以下任务，将其分解为步骤：

任务: {task}

可用工具:
- search: 搜索信息
- calculator: 数学计算
- code_executor: 执行代码

请输出JSON格式的计划：
{{
  "steps": [
    {{"step": 1, "action": "工具名", "input": "输入"}},
    {{"step": 2, "action": "工具名", "input": "输入"}}
  ]
}}
"""

        response = self.client.chat.completions.create(
            model="gpt-3.5-turbo",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.3
        )

        # 解析计划
        plan_text = response.choices[0].message.content
        return json.loads(plan_text)

    def execute_step(self, step):
        """执行单步"""
        action = step["action"]
        input_data = step["input"]

        if action in self.tools:
            return self.tools[action](input_data)
        return f"未知工具: {action}"

    def run(self, task):
        """运行任务"""
        print(f"\n📋 任务: {task}")

        # 规划
        plan = self.plan(task)
        print(f"\n📝 计划: {json.dumps(plan, indent=2, ensure_ascii=False)}")

        # 执行
        results = []
        for step in plan["steps"]:
            print(f"\n⚙️  执行步骤 {step['step']}: {step['action']}({step['input']})")
            result = self.execute_step(step)
            print(f"   结果: {result}")
            results.append(result)

        # 总结
        summary_prompt = f"""
任务: {task}

执行步骤:
{json.dumps(plan, indent=2)}

执行结果:
{json.dumps(results, indent=2)}

请总结最终结果：
"""
        summary = self.client.chat.completions.create(
            model="gpt-3.5-turbo",
            messages=[{"role": "user", "content": summary_prompt}],
            temperature=0.3
        )

        return summary.choices[0].message.content

# 使用
agent = AdvancedAgent()
result = agent.run("计算从1加到100的和")
print(f"\n🎯 最终结果: {result}")
```

---

### 6. LangChain 简化 Agent

```python
from langchain.agents import initialize_agent, Tool
from langchain.llms import OpenAI
from langchain.chains import LLMMathChain

# 初始化 LLM
llm = OpenAI(temperature=0)

# 创建工具
tools = [
    Tool(
        name="Calculator",
        func=LLMMathChain(llm=llm).run,
        description="用于数学计算"
    ),
    Tool(
        name="Search",
        func=lambda x: f"搜索 '{x}' 的结果",
        description="用于搜索信息"
    )
]

# 初始化 Agent
agent = initialize_agent(
    tools=tools,
    llm=llm,
    agent="zero-shot-react-description",
    verbose=True
)

# 执行任务
result = agent.run("计算 123 + 456 的结果")
print(f"结果: {result}")
```

---

## 📖 第二部分：10分钟 - AI 工程化

---

### 什么是 AI 工程化？

**AI 工程化 = AI + DevOps + MLOps**

**目标**：将 AI 模型从实验室推向生产环境

---

### MLOps 流程

```
数据 → 训练 → 验证 → 部署 → 监控
  ↑                           ↓
  └──────────── 反馈 ────────────┘
```

---

### 核心组件

| 组件 | 说明 | 工具 |
|------|------|------|
| **实验管理** | 记录实验 | MLflow, Weights & Biases |
| **模型服务** | 模型部署 | TensorFlow Serving, TorchServe |
| **容器化** | 环境一致性 | Docker, Kubernetes |
| **监控** | 性能监控 | Prometheus, Grafana |
| **CI/CD** | 自动化部署 | GitHub Actions |

---

### 模型部署示例

#### 🚀 FastAPI 服务

```python
from fastapi import FastAPI
from pydantic import BaseModel
import uvicorn
from transformers import pipeline

app = FastAPI(title="AI 服务 API")

# 加载模型
classifier = pipeline("sentiment-analysis")

class TextRequest(BaseModel):
    text: str

@app.post("/classify")
async def classify(request: TextRequest):
    """情感分析 API"""
    result = classifier(request.text)[0]

    return {
        "label": result['label'],
        "score": float(result['score']),
        "text": request.text
    }

@app.get("/health")
async def health():
    """健康检查"""
    return {"status": "healthy"}

if __name__ == "__main__":
    uvicorn.run(app, host="0.0.0.0", port=5000)
```

---

#### 📦 Docker 化

```dockerfile
# Dockerfile
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app.py .

EXPOSE 5000

CMD ["python", "app.py"]
```

---

#### 🐳 构建和运行

```bash
# 构建镜像
docker build -t ai-service .

# 运行容器
docker run -p 5000:5000 ai-service
```

---

## ✅ 今天你学到了什么

| 部分 | 知识点 | 掌握程度 |
|------|--------|---------|
| Agent | Agent 定义 | 🎯 已理解 |
| | 核心组件 | 🎯 已理解 |
| | 简单 Agent 实现 | 🎯 已了解 |
| | 高级 Agent（规划+工具） | 🎯 已了解 |
| | LangChain | 🎯 已了解 |
| AI 工程化 | MLOps 流程 | 🎯 已理解 |
| | FastAPI 部署 | 🎯 已掌握 |
| | Docker | 🎯 已了解 |

---

## 📝 今天的作业

1. ✅ 理解 Agent 的原理
2. ✅ 尝试实现一个简单的 Agent
3. ✅ 了解 AI 工程化的基本概念

---

## 🚀 明天预告

| 时间 | 主题 |
|------|------|
| 🕐 20分钟 | 综合实战项目 |
| 🕐 10分钟 | 综合面试题 |

---

**学习进度**：📊 第6天/第3周

---

*最后更新：2026-03-29*
