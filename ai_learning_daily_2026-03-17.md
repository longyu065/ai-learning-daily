# AI 知识学习日报

> 学习路径：7天循环 | 推送时间：每天 08:30 (UTC) | 起始日期：2026-03-17

---

## 📅 2026-03-17 周一 - 第1天

### 🎯 主题：机器学习基础（新人入门版）

---

### 📚 核心知识点

#### 1. 什么是机器学习？

**通俗解释**：
- 传统编程：告诉计算机"怎么做"（写规则）
- 机器学习：告诉计算机"要什么"，让它自己学习"怎么做"（给数据）

**示例对比**：

| 传统编程 | 机器学习 |
|---------|---------|
| `if 图片有猫的耳朵: return "这是猫"` | 训练模型识别猫 |
| `if 图片有狗的耳朵: return "这是狗"` | 模型自动学会区分猫狗 |

---

#### 2. 监督学习 vs 无监督学习

##### 监督学习（有老师教）

**定义**：给模型提供数据的同时，也提供正确答案（标签）

**示例**：
```
输入数据          标签（答案）
------------------------
[图片1.jpg]  →  "猫"
[图片2.jpg]  →  "狗"
[图片3.jpg]  →  "猫"
```

模型学习：看到这些图片后，学会如何区分猫和狗。

**常用算法**：
- **逻辑回归（Logistic Regression）**：二分类，预测是/否
  - 示例：预测邮件是否为垃圾邮件
  - 输出：概率值（0-1之间）

- **支持向量机（SVM）**：寻找最佳分隔线
  - 示例：分类图片中的物体
  - 特点：在高维空间表现好

- **决策树**：像人做决策一样，一层层判断
  ```
  是否有胡须？
    ├─ 是 → 是否有尖耳朵？
    │       ├─ 是 → 猫
    │       └─ 否 → 狮子
    └─ 否 → 是否会叫？
            ├─ 是 → 狗
            └─ 否 → 其他动物
  ```

- **随机森林**：多棵决策树投票，更准确
  - 原理：创建100棵决策树，每棵树的判断可能不同，取多数票
  - 优点：比单棵树更稳定，不易过拟合

**代码示例（决策树分类）**：
```python
from sklearn.tree import DecisionTreeClassifier
from sklearn.datasets import load_iris

# 加载数据（鸢尾花分类）
data = load_iris()
X, y = data.data, data.target

# 创建并训练模型
model = DecisionTreeClassifier()
model.fit(X, y)

# 预测
prediction = model.predict([[5.1, 3.5, 1.4, 0.2]])
print(f"预测结果: {data.target_names[prediction[0]]}")
# 输出：预测结果: setosa
```

---

##### 无监督学习（自学）

**定义**：给模型提供数据，但不提供答案，让模型自己发现数据中的规律

**示例**：
```
输入数据（没有标签）
------------------------
[图片1.jpg]
[图片2.jpg]
[图片3.jpg]
```

模型学习：自动发现这些图片可以分为几类。

**常用算法**：
- **K-Means 聚类**：将数据分成 K 个组
  - 示例：客户细分，将用户分为"高价值"、"中价值"、"低价值"
  - 步骤：
    1. 随机选择 K 个中心点
    2. 计算每个点到各中心的距离，分配到最近的中心
    3. 重新计算每个组的中心
    4. 重复2-3步，直到中心点不再变化

**代码示例（K-Means 聚类）**：
```python
from sklearn.cluster import KMeans
import numpy as np

# 生成随机数据
X = np.array([[1, 2], [1, 4], [1, 0],
              [10, 2], [10, 4], [10, 0]])

# 创建并训练模型（分成2组）
model = KMeans(n_clusters=2)
model.fit(X)

# 查看每个点属于哪个组
print(f"聚类结果: {model.labels_}")
# 输出：聚类结果: [1 1 1 0 0 0]
#     前3个点属于组1，后3个点属于组0
```

- **PCA（主成分分析）**：降维
  - 示例：将100个特征压缩到10个特征
  - 用途：数据可视化、减少计算量

---

#### 3. 评估指标（如何判断模型好坏？）

##### 混淆矩阵

以"垃圾邮件分类"为例：

| | 预测为垃圾邮件 | 预测为正常邮件 |
|---|---|---|
| **实际是垃圾邮件** | TP（真正例） | FN（假负例） |
| **实际是正常邮件** | FP（假正例） | TN（真负例） |

**解释**：
- **TP (True Positive)**：正确识别出垃圾邮件
- **FN (False Negative)**：垃圾邮件被误判为正常（漏掉）
- **FP (False Positive)**：正常邮件被误判为垃圾（误报）
- **TN (True Negative)**：正确识别出正常邮件

---

##### 常用指标

| 指标 | 公式 | 含义 | 适用场景 |
|------|------|------|----------|
| **Accuracy（准确率）** | (TP+TN) / 总数 | 所有预测中正确的比例 | 类别平衡时 |
| **Precision（精确率）** | TP / (TP+FP) | 预测为正例的样本中，真正是正例的比例 | 希望减少误报 |
| **Recall（召回率）** | TP / (TP+FN) | 所有正例中，被正确预测的比例 | 希望减少漏报 |
| **F1-score** | 2×P×R / (P+R) | 精确率和召回率的调和平均 | 需要平衡时 |

**通俗解释**：
- **精确率**：说"是垃圾邮件"的邮件中，真的有多少是垃圾邮件？
  - 高精确率 = 很少把正常邮件误判为垃圾
- **召回率**：所有垃圾邮件中，被你找出了多少？
  - 高召回率 = 很少漏掉垃圾邮件

**代码示例**：
```python
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score

# 真实标签和预测结果
y_true = [1, 1, 0, 0, 1]
y_pred = [1, 1, 1, 0, 0]

print(f"准确率: {accuracy_score(y_true, y_pred):.2f}")
print(f"精确率: {precision_score(y_true, y_pred):.2f}")
print(f"召回率: {recall_score(y_true, y_pred):.2f}")
print(f"F1-score: {f1_score(y_true, y_pred):.2f}")
```

---

### 💡 实操练习（推荐新人尝试）

#### 练习1：手写数字识别（入门级）

使用经典的 MNIST 数据集，训练一个简单的模型识别手写数字。

**步骤**：
```python
from sklearn.datasets import load_digits
from sklearn.model_selection import train_test_split
from sklearn.ensemble import RandomForestClassifier
from sklearn.metrics import accuracy_score

# 1. 加载数据（1792张手写数字图片）
digits = load_digits()
X, y = digits.data, digits.target

# 2. 拆分数据（80%训练，20%测试）
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2)

# 3. 创建模型（随机森林）
model = RandomForestClassifier()

# 4. 训练模型
model.fit(X_train, y_train)

# 5. 预测
y_pred = model.predict(X_test)

# 6. 评估
print(f"准确率: {accuracy_score(y_test, y_pred):.2f}")
```

**预期结果**：准确率约 95% 左右

---

#### 练习2：鸢尾花分类（入门级）

使用经典的鸢尾花数据集，预测花的品种。

**步骤**：
```python
from sklearn.datasets import load_iris
from sklearn.tree import DecisionTreeClassifier
from sklearn.tree import export_text

# 1. 加载数据
iris = load_iris()
X, y = iris.data, iris.target

# 2. 训练决策树
model = DecisionTreeClassifier(max_depth=3)
model.fit(X, y)

# 3. 查看决策规则
print("决策树规则：")
print(export_text(model, feature_names=iris.feature_names))

# 4. 预测新样本
new_flower = [[5.1, 3.5, 1.4, 0.2]]  # 花萼长、宽，花瓣长、宽
prediction = model.predict(new_flower)
print(f"预测品种: {iris.target_names[prediction[0]]}")
```

---

### 🎯 面试题（新人必背）

#### 题目1：如何处理类别不平衡问题？

**场景**：100封邮件中，95封正常，5封垃圾邮件。模型全部预测为"正常"，准确率95%，但实际上垃圾邮件全部漏掉了。

**解决方案**：

| 方法 | 说明 | 实现方式 |
|------|------|----------|
| **欠采样** | 减少多数类样本 | 随机删除正常邮件 |
| **过采样** | 增加少数类样本 | 复制垃圾邮件或 SMOTE |
| **调整类别权重** | 给少数类更高权重 | `class_weight='balanced'` |
| **使用不同评估指标** | 不看准确率，看F1、AUC | 适合不平衡场景 |

**代码示例**：
```python
from sklearn.linear_model import LogisticRegression

# 方法1：调整类别权重
model = LogisticRegression(class_weight='balanced')
model.fit(X_train, y_train)

# 方法2：使用 SMOTE 过采样
from imblearn.over_sampling import SMOTE
smote = SMOTE()
X_resampled, y_resampled = smote.fit_resample(X_train, y_train)
model.fit(X_resampled, y_resampled)
```

---

#### 题目2：L1 正则化和 L2 正则化的区别？

**核心区别**：

| 特性 | L1 正则化 (Lasso) | L2 正则化 (Ridge) |
|------|------------------|------------------|
| **公式** | `|w|` | `|w|²` |
| **效果** | 特征稀疏化（很多权重为0） | 权重衰减（权重变小） |
| **用途** | 特征选择 | 防止过拟合 |
| **示例** | 100个特征 → 只用10个 | 100个特征 → 全部使用，权重减小 |

**通俗解释**：
- L1：像"断舍离"，不重要的特征直接丢弃（权重为0）
- L2：像"减肥"，所有特征都保留，但权重变小

**代码示例**：
```python
from sklearn.linear_model import Lasso, Ridge

# L1 正则化（特征选择）
lasso = Lasso(alpha=0.1)
lasso.fit(X_train, y_train)
print(f"L1 非零特征数: {sum(lasso.coef_ != 0)}")

# L2 正则化（防止过拟合）
ridge = Ridge(alpha=0.1)
ridge.fit(X_train, y_train)
print(f"L2 非零特征数: {sum(ridge.coef_ != 0)}")
```

---

#### 题目3：解释过拟合和欠拟合及解决方法？

**图示理解**：

```
欠拟合          适拟合          过拟合
   O               O              O
    O             O O           OO
     O           O   O         O O
      O         O     O       O O
       O       O       O     O   O
       │       │       │     │
       └───┬───┴───┬───┴─────┘
           └───┬───┘
            高偏差
             简单模型
              欠拟合
                              高方差
                            复杂模型
                             过拟合
```

**现象对比**：

| 现象 | 训练集表现 | 测试集表现 | 原因 |
|------|-----------|-----------|------|
| **欠拟合** | 差 | 差 | 模型太简单 |
| **适拟合** | 好 | 好 | 刚刚好 |
| **过拟合** | 很好 | 差 | 模型太复杂，学到了噪声 |

**解决方法**：

| 问题 | 解决方法 |
|------|----------|
| **欠拟合** | 增加模型复杂度（更多特征、更深网络）<br>减少正则化<br>特征工程 |
| **过拟合** | 增加数据量<br>正则化（L1/L2）<br>Dropout（深度学习）<br>早停（Early Stopping）<br>数据增强 |

**代码示例**：
```python
from sklearn.tree import DecisionTreeClassifier

# 欠拟合模型（太简单）
model_under = DecisionTreeClassifier(max_depth=2)  # 只能分2层
model_under.fit(X_train, y_train)

# 适拟合模型（刚刚好）
model_good = DecisionTreeClassifier(max_depth=5)
model_good.fit(X_train, y_train)

# 过拟合模型（太复杂）
model_over = DecisionTreeClassifier(max_depth=100)  # 几乎记住每个样本
model_over.fit(X_train, y_train)

# 评估
print(f"欠拟合 - 训练集: {model_under.score(X_train, y_train):.2f}, 测试集: {model_under.score(X_test, y_test):.2f}")
print(f"适拟合 - 训练集: {model_good.score(X_train, y_train):.2f}, 测试集: {model_good.score(X_test, y_test):.2f}")
print(f"过拟合 - 训练集: {model_over.score(X_train, y_train):.2f}, 测试集: {model_over.score(X_test, y_test):.2f}")
```

---

## 📝 今日小结

| 知识点 | 关键内容 |
|--------|----------|
| **监督学习** | 有标签数据：LR、SVM、决策树、随机森林 |
| **无监督学习** | 无标签数据：K-Means、PCA |
| **评估指标** | Accuracy、Precision、Recall、F1-score |
| **类别不平衡** | 过采样、欠采样、调整权重 |
| **正则化** | L1（特征选择）、L2（防止过拟合） |
| **过/欠拟合** | 增减复杂度、正则化、数据量 |

---

## 🚀 下一步行动

1. ✅ **理解核心概念**：监督 vs 无监督、过拟合 vs 欠拟合
2. ✅ **跑通示例代码**：手写数字识别、鸢尾花分类
3. ✅ **背诵面试题**：3道必考题的答案
4. ✅ **尝试修改参数**：改变决策树深度，观察准确率变化

---

**学习进度**：📊 1/7 天完成

明天（周二）将开始 **第2天：深度学习** - 从入门到神经网络

---

---

*最后更新：2026-03-17*
