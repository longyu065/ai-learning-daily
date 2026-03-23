# AI 知识学习日报

> 每天学习时间：30分钟 | 学习主题：深度学习 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-25 周二 - 第2天

---

### 🎯 今日主题：深度学习

---

## 📖 第一部分：深度学习基础（15分钟）

---

### 1. 什么是深度学习？

**定义**：基于多层神经网络的机器学习方法

**与传统机器学习的区别**：

| 维度 | 传统机器学习 | 深度学习 |
|------|------------|---------|
| **特征工程** | 手工设计 | 自动学习 |
| **数据量** | 小到中等 | 需要大量数据 |
| **模型复杂度** | 较简单 | 非常复杂 |
| **计算资源** | CPU | GPU/TPU |
| **可解释性** | 较好 | 较差（黑盒） |

**深度学习成功的原因**：
1. **大数据**：互联网提供海量数据
2. **大算力**：GPU 加速计算
3. **新算法**：更深的网络、更好的优化

---

### 2. 神经网络基础

#### 感知机（Perceptron）

**结构**：
```
输入 x1, x2, x3
    ↓   ↓   ↓
  权重 w1, w2, w3
    ↓   ↓   ↓
    加权求和 → 激活函数 → 输出
```

**代码示例**：

```python
import numpy as np

class Perceptron:
    def __init__(self, input_size):
        self.weights = np.random.randn(input_size)
        self.bias = 0

    def forward(self, x):
        # 加权求和 + 偏置
        z = np.dot(x, self.weights) + self.bias
        # 激活函数（阶跃函数）
        return 1 if z > 0 else 0

# 使用
perceptron = Perceptron(input_size=3)
x = np.array([1, 0, 1])
output = perceptron.forward(x)
print(f"输出: {output}")
```

---

#### 多层感知机（MLP）

**结构**：
```
输入层 → 隐藏层 → 输出层
```

**代码示例**（使用 PyTorch）：

```python
import torch
import torch.nn as nn

# 定义 MLP
class MLP(nn.Module):
    def __init__(self, input_size, hidden_size, output_size):
        super(MLP, self).__init__()
        self.fc1 = nn.Linear(input_size, hidden_size)  # 输入层 → 隐藏层
        self.relu = nn.ReLU()                          # 激活函数
        self.fc2 = nn.Linear(hidden_size, output_size) # 隐藏层 → 输出层

    def forward(self, x):
        x = self.fc1(x)
        x = self.relu(x)
        x = self.fc2(x)
        return x

# 创建模型
model = MLP(input_size=10, hidden_size=20, output_size=2)

# 前向传播
x = torch.randn(1, 10)
output = model(x)
print(f"输出形状: {output.shape}")  # 输出形状: torch.Size([1, 2])
```

---

### 3. 激活函数

**作用**：引入非线性，让网络能学习复杂模式

| 激活函数 | 公式 | 特点 | 适用场景 |
|---------|------|------|---------|
| **Sigmoid** | 1/(1+e^(-x)) | 输出0-1 | 二分类输出 |
| **Tanh** | (e^x-e^(-x))/(e^x+e^(-x)) | 输出-1到1 | RNN |
| **ReLU** | max(0, x) | 简单、收敛快 | 隐藏层（最常用） |
| **Leaky ReLU** | max(0.01x, x) | 解决死亡ReLU | 深层网络 |
| **Softmax** | e^xi/Σe^xj | 概率分布 | 多分类输出 |

**可视化**：

```python
import numpy as np
import matplotlib.pyplot as plt

x = np.linspace(-5, 5, 100)

relu = np.maximum(0, x)
sigmoid = 1 / (1 + np.exp(-x))

fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(10, 4))
ax1.plot(x, relu)
ax1.set_title('ReLU')
ax1.grid(True)

ax2.plot(x, sigmoid)
ax2.set_title('Sigmoid')
ax2.grid(True)

plt.show()
```

---

### 4. 损失函数

**作用**：衡量预测值与真实值的差距

**常用损失函数**：

| 任务 | 损失函数 | 说明 |
|------|---------|------|
| **回归** | MSE | 均方误差 |
| **二分类** | BCE | 二元交叉熵 |
| **多分类** | CE | 交叉熵 |

**代码示例**：

```python
import torch.nn.functional as F

# 预测和真实值
predictions = torch.tensor([[0.2, 0.8], [0.9, 0.1]])
targets = torch.tensor([[0, 1], [1, 0]])

# 交叉熵损失
loss = F.cross_entropy(predictions, targets.argmax(dim=1))
print(f"交叉熵损失: {loss.item():.4f}")

# MSE 损失（回归）
pred_values = torch.tensor([2.5, 3.7])
true_values = torch.tensor([2.8, 4.0])
mse_loss = F.mse_loss(pred_values, true_values)
print(f"MSE 损失: {mse_loss.item():.4f}")
```

---

### 5. 优化器

**作用**：更新网络参数，最小化损失

**常用优化器**：

| 优化器 | 特点 | 适用场景 |
|--------|------|---------|
| **SGD** | 简单、稳定 | 基础模型 |
| **Momentum** | 加速收敛 | 深层网络 |
| **Adam** | 自适应学习率 | **最常用** |
| **AdamW** | Adam + 权重衰减 | 大规模模型 |

**代码示例**：

```python
import torch.optim as optim

# 模型
model = MLP(input_size=10, hidden_size=20, output_size=2)

# 优化器
optimizer = optim.Adam(model.parameters(), lr=0.001)

# 训练循环
for epoch in range(10):
    # 前向传播
    x = torch.randn(32, 10)
    y = torch.randint(0, 2, (32,))
    output = model(x)
    loss = F.cross_entropy(output, y)

    # 反向传播
    optimizer.zero_grad()
    loss.backward()
    optimizer.step()

    print(f"Epoch {epoch}, Loss: {loss.item():.4f}")
```

---

### 6. 卷积神经网络（CNN）

**用途**：图像处理、计算机视觉

**核心组件**：

| 组件 | 作用 | 参数 |
|------|------|------|
| **卷积层** | 提取特征 | kernel_size, stride, padding |
| **池化层** | 降采样 | pool_size, stride |
| **全连接层** | 分类 | - |

**代码示例**：

```python
class CNN(nn.Module):
    def __init__(self, num_classes=10):
        super(CNN, self).__init__()
        self.conv1 = nn.Conv2d(3, 32, kernel_size=3, padding=1)
        self.conv2 = nn.Conv2d(32, 64, kernel_size=3, padding=1)
        self.pool = nn.MaxPool2d(2, 2)
        self.fc = nn.Linear(64 * 8 * 8, num_classes)

    def forward(self, x):
        # 3x32x32 → 32x16x16
        x = self.pool(torch.relu(self.conv1(x)))
        # 32x16x16 → 64x8x8
        x = self.pool(torch.relu(self.conv2(x)))
        x = x.view(x.size(0), -1)
        x = self.fc(x)
        return x

model = CNN()
print(model)
```

---

### 7. 循环神经网络（RNN）

**用途**：序列数据处理（文本、语音、时间序列）

**基础 RNN**：

```python
class RNN(nn.Module):
    def __init__(self, input_size, hidden_size, output_size):
        super(RNN, self).__init__()
        self.hidden_size = hidden_size
        self.rnn = nn.RNN(input_size, hidden_size, batch_first=True)
        self.fc = nn.Linear(hidden_size, output_size)

    def forward(self, x):
        # x: (batch, seq_len, input_size)
        out, hidden = self.rnn(x)
        # 使用最后一个时间步的输出
        out = self.fc(out[:, -1, :])
        return out

model = RNN(input_size=10, hidden_size=20, output_size=2)
```

**LSTM（长短期记忆网络）**：解决长期依赖问题

```python
class LSTM(nn.Module):
    def __init__(self, input_size, hidden_size, output_size):
        super(LSTM, self).__init__()
        self.hidden_size = hidden_size
        self.lstm = nn.LSTM(input_size, hidden_size, batch_first=True)
        self.fc = nn.Linear(hidden_size, output_size)

    def forward(self, x):
        out, (hidden, cell) = self.lstm(x)
        out = self.fc(out[:, -1, :])
        return out
```

---

### 8. 注意力机制

**作用**：让模型关注重要部分

**Self-Attention**：

```python
import torch
import torch.nn.functional as F

def self_attention(query, key, value):
    # query, key, value: (batch, seq_len, d_model)
    # 计算注意力分数
    scores = torch.matmul(query, key.transpose(-2, -1))
    scores = scores / np.sqrt(key.size(-1))

    # Softmax
    attention_weights = F.softmax(scores, dim=-1)

    # 加权求和
    output = torch.matmul(attention_weights, value)
    return output

# 使用
batch_size = 4
seq_len = 10
d_model = 64

query = torch.randn(batch_size, seq_len, d_model)
key = torch.randn(batch_size, seq_len, d_model)
value = torch.randn(batch_size, seq_len, d_model)

output = self_attention(query, key, value)
print(f"输出形状: {output.shape}")
```

---

## 💻 第二部分：实战练习（15分钟）

---

### 练习1：图像分类（MNIST）

```python
import torch
import torch.nn as nn
import torch.optim as optim
from torchvision import datasets, transforms

# 数据加载
transform = transforms.Compose([
    transforms.ToTensor(),
    transforms.Normalize((0.5,), (0.5,))
])

train_dataset = datasets.MNIST('./data', train=True,
                              download=True, transform=transform)
train_loader = torch.utils.data.DataLoader(train_dataset,
                                          batch_size=64,
                                          shuffle=True)

# 模型
class SimpleCNN(nn.Module):
    def __init__(self):
        super(SimpleCNN, self).__init__()
        self.conv1 = nn.Conv2d(1, 32, kernel_size=3)
        self.conv2 = nn.Conv2d(32, 64, kernel_size=3)
        self.pool = nn.MaxPool2d(2)
        self.fc = nn.Linear(64 * 5 * 5, 10)

    def forward(self, x):
        x = self.pool(torch.relu(self.conv1(x)))
        x = self.pool(torch.relu(self.conv2(x)))
        x = x.view(x.size(0), -1)
        x = self.fc(x)
        return x

# 训练
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
model = SimpleCNN().to(device)
optimizer = optim.Adam(model.parameters())
criterion = nn.CrossEntropyLoss()

for epoch in range(3):
    model.train()
    for batch_idx, (data, target) in enumerate(train_loader):
        data, target = data.to(device), target.to(device)

        optimizer.zero_grad()
        output = model(data)
        loss = criterion(output, target)
        loss.backward()
        optimizer.step()

        if batch_idx % 100 == 0:
            print(f"Epoch {epoch}, Batch {batch_idx}, Loss: {loss.item():.4f}")
```

---

### 练习2：文本分类（情感分析）

```python
import torch
import torch.nn as nn
from torchtext.vocab import build_vocab_from_iterator
from torchtext.data.utils import get_tokenizer

# 简化的文本分类模型
class TextClassifier(nn.Module):
    def __init__(self, vocab_size, embed_dim, hidden_dim, num_classes):
        super(TextClassifier, self).__init__()
        self.embedding = nn.Embedding(vocab_size, embed_dim)
        self.lstm = nn.LSTM(embed_dim, hidden_dim, batch_first=True)
        self.fc = nn.Linear(hidden_dim, num_classes)

    def forward(self, x):
        embedded = self.embedding(x)
        output, (hidden, _) = self.lstm(embedded)
        output = self.fc(hidden[-1])
        return output

# 参数
vocab_size = 10000
embed_dim = 100
hidden_dim = 128
num_classes = 2

model = TextClassifier(vocab_size, embed_dim, hidden_dim, num_classes)
print(model)
```

---

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| 深度学习 vs 传统机器学习 | 🎯 已理解 |
| 感知机和 MLP | 🎯 已掌握 |
| 激活函数 | 🎯 已掌握 |
| 损失函数 | 🎯 已掌握 |
| 优化器 | 🎯 已掌握 |
| CNN | 🎯 已理解 |
| RNN 和 LSTM | 🎯 已理解 |
| 注意力机制 | 🎯 已了解 |

---

## 📝 今天的作业

1. ✅ 运行 MNIST 图像分类示例
2. ✅ 尝试不同的激活函数
3. ✅ 比较不同优化器的效果

---

## 🚀 本周学习进度

| 天数 | 日期 | 主题 | 状态 |
|------|------|------|------|
| 第1天 | 2026-03-24 | 机器学习基础 | ✅ |
| 第2天 | 2026-03-25 | 深度学习 | ✅ |
| 第3天 | 2026-03-26 | NLP | 📅 |
| 第4天 | 2026-03-27 | 计算机视觉 | ⏳ |
| 第5天 | 2026-03-28 | 大语言模型 | ⏳ |
| 第6天 | 2026-03-29 | AI工程化 | ⏳ |
| 第7天 | 2026-03-30 | 综合面试题 | ⏳ |

---

**学习进度**：📊 第2天/第2周

---

*最后更新：2026-03-25*
