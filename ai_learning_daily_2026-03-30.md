# AI 知识学习日报

> 每天学习时间：30分钟 | 学习主题：综合面试题 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-30 周日 - 第7天

---

### 🎯 今日主题：AI 综合面试题

---

## 📖 第一部分：AI 面试题精选（30分钟）

---

### 📚 目录

#### 一、机器学习面试题
#### 二、深度学习面试题
#### 三、NLP 面试题
#### 四、计算机视觉面试题
#### 五、LLM 面试题
#### 六、编程面试题

---

## 一、机器学习面试题

---

### Q1: 偏差与方差

**问题**：什么是偏差和方差？如何平衡？

**答案**：

| 概念 | 定义 | 现象 |
|------|------|------|
| **偏差** | 预测值与真实值的差距 | 欠拟合 |
| **方差** | 模型对数据扰动的敏感度 | 过拟合 |

**关系**：

```
总误差 = 偏差² + 方差 + 不可约误差
```

**权衡**：
- 高偏差：增加模型复杂度
- 高方差：正则化、增加数据、简化模型

---

### Q2: 过拟合的解决方法

**问题**：如何防止过拟合？

**答案**：

| 方法 | 说明 |
|------|------|
| **L1/L2 正则化** | 惩罚大权重 |
| **Dropout** | 随机丢弃神经元 |
| **早停** | 验证集不降则停 |
| **数据增强** | 增加训练数据 |
| **集成学习** | 多模型投票 |

**代码示例**：

```python
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense, Dropout

model = Sequential([
    Dense(128, activation='relu', kernel_regularizer='l2'),
    Dropout(0.5),
    Dense(64, activation='relu', kernel_regularizer='l2'),
    Dropout(0.5),
    Dense(10, activation='softmax')
])
```

---

### Q3: 交叉验证

**问题**：什么是 K-Fold 交叉验证？为什么使用它？

**答案**：

**定义**：将数据分成 K 份，轮流用 K-1 份训练，1 份测试

**优势**：
- 充分利用数据
- 评估更稳定
- 减少偶然性

**代码**：

```python
from sklearn.model_selection import cross_val_score
from sklearn.ensemble import RandomForestClassifier

model = RandomForestClassifier()
scores = cross_val_score(model, X, y, cv=5)

print(f"平均准确率: {scores.mean():.2%}")
print(f"标准差: {scores.std():.2%}")
```

---

### Q4: 特征工程

**问题**：常见的特征工程方法有哪些？

**答案**：

| 方法 | 说明 |
|------|------|
| **归一化** | Min-Max, Z-Score |
| **编码** | One-Hot, Label Encoding |
| **特征选择** | 过滤法、包装法、嵌入法 |
| **特征组合** | 交互特征 |
| **降维** | PCA, t-SNE |

**代码**：

```python
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.decomposition import PCA

# 标准化
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# PCA
pca = PCA(n_components=0.95)
X_pca = pca.fit_transform(X_scaled)
```

---

## 二、深度学习面试题

---

### Q5: 激活函数

**问题**：为什么需要激活函数？常用激活函数有哪些？

**答案**：

**作用**：引入非线性，否则多层网络等价于单层

**常用激活函数**：

```python
import torch
import torch.nn as nn

# ReLU（最常用）
relu = nn.ReLU()

# Leaky ReLU（解决死亡ReLU）
leaky_relu = nn.LeakyReLU(0.01)

# GELU（Transformer 常用）
gelu = nn.GELU()

# Sigmoid（输出层）
sigmoid = nn.Sigmoid()

# Tanh
tanh = nn.Tanh()
```

**对比**：

| 激活函数 | 优点 | 缺点 | 适用场景 |
|---------|------|------|---------|
| ReLU | 简单、收敛快 | 死亡ReLU | 隐藏层 |
| Sigmoid | 输出0-1 | 梯度消失 | 二分类输出 |
| Tanh | 零中心 | 梯度消失 | RNN |
| GELU | 平滑 | 计算复杂 | Transformer |

---

### Q6: 梯度消失与梯度爆炸

**问题**：如何解决梯度消失和梯度爆炸？

**答案**：

**梯度消失**：深层网络梯度趋于0
**梯度爆炸**：深层网络梯度无限增大

**解决方案**：

| 方法 | 说明 |
|------|------|
| **ReLU** | 缓解梯度消失 |
| **BatchNorm** | 稳定梯度分布 |
| **残差连接** | 梯度直传 |
| **梯度裁剪** | 防止梯度爆炸 |
| **Xavier/He 初始化** | 合理初始化 |

**残差连接代码**：

```python
import torch.nn as nn

class ResidualBlock(nn.Module):
    def __init__(self, in_channels, out_channels):
        super().__init__()
        self.conv1 = nn.Conv2d(in_channels, out_channels, 3, padding=1)
        self.bn1 = nn.BatchNorm2d(out_channels)
        self.conv2 = nn.Conv2d(out_channels, out_channels, 3, padding=1)
        self.bn2 = nn.BatchNorm2d(out_channels)

    def forward(self, x):
        residual = x
        out = torch.relu(self.bn1(self.conv1(x)))
        out = self.bn2(self.conv2(out))
        out += residual  # 残差连接
        out = torch.relu(out)
        return out
```

---

### Q7: Batch Normalization

**问题**：BatchNorm 的原理和作用？

**答案**：

**原理**：
```
均值归一化：x̂ = (x - μ) / σ
缩放平移：y = γx̂ + β
```

**作用**：
- 加速收敛
- 允许更大学习率
- 减少对初始化敏感
- 一定的正则化效果

**代码**：

```python
class CNNWithBN(nn.Module):
    def __init__(self):
        super().__init__()
        self.conv1 = nn.Conv2d(3, 64, 3)
        self.bn1 = nn.BatchNorm2d(64)  # BatchNorm
        self.conv2 = nn.Conv2d(64, 128, 3)
        self.bn2 = nn.BatchNorm2d(128)

    def forward(self, x):
        x = torch.relu(self.bn1(self.conv1(x)))
        x = torch.relu(self.bn2(self.conv2(x)))
        return x
```

---

## 三、NLP 面试题

---

### Q8: Transformer 自注意力

**问题**：解释 Transformer 的自注意力机制

**答案**：

**核心公式**：
```
Attention(Q, K, V) = softmax(QK^T / √d_k)V
```

**流程**：
1. **Q, K, V** = Input × W_Q, W_K, W_V
2. **注意力分数** = Q × K^T / √d_k
3. **Softmax** 归一化
4. **加权求和** = attention × V

**代码**：

```python
import torch
import torch.nn as nn

class SelfAttention(nn.Module):
    def __init__(self, embed_size, heads):
        super().__init__()
        self.embed_size = embed_size
        self.heads = heads
        self.head_dim = embed_size // heads

        self.W_q = nn.Linear(embed_size, embed_size)
        self.W_k = nn.Linear(embed_size, embed_size)
        self.W_v = nn.Linear(embed_size, embed_size)
        self.W_o = nn.Linear(embed_size, embed_size)

    def forward(self, x):
        batch_size, seq_len, _ = x.shape

        Q = self.W_q(x)
        K = self.W_k(x)
        V = self.W_v(x)

        scores = torch.matmul(Q, K.transpose(-2, -1)) / (self.head_dim ** 0.5)
        attention = torch.softmax(scores, dim=-1)
        output = torch.matmul(attention, V)

        output = self.W_o(output)
        return output
```

---

### Q9: Word2Vec vs GloVe

**问题**：Word2Vec 和 GloVe 的区别？

**答案**：

| 维度 | Word2Vec | GloVe |
|------|----------|-------|
| **方法** | 局部上下文预测 | 全局共现矩阵 |
| **训练** | 迭代优化 | 矩阵分解 |
| **计算** | 更快 | 更慢 |
| **效果** | 不错 | 更好 |

**Word2Vec**：
- Skip-gram：用中心词预测上下文
- CBOW：用上下文预测中心词

**GloVe**：
- Global Vectors
- 结合局部和全局信息

---

### Q10: BERT vs GPT

**问题**：BERT 和 GPT 的区别？

**答案**：

| 维度 | BERT | GPT |
|------|------|-----|
| **架构** | Encoder | Decoder |
| **注意力** | 双向 | 单向（从左到右） |
| **训练** | Masked LM | 自回归 |
| **适用** | 理解任务 | 生成任务 |

**BERT**：
- 更适合分类、NER、问答等理解任务
- 双向上下文

**GPT**：
- 更适合文本生成
- 单向生成能力

---

## 四、计算机视觉面试题

---

### Q11: CNN 为什么适合图像？

**问题**：为什么 CNN 比传统 MLP 适合图像？

**答案**：

**CNN 优势**：

| 优势 | 说明 |
|------|------|
| **局部感知** | 卷积核感受野小 |
| **参数共享** | 同一卷积核遍历全图 |
| **平移不变** | 图像平移不影响特征 |
| **层次特征** | 底层到高层特征 |

**对比**：
- MLP：全连接，参数爆炸，无空间结构
- CNN：局部连接，参数少，保留空间关系

---

### Q12: 感受野

**问题**：什么是感受野？如何增大感受野？

**答案**：

**定义**：神经元能感知的原始图像区域大小

**公式**：
```
RF = Σ(k_i - 1) × Π(s_j) + k_0
```
其中 k=kernel size, s=stride

**增大感受野方法**：
- 更大的卷积核
- 增加网络深度
- 使用空洞卷积（Dilated Convolution）
- 使用池化层

**代码**：

```python
# 空洞卷积
dilated_conv = nn.Conv2d(
    in_channels=64,
    out_channels=128,
    kernel_size=3,
    dilation=2,  # 扩大感受野
    padding=2
)
```

---

### Q13: YOLO vs Faster R-CNN

**问题**：YOLO 和 Faster R-CNN 的区别？

**答案**：

| 维度 | YOLO | Faster R-CNN |
|------|------|-------------|
| **阶段** | 单阶段 | 两阶段 |
| **速度** | 快（实时） | 慢 |
| **精度** | 略低 | 更高 |
| **原理** | 直接回归边界框 | RPN + 分类回归 |

**YOLO**：
- 单次前向传播
- 适合实时检测

**Faster R-CNN**：
- 先生成候选框（RPN）
- 再分类回归
- 精度更高

---

## 五、LLM 面试题

---

### Q14: LLM 训练三阶段

**问题**：LLM 训练的三个阶段？

**答案**：

| 阶段 | 目标 | 数据 |
|------|------|------|
| **预训练** | 学习语言规律 | 大规模文本 |
| **指令微调** | 理解指令 | 指令-响应对 |
| **RLHF** | 对齐人类偏好 | 人类偏好数据 |

**预训练**：
```
输入: "The capital of France is"
输出: "Paris"
```

**指令微调**：
```
指令: "翻译成中文"
输入: "Hello"
输出: "你好"
```

**RLHF**：
```
1. 收集偏好（A vs B）
2. 训练奖励模型
3. PPO 微调
```

---

### Q15: Temperature 参数

**问题**：LLM 中 Temperature 的作用？

**答案**：

**作用**：控制输出的随机性和确定性

| 温度 | 效果 |
|------|------|
| **低（0.1-0.3）** | 更确定、一致 |
| **中（0.5-0.7）** | 平衡 |
| **高（0.8-1.2）** | 更随机、有创意 |

**代码**：

```python
import torch.nn.functional as F

def sample_with_temperature(logits, temperature=0.7):
    logits = logits / temperature
    probs = F.softmax(logits, dim=-1)
    next_token = torch.multinomial(probs, num_samples=1)
    return next_token
```

**应用**：
- 编程：低温度
- 创意写作：高温度

---

### Q16: RAG vs Fine-tuning

**问题**：何时使用 RAG？何时使用 Fine-tuning？

**答案**：

| 维度 | RAG | Fine-tuning |
|------|-----|-------------|
| **更新知识** | 添加文档 | 重新训练 |
| **成本** | 低 | 高 |
| **时效性** | 好 | 差 |
| **幻觉** | 减少 | 仍存在 |
| **适用** | 问答、检索 | 领域任务 |

**RAG 适合**：
- 知识问答
- 最新信息
- 多轮对话

**Fine-tuning 适合**：
- 特定格式输出
- 领域专用任务
- 风格定制

---

### Q17: Context Window 优化

**问题**：如何优化长上下文处理？

**答案**：

**方法**：

| 方法 | 说明 |
|------|------|
| **滑动窗口** | 分段处理 |
| **摘要压缩** | 压缩历史对话 |
| **KV Cache** | 缓存 KV 矩阵 |
| **长上下文模型** | 使用长窗口模型（Claude, GPT-4） |

**KV Cache 代码**：

```python
class KVCache:
    def __init__(self):
        self.keys = []
        self.values = []

    def update(self, key, value):
        self.keys.append(key)
        self.values.append(value)

    def get(self):
        return torch.cat(self.keys, dim=1), torch.cat(self.values, dim=1)
```

---

## 六、编程面试题

---

### Q18: 实现交叉熵损失

**问题**：手动实现交叉熵损失

**答案**：

```python
import torch
import torch.nn.functional as F

def cross_entropy_loss_manual(logits, targets):
    """
    logits: (batch_size, num_classes)
    targets: (batch_size,)
    """
    batch_size = logits.size(0)

    # Softmax
    probs = F.softmax(logits, dim=1)

    # 获取真实类别的概率
    target_probs = probs[range(batch_size), targets]

    # 计算损失
    loss = -torch.log(target_probs + 1e-10)  # 避免log(0)

    # 平均
    return loss.mean()

# 测试
logits = torch.tensor([[2.0, 1.0, 0.1],
                       [0.5, 2.5, 0.5]])
targets = torch.tensor([0, 1])

# 手动实现
manual_loss = cross_entropy_loss_manual(logits, targets)

# PyTorch 实现
pytorch_loss = F.cross_entropy(logits, targets)

print(f"手动实现: {manual_loss.item():.4f}")
print(f"PyTorch: {pytorch_loss.item():.4f}")
```

---

### Q19: 实现 K-Means

**问题**：手动实现 K-Means 聚类

**答案**：

```python
import numpy as np

class KMeans:
    def __init__(self, k=3, max_iters=100, tol=1e-4):
        self.k = k
        self.max_iters = max_iters
        self.tol = tol

    def fit(self, X):
        """训练 K-Means"""
        # 随机初始化中心点
        self.centroids = X[np.random.choice(X.shape[0], self.k, replace=False)]

        for _ in range(self.max_iters):
            # 分配到最近的中心点
            distances = self._calc_distances(X)
            labels = np.argmin(distances, axis=1)

            # 计算新中心点
            new_centroids = np.array([
                X[labels == i].mean(axis=0) for i in range(self.k)
            ])

            # 检查收敛
            if np.allclose(self.centroids, new_centroids, atol=self.tol):
                break

            self.centroids = new_centroids

        return labels

    def _calc_distances(self, X):
        """计算到每个中心点的距离"""
        distances = []
        for centroid in self.centroids:
            distances.append(np.linalg.norm(X - centroid, axis=1))
        return np.array(distances).T

# 测试
np.random.seed(42)
X = np.random.randn(300, 2)

kmeans = KMeans(k=3)
labels = kmeans.fit(X)

print(f"中心点:\n{kmeans.centroids}")
print(f"标签分布: {np.bincount(labels)}")
```

---

### Q20: 实现 ReLU

**问题**：手动实现 ReLU 及其导数

**答案**：

```python
import numpy as np
import matplotlib.pyplot as plt

def relu(x):
    """ReLU 激活函数"""
    return np.maximum(0, x)

def relu_derivative(x):
    """ReLU 导数"""
    return (x > 0).astype(float)

# 测试
x = np.linspace(-5, 5, 100)
y = relu(x)
dy = relu_derivative(x)

# 可视化
fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(12, 4))

ax1.plot(x, y)
ax1.set_title('ReLU')
ax1.grid(True)

ax2.plot(x, dy)
ax2.set_title('ReLU 导数')
ax2.grid(True)

plt.show()

# 数值测试
test_values = [-2, -1, 0, 1, 2]
print("ReLU 测试:")
for val in test_values:
    print(f"ReLU({val}) = {relu(val)}, 导数 = {relu_derivative(val)}")
```

---

## 📝 面试技巧

---

### 准备建议

1. **理解原理**：不仅要会用，还要知道为什么
2. **手写代码**：练习基础算法和模型
3. **结合项目**：用项目经验举例
4. **持续学习**：关注最新技术动态

### 常见问题类型

| 类型 | 例子 |
|------|------|
| **概念题** | 解释过拟合 |
| **对比题** | CNN vs RNN |
| **算法题** | 实现交叉熵 |
| **项目题** | 描述你的项目 |
| **场景题** | 如何优化模型 |

---

## ✅ 本周学习总结

| 天数 | 日期 | 主题 | 状态 |
|------|------|------|------|
| 第1天 | 2026-03-24 | 机器学习基础 | ✅ |
| 第2天 | 2026-03-25 | 深度学习 | ✅ |
| 第3天 | 2026-03-26 | NLP | ✅ |
| 第4天 | 2026-03-27 | 计算机视觉 | ✅ |
| 第5天 | 2026-03-28 | 大语言模型 | ✅ |
| 第6天 | 2026-03-29 | AI工程化 | ✅ |
| 第7天 | 2026-03-30 | 综合面试题 | ✅ |

---

## 🎓 恭喜完成第二周学习！

---

**学习进度**：📊 第2周完成 ✅

---

*最后更新：2026-03-30*
