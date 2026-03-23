# AI 知识学习日报

> 每天学习时间：30分钟 | 模式：20分钟大模型应用 + 10分钟机器学习基础 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-25 周二 - 第2天

---

### 🎯 今日学习目标

| 部分 | 时间 | 主题 |
|------|------|------|
| 🕐 20分钟 | **大模型应用** | OpenAI API 调用 |
| 🕐 10分钟 | **机器学习基础** | 深度学习基础 |

---

## 📖 第一部分：20分钟 - OpenAI API 调用

---

### 1. OpenAI API 简介

**OpenAI API**：使用 OpenAI 提供的大模型能力（GPT-4, GPT-3.5 等）

**主要能力**：
- 💬 聊天对话
- 📝 文本生成
- 🏷️ 分类标注
- 🔄 代码补全
- 🎨 图像生成（DALL-E）

---

### 2. API 密钥获取

**步骤**：

1. 访问 https://platform.openai.com/
2. 注册/登录账号
3. 进入 API Keys 页面
4. 点击 "Create new secret key"
5. 复制密钥（只显示一次，妥善保存！）

**环境变量设置**：

```bash
# Windows
set OPENAI_API_KEY=sk-xxxxxxxxxxxxxxxxxxxxxxxx

# Linux/Mac
export OPENAI_API_KEY="sk-xxxxxxxxxxxxxxxxxxxxxxxx"
```

---

### 3. 安装 SDK

```bash
# 安装 OpenAI Python SDK
pip install openai

# 验证安装
python -c "import openai; print(openai.__version__)"
```

---

### 4. 基础调用

#### 📝 最简单的调用

```python
from openai import OpenAI

# 初始化客户端
client = OpenAI(api_key="你的API密钥")

# 调用聊天接口
response = client.chat.completions.create(
    model="gpt-3.5-turbo",  # 模型选择
    messages=[
        {"role": "system", "content": "你是一个有帮助的助手。"},
        {"role": "user", "content": "你好！"}
    ]
)

# 获取回答
answer = response.choices[0].message.content
print(answer)
# 输出：你好！有什么可以帮助你的吗？
```

---

#### 🔑 从环境变量读取密钥

```python
import os
from openai import OpenAI

# 从环境变量读取
api_key = os.getenv("OPENAI_API_KEY")

# 初始化客户端
client = OpenAI(api_key=api_key)

# 调用
response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[
        {"role": "user", "content": "用一句话解释什么是机器学习。"}
    ]
)

print(response.choices[0].message.content)
```

---

### 5. 参数详解

#### 📊 核心参数

| 参数 | 说明 | 示例 |
|------|------|------|
| **model** | 使用的模型 | "gpt-3.5-turbo", "gpt-4" |
| **messages** | 对话消息 | [{"role": "user", "content": "..."}] |
| **temperature** | 随机性（0-2） | 0.7（默认） |
| **max_tokens** | 最大输出token数 | 100 |
| **top_p** | 核采样（0-1） | 0.9 |
| **frequency_penalty** | 频率惩罚（-2-2） | 0 |
| **presence_penalty** | 存在惩罚（-2-2） | 0 |

---

#### 🌡️ Temperature 参数

**控制输出的随机性和创造性**：

| 温度 | 效果 | 适用场景 |
|------|------|---------|
| **0.1-0.3** | 更确定、一致 | 代码生成、翻译 |
| **0.5-0.7** | 平衡 | 通用对话 |
| **0.8-1.2** | 更随机、有创意 | 创意写作、头脑风暴 |

```python
# 低温度（更确定）
response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[{"role": "user", "content": "写一个函数，计算斐波那契数列"}],
    temperature=0.1
)

# 高温度（更有创意）
response = client.chat.completions.create(
    model="gpt-3.5-turbo",
    messages=[{"role": "user", "content": "写一个关于AI的科幻故事"}],
    temperature=1.0
)
```

---

### 6. 实战案例

#### 💻 案例1：代码助手

```python
from openai import OpenAI

client = OpenAI()

def code_review(code: str) -> str:
    """代码审查"""
    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[
            {
                "role": "system",
                "content": "你是一位资深代码审查专家，擅长发现代码中的问题。"
            },
            {
                "role": "user",
                "content": f"""
请审查以下 Java 代码，指出问题并给出优化建议：

```java
{code}
```

请以表格形式输出：
| 问题 | 严重程度 | 说明 | 修复建议 |
"""
            }
        ],
        temperature=0.3
    )

    return response.choices[0].message.content

# 使用
code = """
public List<String> filterLongStrings(List<String> list) {
    List<String> result = new ArrayList<>();
    for (String s : list) {
        if (s.length() > 10) {
            result.add(s);
        }
    }
    return result;
}
"""

print(code_review(code))
```

---

#### 💻 案例2：Java API 文档生成器

```python
def generate_javadoc(method_code: str) -> str:
    """生成 JavaDoc"""
    response = client.chat.completions.create(
        model="gpt-3.5-turbo",
        messages=[
            {
                "role": "system",
                "content": "你是一位 Java 开发专家，擅长编写规范的 JavaDoc 注释。"
            },
            {
                "role": "user",
                "content": f"""
为以下 Java 方法生成完整的 JavaDoc 注释：

```java
{method_code}
```

要求：
1. 包含方法描述
2. @param 参数说明
3. @return 返回值说明
4. @throws 异常说明
5. @since 版本信息
"""
            }
        ],
        temperature=0.2
    )

    return response.choices[0].message.content

# 使用
method = """
public User getUserById(Long id) {
    if (id == null) {
        throw new IllegalArgumentException("ID不能为空");
    }
    return userRepository.findById(id)
        .orElseThrow(() -> new UserNotFoundException(id));
}
"""

print(generate_javadoc(method))
```

---

#### 💻 案例3：对话历史管理

```python
class ChatAssistant:
    """对话助手"""

    def __init__(self):
        self.client = OpenAI()
        self.messages = [
            {"role": "system", "content": "你是一个有帮助的编程助手。"}
        ]

    def add_message(self, role: str, content: str):
        """添加消息"""
        self.messages.append({"role": role, "content": content})

        # 限制历史长度（避免 token 超限）
        if len(self.messages) > 10:
            self.messages = [self.messages[0]] + self.messages[-9:]

    def chat(self, user_input: str) -> str:
        """对话"""
        self.add_message("user", user_input)

        response = self.client.chat.completions.create(
            model="gpt-3.5-turbo",
            messages=self.messages,
            temperature=0.7
        )

        assistant_reply = response.choices[0].message.content

        self.add_message("assistant", assistant_reply)

        return assistant_reply

# 使用
assistant = ChatAssistant()

print(assistant.chat("什么是设计模式？"))
print(assistant.chat("给我举一个单例模式的例子"))
print(assistant.chat("单例模式有什么缺点？"))
```

---

### 7. 错误处理

```python
from openai import OpenAI
from openai import OpenAIError

client = OpenAI()

def safe_chat(prompt: str) -> str:
    """安全的聊天调用"""
    try:
        response = client.chat.completions.create(
            model="gpt-3.5-turbo",
            messages=[{"role": "user", "content": prompt}],
            temperature=0.7
        )
        return response.choices[0].message.content

    except OpenAIError as e:
        # 处理 OpenAI API 错误
        return f"API 错误: {e}"

    except Exception as e:
        # 处理其他错误
        return f"未知错误: {e}"

# 使用
print(safe_chat("你好"))
```

---

## 📖 第二部分：10分钟 - 深度学习基础

---

### 什么是深度学习？

**定义**：基于多层神经网络的机器学习方法

**与传统机器学习的区别**：

| 维度 | 传统机器学习 | 深度学习 |
|------|------------|---------|
| **特征工程** | 手工设计 | 自动学习 |
| **数据量需求** | 中等 | 需要大量数据 |
| **计算资源** | CPU | GPU/TPU |
| **模型复杂度** | 相对简单 | 可以非常复杂 |
| **可解释性** | 较好 | 较差（黑盒） |

---

### 神经网络基础

#### 1. 神经元（感知机）

**结构**：

```
输入 x1, x2, x3
    ↓   ↓   ↓
  权重 w1, w2, w3
    ↓   ↓   ↓
  加权求和 + 偏置
    ↓
  激活函数
    ↓
  输出
```

**公式**：
```
y = f(w1*x1 + w2*x2 + ... + wn*xn + b)
```
- `w`: 权重
- `b`: 偏置
- `f`: 激活函数

---

#### 2. 激活函数

**作用**：引入非线性，让网络能学习复杂模式

**常见激活函数**：

| 激活函数 | 公式 | 特点 | 适用场景 |
|---------|------|------|---------|
| **ReLU** | max(0, x) | 简单、收敛快 | 隐藏层（最常用） |
| **Sigmoid** | 1/(1+e^(-x)) | 输出0-1 | 二分类输出 |
| **Tanh** | (e^x-e^(-x))/(e^x+e^(-x)) | 输出-1到1 | RNN |
| **Softmax** | e^xi/Σe^xj | 概率分布 | 多分类输出 |

---

### 为什么深度学习能自动学习特征？

**类比**：

| 传统机器学习 | 深度学习 |
|-------------|---------|
| 手工告诉计算机："看颜色、形状、纹理" | 计算机自己发现："哦，猫有耳朵、胡须、毛茸茸的" |

**层级特征学习**：

```
输入层：原始像素
    ↓
第1层：边缘、线条
    ↓
第2层：形状、角落
    ↓
第3层：物体部件（眼睛、鼻子）
    ↓
第4层：完整物体（猫、狗）
```

---

## ✅ 今天你学到了什么

| 部分 | 知识点 | 掌握程度 |
|------|--------|---------|
| OpenAI API | API 密钥获取 | 🎯 已了解 |
| | SDK 安装和初始化 | 🎯 已掌握 |
| | 基础调用 | 🎯 已掌握 |
| | 参数详解 | 🎯 已理解 |
| | Temperature 使用 | 🎯 已掌握 |
| | 实战案例 | 🎯 已掌握 |
| | 错误处理 | 🎯 已了解 |
| 深度学习 | 深度学习定义 | 🎯 已理解 |
| | 神经元结构 | 🎯 已理解 |
| | 激活函数 | 🎯 已了解 |
| | 特征学习 | 🎯 已理解 |

---

## 📝 今天的作业

1. ✅ 注册 OpenAI 账号，获取 API 密钥
2. ✅ 尝试调用 API，完成一次简单对话
3. ✅ 编写一个"代码审查"或"文档生成"的实用函数

---

## 🚀 明天预告

| 时间 | 主题 |
|------|------|
| 🕐 20分钟 | RAG 检索增强生成 |
| 🕐 10分钟 | NLP 基础 |

---

**学习进度**：📊 第2天/第3周

---

*最后更新：2026-03-25*
