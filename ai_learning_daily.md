# AI 知识学习日报

> 学习路径：7天循环 | 推送时间：每天 08:30 (UTC)

---

## 📅 2025-03-16 周日 - 第7天

### 🎯 主题：综合面试题（算法+代码+系统设计）

---

### 1️⃣ 算法代码题

**题目：手写 K-Means 聚类算法**

```python
import numpy as np

class KMeans:
    def __init__(self, n_clusters, max_iter=300):
        self.n_clusters = n_clusters
        self.max_iter = max_iter

    def fit(self, X):
        # 随机初始化质心
        self.centroids = X[np.random.choice(X.shape[0], self.n_clusters, replace=False)]

        for _ in range(self.max_iter):
            # 分配样本到最近的质心
            labels = self._assign_labels(X)

            # 计算新质心
            new_centroids = np.array([X[labels == k].mean(axis=0) for k in range(self.n_clusters)])

            # 收敛判断
            if np.allclose(self.centroids, new_centroids):
                break
            self.centroids = new_centroids

    def _assign_labels(self, X):
        distances = np.linalg.norm(X[:, np.newaxis] - self.centroids, axis=2)
        return np.argmin(distances, axis=1)
```

**关键点**：
- 初始化：随机选择 k 个样本作为质心
- 分配：计算每个样本到各质心的距离，分配到最近的
- 更新：计算每个簇的新质心（均值）
- 收敛：质心不再变化或达到最大迭代次数

---

### 2️⃣ 系统设计题

**题目：设计一个推荐系统架构**

```
                    ┌─────────────────┐
                    │   用户请求      │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  API Gateway    │
                    └────────┬────────┘
                             │
         ┌───────────────────┼───────────────────┐
         │                   │                   │
  ┌──────▼──────┐    ┌──────▼──────┐    ┌──────▼──────┐
  │ 用户服务    │    │ 物品服务    │    │ 召回服务    │
  │ (用户画像)  │    │ (物品特征)  │    │ (多路召回)  │
  └─────────────┘    └─────────────┘    └──────┬──────┘
                                                │
                                        ┌───────▼───────┐
                                        │  排序服务     │
                                        │ (精排模型)    │
                                        └───────┬───────┘
                                                │
                                        ┌───────▼───────┐
                                        │  重排服务     │
                                        │ (多样性)      │
                                        └───────┬───────┘
                                                │
                                        ┌───────▼───────┐
                                        │  实时特征库   │
                                        │  (Redis)      │
                                        └───────────────┘
```

**核心组件**：

| 组件 | 功能 | 常用技术 |
|------|------|----------|
| **召回层** | 从百万级物品中筛选出百级候选 | 协同过滤、内容召回、向量召回（FAISS） |
| **排序层** | 对候选物品打分排序 | Wide&Deep、DIN、DeepFM |
| **重排层** | 保证推荐结果的多样性 | MMR、打散规则 |
| **实时特征** | 用户实时行为特征 | Redis、Flink |

---

### 3️⃣ 项目实战题

**题目：如何从零开始训练一个图像分类模型？**

**流程**：

```
1. 数据准备
   ├── 数据收集：爬虫、公开数据集（ImageNet、COCO）
   ├── 数据清洗：去重、标注检查
   └── 数据增强：随机裁剪、旋转、颜色抖动

2. 模型选择
   ├── Baseline：ResNet-50
   ├── 优化：EfficientNet、ConvNeXt
   └── 预训练权重：使用 ImageNet 预训练

3. 训练策略
   ├── 损失函数：CrossEntropy + Label Smoothing
   ├── 优化器：AdamW + Cosine Annealing
   ├── 学习率：Warmup + 衰减
   └── 正则化：Dropout、Weight Decay

4. 评估优化
   ├── 指标：Top-1 Accuracy、Top-5 Accuracy
   ├── 混淆矩阵分析：错分类型
   └── 模型集成：多个模型投票

5. 部署上线
   ├── 模型量化：FP16/INT8
   ├── 推理框架：ONNX Runtime / TensorRT
   └── 服务封装：TensorFlow Serving
```

---

## 📝 今日小结

| 题型 | 关键点 |
|------|--------|
| 算法代码 | K-Means：初始化→分配→更新→收敛 |
| 系统设计 | 推荐：召回→排序→重排→实时特征 |
| 项目实战 | 图像分类：数据→模型→训练→评估→部署 |

---

**学习进度**：📊 1/7 天完成

明天（周一）将开始 **第1天：机器学习基础**

---

---

*最后更新：2025-03-16*
