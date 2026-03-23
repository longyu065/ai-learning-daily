# AI 知识学习日报

> 每天学习时间：30分钟 | 学习主题：机器学习基础 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-24 周一 - 第1天

---

### 🎯 今日主题：机器学习基础（第2周回顾）

---

## 📖 第一部分：机器学习核心概念（15分钟）

---

### 1. 机器学习三要素

**机器学习 = 数据 + 算法 + 算力**

| 要素 | 说明 | 示例 |
|------|------|------|
| **数据** | 训练和测试样本 | 图片、文本、表格数据 |
| **算法** | 学习规律的方法 | 决策树、神经网络 |
| **算力** | 计算能力 | GPU、TPU、CPU |

**类比**：
- 数据：学生的教材
- 算法：学习方法
- 算力：学生的学习时间

---

### 2. 监督学习详解

**定义**：使用有标签的数据训练模型

**任务类型**：

| 任务 | 目标 | 典型算法 |
|------|------|---------|
| **分类** | 预测离散类别 | 逻辑回归、SVM、随机森林 |
| **回归** | 预测连续值 | 线性回归、决策树 |
| **序列标注** | 预测标签序列 | CRF、HMM |

**分类任务示例**：

```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier

# 加载数据（鸢尾花分类）
data = load_iris()
X = data.data  # 特征：花萼长度、宽度等
y = data.target  # 标签：3种鸢尾花

# 拆分数据
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

# 训练模型
model = RandomForestClassifier()
model.fit(X_train, y_train)

# 评估
accuracy = model.score(X_test, y_test)
print(f"准确率: {accuracy:.2%}")
# 输出：准确率: 96.67%
```

---

### 3. 无监督学习详解

**定义**：使用无标签的数据发现规律

**任务类型**：

| 任务 | 目标 | 典型算法 |
|------|------|---------|
| **聚类** | 将相似数据分组 | K-Means、DBSCAN |
| **降维** | 减少特征数量 | PCA、t-SNE |
| **关联规则** | 发现数据关联 | Apriori、FP-Growth |

**聚类任务示例**：

```python
from sklearn.cluster import KMeans
import matplotlib.pyplot as plt

# 生成数据
import numpy as np
np.random.seed(42)
group1 = np.random.randn(50, 2) + [2, 2]
group2 = np.random.randn(50, 2) + [-2, -2]
X = np.vstack([group1, group2])

# K-Means 聚类
model = KMeans(n_clusters=2, random_state=42)
labels = model.fit_predict(X)

# 可视化
plt.scatter(X[:, 0], X[:, 1], c=labels, cmap='viridis')
plt.scatter(model.cluster_centers_[:, 0],
           model.cluster_centers_[:, 1],
           c='red', marker='x', s=200, label='中心点')
plt.legend()
plt.title("K-Means 聚类")
plt.show()
```

---

### 4. 强化学习简介

**定义**：通过与环境交互，学习最优策略

**核心概念**：

| 概念 | 说明 |
|------|------|
| **Agent** | 智能体（学习者） |
| **Environment** | 环境 |
| **State** | 状态 |
| **Action** | 动作 |
| **Reward** | 奖励 |

**应用场景**：
- 游戏 AI（AlphaGo）
- 机器人控制
- 自动驾驶
- 推荐系统

**简单示例**：

```python
import random

# 简单的强化学习环境
class SimpleEnv:
    def __init__(self):
        self.state = 0
        self.goal = 5

    def reset(self):
        self.state = 0
        return self.state

    def step(self, action):
        # action: 0=左移, 1=右移
        if action == 1:
            self.state += 1
        elif action == 0:
            self.state -= 1

        # 奖励
        if self.state == self.goal:
            reward = 1
            done = True
        elif self.state < 0 or self.state > 10:
            reward = -1
            done = True
        else:
            reward = 0
            done = False

        return self.state, reward, done

# 随机策略
env = SimpleEnv()
state = env.reset()
total_reward = 0

for _ in range(100):
    action = random.choice([0, 1])  # 随机动作
    state, reward, done = env.step(action)
    total_reward += reward
    if done:
        state = env.reset()

print(f"总奖励: {total_reward}")
```

---

### 5. 评估指标

#### 分类指标

| 指标 | 公式 | 说明 |
|------|------|------|
| **准确率** | (TP+TN)/(TP+TN+FP+FN) | 整体正确率 |
| **精确率** | TP/(TP+FP) | 预测为正的准确性 |
| **召回率** | TP/(TP+FN) | 实际为正的覆盖率 |
| **F1** | 2×精确率×召回率/(精确率+召回率) | 平衡精确率和召回率 |

```python
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score

y_true = [1, 1, 0, 1, 0]
y_pred = [1, 1, 1, 0, 0]

print(f"准确率: {accuracy_score(y_true, y_pred):.2%}")
print(f"精确率: {precision_score(y_true, y_pred):.2%}")
print(f"召回率: {recall_score(y_true, y_pred):.2%}")
print(f"F1: {f1_score(y_true, y_pred):.2%}")
```

---

### 6. 交叉验证

**目的**：更准确地评估模型性能

**K-Fold 交叉验证**：

```python
from sklearn.model_selection import cross_val_score
from sklearn.ensemble import RandomForestClassifier

# 加载数据
data = load_iris()
X, y = data.data, data.target

# 模型
model = RandomForestClassifier()

# 5折交叉验证
scores = cross_val_score(model, X, y, cv=5)

print(f"每折准确率: {scores}")
print(f"平均准确率: {scores.mean():.2%}")
print(f"标准差: {scores.std():.2%}")
```

---

## 💻 第二部分：实战练习（15分钟）

---

### 练习1：手写数字识别

```python
from sklearn.datasets import load_digits
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import classification_report

# 加载数据
digits = load_digits()
X = digits.data  # 64个像素特征
y = digits.target  # 0-9的数字

print(f"数据形状: {X.shape}")
print(f"示例图片:")
print(digits.images[0])

# 拆分数据
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# 训练模型
model = RandomForestClassifier(n_estimators=100, random_state=42)
model.fit(X_train, y_train)

# 预测
y_pred = model.predict(X_test)

# 评估
print("\n分类报告:")
print(classification_report(y_test, y_pred))
```

---

### 练习2：客户分群

```python
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler
import numpy as np

# 模拟客户数据
np.random.seed(42)
n_customers = 300
spending = np.random.normal(1000, 300, n_customers)  # 消费金额
frequency = np.random.poisson(10, n_customers)       # 消费频率

X = np.column_stack([spending, frequency])

# 标准化
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

# K-Means 聚类
model = KMeans(n_clusters=3, random_state=42)
labels = model.fit_predict(X_scaled)

# 分析结果
import pandas as pd
df = pd.DataFrame({
    'spending': spending,
    'frequency': frequency,
    'cluster': labels
})

print("\n客户群体分析:")
print(df.groupby('cluster').agg({
    'spending': ['mean', 'std'],
    'frequency': ['mean', 'std'],
    'cluster': 'count'
}))
```

---

### 练习3：房价预测

```python
from sklearn.datasets import fetch_california_housing
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_squared_error, r2_score

# 加载数据
data = fetch_california_housing()
X = data.data
y = data.target

print(f"特征名称: {data.feature_names}")

# 拆分数据
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=42
)

# 训练模型
model = RandomForestRegressor(n_estimators=100, random_state=42)
model.fit(X_train, y_train)

# 预测
y_pred = model.predict(X_test)

# 评估
mse = mean_squared_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)

print(f"\n均方误差 (MSE): {mse:.4f}")
print(f"R² 分数: {r2:.4f}")

# 特征重要性
importance = model.feature_importances_
print("\n特征重要性:")
for name, imp in zip(data.feature_names, importance):
    print(f"{name:20s}: {imp:.4f}")
```

---

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| 机器学习三要素 | 🎯 已理解 |
| 监督学习任务 | 🎯 已掌握 |
| 无监督学习任务 | 🎯 已掌握 |
| 强化学习基础 | 🎯 已了解 |
| 评估指标 | 🎯 已掌握 |
| 交叉验证 | 🎯 已掌握 |

---

## 📝 今天的作业

1. ✅ 运行手写数字识别示例
2. ✅ 尝试不同的 K 值进行聚类
3. ✅ 比较不同模型的性能

---

## 🚀 本周学习计划

| 天数 | 日期 | 主题 |
|------|------|------|
| 第1天 | 2026-03-24 | 机器学习基础 |
| 第2天 | 2026-03-25 | 深度学习 |
| 第3天 | 2026-03-26 | NLP |
| 第4天 | 2026-03-27 | 计算机视觉 |
| 第5天 | 2026-03-28 | 大语言模型 |
| 第6天 | 2026-03-29 | AI工程化 |
| 第7天 | 2026-03-30 | 综合面试题 |

---

**学习进度**：📊 第1天/第2周

---

*最后更新：2026-03-24*
