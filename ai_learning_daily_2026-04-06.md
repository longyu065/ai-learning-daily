# AI 知识学习日报

> 每天学习时间：30分钟 | 学习主题：综合面试题 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-04-06 周一 - 第7天

---

### 🎯 今日主题：综合面试题

---

## 📖 本周知识回顾与面试题

---

### 一、机器学习基础面试题

---

#### Q1: 过拟合 vs 欠拟合

**问题**：如何判断和解决过拟合/欠拟合？

**答案**：

| 现象 | 训练集准确率 | 验证集准确率 | 解决方法 |
|------|-------------|-------------|---------|
| **过拟合** | 高 | 低 | 正则化、Dropout、增加数据、早停 |
| **欠拟合** | 低 | 低 | 增加模型复杂度、减少正则化、特征工程 |

---

#### Q2: 交叉验证

**问题**：为什么需要交叉验证？常用方法有哪些？

**答案**：

**目的**：更准确评估模型泛化能力

**方法**：
1. **K-Fold CV**：将数据分成 K 份，轮流作验证集
2. **Stratified K-Fold**：保持类别分布
3. **Leave-One-Out**：每次留一个样本

---

#### Q3: 正则化（L1 vs L2）

**问题**：L1 和 L2 正则化的区别？

**答案**：

| 正则化 | 公式 | 特点 | 用途 |
|--------|------|------|------|
| **L1** | Σ|w| | 稀疏解 | 特征选择 |
| **L2** | Σw² | 平滑解 | 防止过拟合 |

```python
# L1 正则化 (Lasso)
from sklearn.linear_model import Lasso
model = Lasso(alpha=0.1)  # alpha 控制正则化强度

# L2 正则化 (Ridge)
from sklearn.linear_model import Ridge
model = Ridge(alpha=0.1)

# L1 + L2 (ElasticNet)
from sklearn.linear_model import ElasticNet
model = ElasticNet(alpha=0.1, l1_ratio=0.5)
```

---

#### Q4: 梯度下降优化算法

**问题**：SGD、Adam、RMSprop 的区别？

**答案**：

| 算法 | 特点 | 适用场景 |
|------|------|---------|
| **SGD** | 简单、随机 | 大数据集 |
| **Momentum** | 加速收敛 | 深度学习 |
| **Adam** | 自适应学习率 | 通用 |
| **RMSprop** | 自适应学习率 | RNN |

```python
import torch.optim as optim

# 不同优化器
optimizer_sgd = optim.SGD(model.parameters(), lr=0.01, momentum=0.9)
optimizer_adam = optim.Adam(model.parameters(), lr=0.001)
optimizer_rmsprop = optim.RMSprop(model.parameters(), lr=0.001)
```

---

### 二、深度学习面试题

---

#### Q5: 激活函数

**问题**：常用激活函数及其特点？

**答案**：

| 激活函数 | 公式 | 特点 | 适用场景 |
|---------|------|------|---------|
| **ReLU** | max(0, x) | 简单、高效 | 隐藏层 |
| **Sigmoid** | 1/(1+e^-x) | 输出(0,1) | 二分类输出 |
| **Tanh** | (e^x-e^-x)/(e^x+e^-x) | 输出(-1,1) | RNN |
| **Softmax** | exp(x_i)/Σexp(x_j) | 概率分布 | 多分类输出 |

---

#### Q6: 反向传播

**问题**：解释反向传播的原理？

**答案**：

**原理**：链式法则

```
∂L/∂w = ∂L/∂a × ∂a/∂z × ∂z/∂w
```

**流程**：
1. 前向传播：计算各层输出
2. 计算损失：预测值与真实值的差距
3. 反向传播：计算梯度
4. 更新参数：w = w - η × ∂L/∂w

---

#### Q7: Batch Normalization

**问题**：Batch Normalization 的作用？

**答案**：

**作用**：
1. 加速训练
2. 减少梯度消失/爆炸
3. 允许更大学习率

**公式**：
```
BN(x) = γ × (x - μ) / σ + β
```

**使用**：
```python
import torch.nn as nn

# PyTorch 中使用
bn = nn.BatchNorm1d(num_features=64)  # 1D
bn = nn.BatchNorm2d(num_features=64)  # 2D (CNN)
```

---

### 三、NLP 面试题

---

#### Q8: Word Embedding

**问题**：Word2Vec 和 GloVe 的区别？

**答案**：

| 方法 | 原理 | 优点 | 缺点 |
|------|------|------|------|
| **Word2Vec** | CBOW/Skip-gram | 高效、局部上下文 | 忽略全局信息 |
| **GloVe** | 矩阵分解 | 全局上下文 | 需要共现矩阵 |

```python
from gensim.models import Word2Vec

# 训练 Word2Vec
sentences = [["hello", "world"], ["hello", "ai"]]
model = Word2Vec(sentences, vector_size=100, window=5, min_count=1)

# 获取词向量
vector = model.wv["hello"]

# 计算相似度
similarity = model.wv.similarity("hello", "world")
```

---

#### Q9: Transformer 自注意力

**问题**：解释自注意力机制？

**答案**：

**核心公式**：
```
Attention(Q, K, V) = softmax(QK^T / √d_k)V
```

**计算步骤**：
1. Q, K, V = Input × W_Q, W_K, W_V
2. 注意力分数 = Q × K^T / √d_k
3. Softmax 归一化
4. 加权求和 = attention × V

---

#### Q10: BERT vs GPT

**问题**：BERT 和 GPT 的区别？

**答案**：

| 模型 | 架构 | 训练目标 | 适用场景 |
|------|------|---------|---------|
| **BERT** | Encoder | Masked LM, Next Sentence Prediction | 理解任务 |
| **GPT** | Decoder | Next Token Prediction | 生成任务 |

---

### 四、计算机视觉面试题

---

#### Q11: CNN vs 传统方法

**问题**：CNN 相比传统图像处理方法的优势？

**答案**：

| 维度 | 传统方法 | CNN |
|------|---------|-----|
| **特征提取** | 手工设计 | 自动学习 |
| **表达能力** | 有限 | 强大 |
| **端到端** | 否 | 是 |
| **迁移学习** | 困难 | 容易 |

---

#### Q12: CNN 卷积操作

**问题**：卷积的计算过程？

**答案**：

**计算**：
```
输出(i, j) = Σ Σ 输入(i + m, j + n) × 卷积核(m, n)
```

**示例**：
```python
import torch
import torch.nn as nn

# 卷积层
conv = nn.Conv2d(in_channels=3, out_channels=64, kernel_size=3, stride=1, padding=1)

# 输入 (batch, channels, height, width)
input_tensor = torch.randn(1, 3, 224, 224)

# 前向传播
output = conv(input_tensor)
print(f"输出形状: {output.shape}")  # (1, 64, 224, 224)
```

---

#### Q13: 常用 CNN 架构

**问题**：LeNet、AlexNet、VGG、ResNet 的演进？

**答案**：

| 架构 | 特点 | 创新 |
|------|------|------|
| **LeNet** | 5层卷积+池化 | 开创性工作 |
| **AlexNet** | 8层 | ReLU、Dropout、数据增强 |
| **VGG** | 19层 | 3×3 小卷积核 |
| **ResNet** | 152层 | 残差连接 |

---

### 五、大语言模型面试题

---

#### Q14: LLM 训练三阶段

**问题**：LLM 训练的三个阶段？

**答案**：

| 阶段 | 目标 | 数据 | 损失 |
|------|------|------|------|
| **预训练** | 学习语言规律 | 大规模文本 | Next Token Prediction |
| **指令微调** | 遵循指令 | 指令-响应对 | Cross-Entropy |
| **RLHF** | 对齐人类偏好 | 人类偏好数据 | PPO |

---

#### Q15: Transformer 架构

**问题**：Transformer 的核心组件？

**答案**：

| 组件 | 作用 |
|------|------|
| **Self-Attention** | 计算词间关系 |
| **Multi-Head** | 并行多个注意力头 |
| **Feed-Forward** | 非线性变换 |
| **Layer Norm** | 层归一化 |
| **Positional Encoding** | 位置信息 |

---

#### Q16: Temperature 参数

**问题**：LLM 中 Temperature 的作用？

**答案**：

**作用**：控制输出的随机性和确定性

| 温度 | 效果 | 适用场景 |
|------|------|---------|
| **0.1-0.3** | 更确定、一致 | 编程、翻译 |
| **0.5-0.7** | 平衡 | 通用问答 |
| **0.8-1.2** | 更随机、有创意 | 创意写作 |

```python
import torch.nn.functional as F

def sample_with_temperature(logits, temperature=0.7):
    logits = logits / temperature
    probs = F.softmax(logits, dim=-1)
    next_token = torch.multinomial(probs, num_samples=1)
    return next_token
```

---

#### Q17: RAG vs Fine-tuning

**问题**：何时使用 RAG？何时使用 Fine-tuning？

**答案**：

| 场景 | 选择 |
|------|------|
| 需要最新信息 | RAG |
| 领域特定任务 | Fine-tuning |
| 降低幻觉 | RAG |
| 改变输出格式 | Fine-tuning |
| 成本敏感 | RAG |

---

#### Q18: RLHF 三步骤

**问题**：RLHF 的三个步骤？

**答案**：

```
步骤1: 收集人类偏好数据
       → 比较 A 和 B 的输出

步骤2: 训练奖励模型（Reward Model）
       → 学习人类偏好

步骤3: 使用 PPO 微调 LLM
       → 最大化奖励
```

---

### 六、AI工程化面试题

---

#### Q19: MLOps vs DevOps

**问题**：MLOps 和 DevOps 的区别？

**答案**：

| 维度 | DevOps | MLOps |
|------|--------|-------|
| **版本** | 代码 | 代码 + 数据 + 模型 |
| **测试** | 单元测试 | 模型评估、A/B测试 |
| **部署** | 服务部署 | 模型部署 + 特征工程 |
| **监控** | 服务监控 | 模型漂移监控 |
| **复现性** | 容易 | 困难（数据、随机性） |

---

#### Q20: A/B 测试

**问题**：如何设计一个好的 A/B 测试？

**答案**：

**步骤**：
1. **确定假设**：明确要验证的假设
2. **选择指标**：选择主要和次要指标
3. **样本量计算**：确定需要的样本量
4. **随机分流**：随机分配用户到不同组
5. **运行实验**：收集足够的数据
6. **统计分析**：进行显著性检验
7. **决策**：根据结果做出决策

```python
from scipy import stats

def analyze_ab_test(control, treatment):
    """分析 A/B 测试"""
    t_stat, p_value = stats.ttest_ind(control, treatment)

    print(f"p值: {p_value:.4f}")
    if p_value < 0.05:
        print("✅ 结果显著")
    else:
        print("❌ 结果不显著")
```

---

#### Q21: 模型部署方式

**问题**：如何选择模型部署方式？

**答案**：

| 部署方式 | 适用场景 |
|---------|---------|
| **API** | 在线实时服务 |
| **批量推理** | 离线批量处理 |
| **边缘部署** | 低延迟、隐私要求高 |
| **Serverless** | 请求量不确定、成本低 |

---

#### Q22: 模型漂移

**问题**：如何检测和处理模型漂移？

**答案**：

**检测方法**：
1. **数据漂移**：监控输入数据的分布变化
2. **概念漂移**：监控输入输出的关系变化
3. **性能监控**：监控模型性能指标

**处理方法**：
1. **重新训练**：定期用新数据重新训练
2. **在线学习**：持续学习新数据
3. **模型集成**：使用多个模型
4. **回滚机制**：快速回滚到旧版本

---

### 七、综合面试题

---

#### Q23: 端到端学习 vs 分阶段学习

**问题**：端到端学习 vs 分阶段学习的优劣？

**答案**：

| 方式 | 优点 | 缺点 |
|------|------|------|
| **端到端** | 简单、联合优化 | 需要大量数据、可解释性差 |
| **分阶段** | 可解释、模块化 | 复杂、可能不是全局最优 |

---

#### Q24: 迁移学习

**问题**：什么是迁移学习？何时使用？

**答案**：

**定义**：将预训练模型的知识迁移到新任务

**使用场景**：
1. 目标任务数据较少
2. 预训练和目标任务相似
3. 节省训练时间

**方法**：
```python
import torchvision.models as models
import torch.nn as nn

# 加载预训练模型
model = models.resnet50(pretrained=True)

# 冻结部分层
for param in model.parameters():
    param.requires_grad = False

# 替换最后的全连接层
model.fc = nn.Linear(2048, 10)  # 10类分类
```

---

#### Q25: 模型集成

**问题**：模型集成的方法和优势？

**答案**：

**方法**：
1. **Bagging**：并行训练多个模型，投票
2. **Boosting**：串行训练，关注错误样本
3. **Stacking**：多个模型输出作为新模型的输入

**优势**：
- 提高准确率
- 减少过拟合
- 提高鲁棒性

```python
from sklearn.ensemble import RandomForestClassifier, VotingClassifier
from sklearn.linear_model import LogisticRegression
from sklearn.svm import SVC

# 模型集成
model = VotingClassifier(
    estimators=[
        ('rf', RandomForestClassifier()),
        ('lr', LogisticRegression()),
        ('svc', SVC(probability=True))
    ],
    voting='soft'  # 软投票
)
```

---

#### Q26: 生成模型 vs 判别模型

**问题**：生成模型 vs 判别模型的区别？

**答案**：

| 模型 | 目标 | 例子 | 特点 |
|------|------|------|------|
| **生成模型** | 学习 p(x,y) | Naive Bayes, GAN | 可生成新数据 |
| **判别模型** | 学习 p(y|x) | Logistic Regression, CNN | 分类性能更好 |

---

#### Q27: Transformer 位置编码

**问题**：Transformer 为什么需要位置编码？

**答案**：

**原因**：Self-Attention 是位置无关的，无法知道词的顺序

**方案**：
1. **绝对位置编码**：给每个位置添加位置编码
2. **相对位置编码**：编码词之间的相对距离

```python
def positional_encoding(max_len, d_model):
    """位置编码"""
    pos = np.arange(max_len)[:, np.newaxis]
    i = np.arange(d_model)[np.newaxis, :]

    angle_rates = 1 / np.power(10000, (2 * (i // 2)) / d_model)
    angle_rads = pos * angle_rates

    # 偶数位置用 sin，奇数位置用 cos
    angle_rads[:, 0::2] = np.sin(angle_rads[:, 0::2])
    angle_rads[:, 1::2] = np.cos(angle_rads[:, 1::2])

    return angle_rads
```

---

#### Q28: Attention 机制

**问题**：Attention 机制解决了什么问题？

**答案**：

**解决的问题**：
1. **信息瓶颈**：RNN 难以处理长序列
2. **并行计算**：Attention 可以并行计算
3. **可解释性**：Attention 权重显示关注点

**应用**：
- 机器翻译（Seq2Seq）
- 图像描述生成
- Transformer

---

#### Q29: 模型评估指标

**问题**：不同任务的评估指标？

**答案**：

| 任务 | 指标 |
|------|------|
| **分类** | 准确率、精确率、召回率、F1、AUC |
| **回归** | MSE、MAE、R² |
| **排名** | NDCG、MRR、Hit@K |
| **生成** | BLEU、ROUGE、Perplexity |
| **检测** | mAP、IOU |

```python
from sklearn.metrics import classification_report, roc_auc_score, mean_squared_error

# 分类
print(classification_report(y_true, y_pred))

# AUC
auc = roc_auc_score(y_true, y_prob)

# 回归
mse = mean_squared_error(y_true, y_pred)
```

---

#### Q30: 梯度消失和爆炸

**问题**：如何解决梯度消失/爆炸？

**答案**：

**问题**：
- **梯度消失**：深层网络梯度变得很小
- **梯度爆炸**：梯度变得很大

**解决方案**：
1. **激活函数**：使用 ReLU 代替 Sigmoid
2. **Batch Norm**：归一化每层的输出
3. **残差连接**：ResNet
4. **梯度裁剪**：限制梯度大小
5. **初始化**：Xavier/He 初始化

```python
# 梯度裁剪
torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=1.0)

# Batch Norm
bn = nn.BatchNorm2d(num_features=64)

# 残差连接
class ResBlock(nn.Module):
    def __init__(self, in_channels, out_channels):
        super().__init__()
        self.conv1 = nn.Conv2d(in_channels, out_channels, 3, padding=1)
        self.bn1 = nn.BatchNorm2d(out_channels)
        self.conv2 = nn.Conv2d(out_channels, out_channels, 3, padding=1)
        self.bn2 = nn.BatchNorm2d(out_channels)

    def forward(self, x):
        residual = x
        out = self.conv1(x)
        out = self.bn1(out)
        out = F.relu(out)
        out = self.conv2(out)
        out = self.bn2(out)
        out += residual  # 残差连接
        out = F.relu(out)
        return out
```

---

## 🎯 本周知识点总结

| 天数 | 主题 | 核心知识点 |
|------|------|-----------|
| **第1天** | 机器学习基础 | 过拟合、交叉验证、正则化、梯度下降 |
| **第2天** | 深度学习 | 激活函数、反向传播、Batch Norm、CNN |
| **第3天** | NLP | Word Embedding、Transformer、BERT/GPT |
| **第4天** | 计算机视觉 | CNN 架构、卷积操作、常用模型 |
| **第5天** | 大语言模型 | LLM 训练、RAG、RLHF、Temperature |
| **第6天** | AI工程化 | MLOps、部署、监控、A/B测试 |
| **第7天** | 综合面试题 | 以上所有知识点 |

---

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| 机器学习基础面试题 | 🎯 已掌握 |
| 深度学习面试题 | 🎯 已掌握 |
| NLP 面试题 | 🎯 已掌握 |
| 计算机视觉面试题 | 🎯 已掌握 |
| 大语言模型面试题 | 🎯 已掌握 |
| AI工程化面试题 | 🎯 已掌握 |
| 综合面试题 | 🎯 已掌握 |

---

## 📝 今天的作业

1. ✅ 复习本周所有知识点
2. ✅ 总结自己的薄弱环节
3. ✅ 准备面试常见问题答案
4. ✅ 练习代码实现

---

## 🚀 本周学习进度

| 天数 | 日期 | 主题 | 状态 |
|------|------|------|------|
| 第1天 | 2026-03-31 | 机器学习基础 | ✅ |
| 第2天 | 2026-04-01 | 深度学习 | ✅ |
| 第3天 | 2026-04-02 | NLP | ✅ |
| 第4天 | 2026-04-03 | 计算机视觉 | ✅ |
| 第5天 | 2026-04-04 | 大语言模型 | ✅ |
| 第6天 | 2026-04-05 | AI工程化 | ✅ |
| 第7天 | 2026-04-06 | 综合面试题 | ✅ |

---

**学习进度**：📊 第7天（本周完成！）

---

*最后更新：2026-04-06*
