# AI 知识学习日报

> 每天学习时间：30分钟 | 学习主题：机器学习基础 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-04-07 周二 - 第1天

---

### 🎯 今日主题：机器学习基础

---

## 📖 学习内容（30分钟）

---

### 1. 什么是机器学习？

**定义**：让计算机从数据中学习规律，无需显式编程

**核心要素**：
- 📊 **数据**：学习的原材料
- 🧠 **模型**：学习的数学表达
- 🎯 **算法**：学习的方法
- 📉 **损失函数**：评估学习效果

---

### 2. 机器学习分类

#### 按监督方式分类

| 类型 | 标签 | 例子 | 应用 |
|------|------|------|------|
| **监督学习** | 有 | 图像分类、垃圾邮件检测 | 大多数任务 |
| **无监督学习** | 无 | 聚类、降维 | 数据分析 |
| **半监督学习** | 部分有 | 文本分类（少量标注） | 标注成本高 |
| **强化学习** | 奖励 | 游戏、机器人 | 决策任务 |

---

#### 按任务类型分类

| 任务类型 | 输出类型 | 例子 |
|---------|---------|------|
| **分类** | 类别标签 | 猫/狗分类、情感分析 |
| **回归** | 连续值 | 房价预测、温度预测 |
| **聚类** | 簇标签 | 客户分群、异常检测 |
| **降维** | 低维表示 | 可视化、特征提取 |

---

### 3. 监督学习

#### 分类任务

**例子**：垃圾邮件检测

```python
from sklearn.linear_model import LogisticRegression
from sklearn.model_selection import train_test_split

# 准备数据
X = [[1.0, 0.5], [0.5, 1.0], [1.5, 0.8], [0.8, 0.2]]  # 特征
y = [1, 1, 1, 0]  # 标签（1:正常，0:垃圾）

# 分割数据
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.25)

# 训练模型
model = LogisticRegression()
model.fit(X_train, y_train)

# 预测
y_pred = model.predict(X_test)
print(f"预测结果: {y_pred}")
print(f"准确率: {model.score(X_test, y_test):.2f}")
```

---

#### 回归任务

**例子**：房价预测

```python
from sklearn.linear_model import LinearRegression
import numpy as np

# 准备数据
# 特征：[面积(平米), 房间数]
X = np.array([[100, 3], [150, 4], [80, 2], [200, 5]])
# 标签：价格(万元)
y = np.array([500, 800, 400, 1000])

# 训练模型
model = LinearRegression()
model.fit(X, y)

# 预测
new_house = [[120, 3]]  # 120平米，3室
price = model.predict(new_house)[0]
print(f"预测价格: {price:.2f}万元")
```

---

### 4. 无监督学习

#### 聚类（K-Means）

```python
from sklearn.cluster import KMeans
import numpy as np

# 准备数据
X = np.array([[1, 1], [1.5, 2], [5, 5], [6, 6]])

# 训练模型
kmeans = KMeans(n_clusters=2, random_state=42)
kmeans.fit(X)

# 预测
labels = kmeans.labels_
centers = kmeans.cluster_centers_

print(f"聚类标签: {labels}")
print(f"聚类中心: {centers}")
```

---

#### 降维（PCA）

```python
from sklearn.decomposition import PCA
import numpy as np

# 准备数据
X = np.array([[1, 2, 3, 4],
              [5, 6, 7, 8],
              [9, 10, 11, 12]])

# 降维到2维
pca = PCA(n_components=2)
X_reduced = pca.fit_transform(X)

print(f"原始维度: {X.shape}")
print(f"降维后维度: {X_reduced.shape}")
print(f"解释方差比: {pca.explained_variance_ratio_}")
```

---

### 5. 模型评估

#### 分类评估指标

```python
from sklearn.metrics import classification_report, confusion_matrix

y_true = [1, 0, 1, 1, 0, 0]
y_pred = [1, 0, 0, 1, 0, 1]

# 混淆矩阵
cm = confusion_matrix(y_true, y_pred)
print("混淆矩阵:")
print(cm)

# 详细报告
print("\n分类报告:")
print(classification_report(y_true, y_pred))
```

---

#### 回归评估指标

```python
from sklearn.metrics import mean_squared_error, mean_absolute_error, r2_score

y_true = [3.0, -0.5, 2.0, 7.0]
y_pred = [2.5, 0.0, 2.0, 8.0]

mse = mean_squared_error(y_true, y_pred)
mae = mean_absolute_error(y_true, y_pred)
r2 = r2_score(y_true, y_pred)

print(f"均方误差(MSE): {mse:.4f}")
print(f"平均绝对误差(MAE): {mae:.4f}")
print(f"决定系数(R²): {r2:.4f}")
```

---

### 6. 交叉验证

**目的**：更准确评估模型泛化能力

```python
from sklearn.model_selection import cross_val_score
from sklearn.linear_model import LogisticRegression
import numpy as np

# 准备数据
X = np.random.randn(100, 5)
y = np.random.randint(0, 2, 100)

# 训练模型
model = LogisticRegression()

# 5折交叉验证
scores = cross_val_score(model, X, y, cv=5)

print(f"各折准确率: {scores}")
print(f"平均准确率: {scores.mean():.4f}")
print(f"标准差: {scores.std():.4f}")
```

---

### 7. 正则化

**目的**：防止过拟合

```python
from sklearn.linear_model import Lasso, Ridge
import numpy as np

# 准备数据
X = np.random.randn(100, 10)
y = np.random.randn(100)

# L1 正则化 (Lasso)
lasso = Lasso(alpha=0.1)  # alpha 控制正则化强度
lasso.fit(X, y)

# L2 正则化 (Ridge)
ridge = Ridge(alpha=0.1)
ridge.fit(X, y)

print("Lasso 系数 (稀疏):")
print(lasso.coef_)

print("\nRidge 系数 (平滑):")
print(ridge.coef_)
```

---

## 💻 实战练习（15分钟）

---

### 练习1：鸢尾花分类

```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score, classification_report

# 加载数据
iris = load_iris()
X = iris.data
y = iris.target

# 分割数据
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# 训练模型
model = LogisticRegression(max_iter=200)
model.fit(X_train, y_train)

# 预测
y_pred = model.predict(X_test)

# 评估
accuracy = accuracy_score(y_test, y_pred)
print(f"准确率: {accuracy:.4f}")
print("\n分类报告:")
print(classification_report(y_test, y_pred, target_names=iris.target_names))
```

---

### 练习2：波士顿房价预测

```python
from sklearn.datasets import fetch_california_housing
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.metrics import mean_squared_error, r2_score

# 加载数据
housing = fetch_california_housing()
X = housing.data
y = housing.target

# 分割数据
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# 训练模型
model = LinearRegression()
model.fit(X_train, y_train)

# 预测
y_pred = model.predict(X_test)

# 评估
mse = mean_squared_error(y_test, y_pred)
r2 = r2_score(y_test, y_pred)

print(f"均方误差(MSE): {mse:.4f}")
print(f"决定系数(R²): {r2:.4f}")
```

---

### 练习3：客户聚类

```python
from sklearn.datasets import make_blobs
from sklearn.cluster import KMeans
import matplotlib.pyplot as plt

# 生成数据
X, _ = make_blobs(n_samples=300, centers=4, cluster_std=0.60, random_state=0)

# 训练模型
kmeans = KMeans(n_clusters=4)
kmeans.fit(X)

# 预测
y_kmeans = kmeans.predict(X)

# 聚类中心
centers = kmeans.cluster_centers_

# 可视化
plt.scatter(X[:, 0], X[:, 1], c=y_kmeans, s=50, cmap='viridis')
plt.scatter(centers[:, 0], centers[:, 1], c='red', s=200, alpha=0.75)
plt.title('K-Means 聚类')
plt.xlabel('Feature 1')
plt.ylabel('Feature 2')
plt.show()
```

---

### 练习4：交叉验证对比

```python
from sklearn.datasets import load_digits
from sklearn.model_selection import cross_val_score
from sklearn.linear_model import LogisticRegression
from sklearn.ensemble import RandomForestClassifier

# 加载数据
digits = load_digits()
X = digits.data
y = digits.target

# 模型1: 逻辑回归
lr = LogisticRegression(max_iter=1000)
lr_scores = cross_val_score(lr, X, y, cv=5)

# 模型2: 随机森林
rf = RandomForestClassifier(n_estimators=100)
rf_scores = cross_val_score(rf, X, y, cv=5)

print("逻辑回归:")
print(f"平均准确率: {lr_scores.mean():.4f}")
print(f"标准差: {lr_scores.std():.4f}")

print("\n随机森林:")
print(f"平均准确率: {rf_scores.mean():.4f}")
print(f"标准差: {rf_scores.std():.4f}")
```

---

## 🎯 面试精选

---

### Q1: 过拟合 vs 欠拟合

**问题**：如何判断和解决过拟合/欠拟合？

**答案**：

| 现象 | 训练集准确率 | 验证集准确率 | 解决方法 |
|------|-------------|-------------|---------|
| **过拟合** | 高 | 低 | 正则化、Dropout、增加数据、早停 |
| **欠拟合** | 低 | 低 | 增加模型复杂度、减少正则化、特征工程 |

**判断方法**：
```python
# 训练集和验证集的准确率差异大 → 过拟合
# 训练集准确率本身就低 → 欠拟合
```

---

### Q2: 交叉验证

**问题**：为什么需要交叉验证？常用方法有哪些？

**答案**：

**目的**：更准确评估模型泛化能力

**常用方法**：
1. **K-Fold CV**：将数据分成 K 份，轮流作验证集
2. **Stratified K-Fold**：保持类别分布
3. **Leave-One-Out**：每次留一个样本

---

### Q3: L1 vs L2 正则化

**问题**：L1 和 L2 正则化的区别？

**答案**：

| 正则化 | 公式 | 特点 | 用途 |
|--------|------|------|------|
| **L1** | Σ|w| | 稀疏解 | 特征选择 |
| **L2** | Σw² | 平滑解 | 防止过拟合 |

**应用**：
```python
from sklearn.linear_model import Lasso, Ridge

# L1 正则化（稀疏）
lasso = Lasso(alpha=0.1)

# L2 正则化（平滑）
ridge = Ridge(alpha=0.1)
```

---

### Q4: 偏差 vs 方差

**问题**：偏差和方差的权衡？

**答案**：

| 模型 | 偏差 | 方差 | 特点 |
|------|------|------|------|
| **简单模型** | 高 | 低 | 欠拟合 |
| **复杂模型** | 低 | 高 | 过拟合 |
| **最佳模型** | 低 | 低 | 适中复杂度 |

**目标**：找到偏差和方差的最佳平衡点

---

### Q5: 分类 vs 回归

**问题**：分类和回归的区别？

**答案**：

| 任务类型 | 输出 | 例子 | 评估指标 |
|---------|------|------|---------|
| **分类** | 离散标签 | 猫/狗分类 | 准确率、F1、AUC |
| **回归** | 连续值 | 房价预测 | MSE、MAE、R² |

---

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| 机器学习定义和分类 | 🎯 已掌握 |
| 监督学习（分类、回归） | 🎯 已掌握 |
| 无监督学习（聚类、降维） | 🎯 已掌握 |
| 模型评估指标 | 🎯 已掌握 |
| 交叉验证 | 🎯 已掌握 |
| 正则化（L1、L2） | 🎯 已掌握 |
| 偏差-方差权衡 | 🎯 已理解 |

---

## 📝 今天的作业

1. ✅ 完成鸢尾花分类练习
2. ✅ 完成房价预测练习
3. ✅ 尝试不同的聚类算法
4. ✅ 对比不同模型的交叉验证结果

---

## 🚀 本周学习进度

| 天数 | 日期 | 主题 | 状态 |
|------|------|------|------|
| 第1天 | 2026-04-07 | 机器学习基础 | ✅ |
| 第2天 | 2026-04-08 | 深度学习 | 📅 |
| 第3天 | 2026-04-09 | NLP | ⏳ |
| 第4天 | 2026-04-10 | 计算机视觉 | ⏳ |
| 第5天 | 2026-04-11 | 大语言模型 | ⏳ |
| 第6天 | 2026-04-12 | AI工程化 | ⏳ |
| 第7天 | 2026-04-13 | 综合面试题 | ⏳ |

---

**学习进度**：📊 第1天

---

*最后更新：2026-04-07*
