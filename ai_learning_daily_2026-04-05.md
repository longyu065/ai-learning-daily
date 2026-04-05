# AI 知识学习日报

> 每天学习时间：30分钟 | 学习主题：AI工程化 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-04-05 周日 - 第6天

---

### 🎯 今日主题：AI工程化

---

## 📖 学习内容（30分钟）

---

### 1. 什么是 AI 工程化？

**AI 工程化 = MLOps + LLMOps**

**定义**：将 AI 模型从实验室环境部署到生产环境的一整套工程实践

**核心目标**：
- 📊 **可重复**：相同输入得到相同输出
- 🚀 **可扩展**：支持大规模并发
- 🔧 **可维护**：易于更新和修复
- 📈 **可观测**：监控模型性能

---

### 2. AI 开发流程

#### 传统 ML 流程（MLOps）

```
数据收集 → 特征工程 → 模型训练 → 评估 → 部署 → 监控
```

#### LLM 流程（LLMOps）

```
提示词工程 → 模型选择 → 微调/RLHF → RAG构建 → 评估 → 部署 → 监控
```

---

### 3. 模型部署方式

#### 部署方式对比

| 方式 | 适用场景 | 优点 | 缺点 |
|------|---------|------|------|
| **API 部署** | 在线服务 | 灵活、易扩展 | 网络延迟 |
| **批量推理** | 离线处理 | 高吞吐 | 延迟高 |
| **边缘部署** | 本地推理 | 低延迟、隐私 | 资源受限 |
| **服务化部署** | 企业内部 | 安全、可控 | 成本高 |

---

#### API 部署示例（FastAPI + PyTorch）

```python
from fastapi import FastAPI
from pydantic import BaseModel
import torch
from transformers import AutoTokenizer, AutoModel

app = FastAPI(title="NLP Inference API")

# 加载模型
tokenizer = AutoTokenizer.from_pretrained("bert-base-uncased")
model = AutoModel.from_pretrained("bert-base-uncased")

class TextRequest(BaseModel):
    text: str

@app.post("/predict")
async def predict(request: TextRequest):
    """文本分类推理接口"""
    # Tokenize
    inputs = tokenizer(request.text, return_tensors="pt", padding=True, truncation=True)

    # 推理
    with torch.no_grad():
        outputs = model(**inputs)

    # 返回结果
    return {
        "embeddings": outputs.last_hidden_state[:, 0, :].tolist()
    }

@app.get("/health")
async def health():
    """健康检查接口"""
    return {"status": "healthy"}
```

---

### 4. 模型版本管理

#### 版本控制最佳实践

```python
# 模型版本命名规范
# {model_name}_{algorithm}_{version}

示例：
- sentiment_bert_v1
- sentiment_roberta_v2
- sentiment_distilbert_v3
```

---

#### MLflow 模型管理

```python
import mlflow
import mlflow.pytorch

# 训练模型
with mlflow.start_run():
    # 记录参数
    mlflow.log_param("learning_rate", 0.001)
    mlflow.log_param("epochs", 10)

    # 训练...
    model = train_model()

    # 记录指标
    mlflow.log_metric("accuracy", 0.95)
    mlflow.log_metric("loss", 0.1)

    # 保存模型
    mlflow.pytorch.log_model(model, "model")

# 加载模型
import mlflow.pytorch

logged_model = "runs:/<run_id>/model"
loaded_model = mlflow.pytorch.load_model(logged_model)
```

---

### 5. 模型监控与观察

#### 监控指标

| 类型 | 指标 | 说明 |
|------|------|------|
| **性能** | 延迟、吞吐量、错误率 | 服务质量 |
| **业务** | 准确率、召回率、F1 | 模型效果 |
| **数据** | 数据漂移、分布变化 | 数据质量 |
| **资源** | CPU、内存、GPU | 资源使用 |

---

#### Prometheus + Grafana 监控

```python
from prometheus_client import Counter, Histogram, start_http_server
import time

# 定义指标
request_counter = Counter('model_requests_total', 'Total model requests')
request_latency = Histogram('model_request_latency_seconds', 'Model request latency')
error_counter = Counter('model_errors_total', 'Total model errors')

def predict_with_monitoring(text):
    """带监控的推理函数"""
    start_time = time.time()

    try:
        # 记录请求
        request_counter.inc()

        # 推理
        result = model.predict(text)

        # 记录延迟
        request_latency.observe(time.time() - start_time)

        return result

    except Exception as e:
        # 记录错误
        error_counter.inc()
        raise e

# 启动监控服务
start_http_server(8000)
```

---

### 6. 模型评估

#### 评估指标

| 任务 | 指标 |
|------|------|
| **分类** | 准确率、精确率、召回率、F1、AUC |
| **回归** | MSE、MAE、R² |
| **排名** | NDCG、MRR、Hit@K |
| **生成** | BLEU、ROUGE、Perplexity |

---

#### 评估代码示例

```python
from sklearn.metrics import accuracy_score, precision_score, recall_score, f1_score
from sklearn.metrics import classification_report

def evaluate_model(y_true, y_pred):
    """评估分类模型"""
    accuracy = accuracy_score(y_true, y_pred)
    precision = precision_score(y_true, y_pred, average='weighted')
    recall = recall_score(y_true, y_pred, average='weighted')
    f1 = f1_score(y_true, y_pred, average='weighted')

    print(f"准确率: {accuracy:.4f}")
    print(f"精确率: {precision:.4f}")
    print(f"召回率: {recall:.4f}")
    print(f"F1分数: {f1:.4f}")

    # 详细报告
    print("\n分类报告:")
    print(classification_report(y_true, y_pred))

# 使用
y_true = [0, 1, 2, 0, 1, 2]
y_pred = [0, 1, 1, 0, 1, 2]
evaluate_model(y_true, y_pred)
```

---

### 7. A/B 测试

#### A/B 测试流程

```
1. 分流：随机分配流量（50% v1, 50% v2）
2. 实验：同时运行两个版本
3. 收集：记录指标
4. 统计：显著性检验
5. 决策：选择更好的版本
```

---

#### A/B 测试代码

```python
import random
import numpy as np
from scipy import stats

def ab_test分流():
    """A/B 测试分流"""
    user_id = get_user_id()
    hash_value = hash(user_id)
    group = "A" if hash_value % 2 == 0 else "B"
    return group

def evaluate_ab_test(group_a_metrics, group_b_metrics):
    """评估 A/B 测试结果"""
    # t检验
    t_stat, p_value = stats.ttest_ind(
        group_a_metrics,
        group_b_metrics
    )

    print(f"t统计量: {t_stat:.4f}")
    print(f"p值: {p_value:.4f}")

    if p_value < 0.05:
        print("✅ 结果显著!")
        winner = "A" if np.mean(group_a_metrics) > np.mean(group_b_metrics) else "B"
        print(f"🏆 获胜版本: {winner}")
    else:
        print("❌ 结果不显著")

# 使用
group_a = [1.2, 1.3, 1.1, 1.4, 1.2]
group_b = [1.1, 1.2, 1.0, 1.3, 1.1]
evaluate_ab_test(group_a, group_b)
```

---

### 8. 容器化部署

#### Dockerfile 示例

```dockerfile
# 基础镜像
FROM python:3.9-slim

# 设置工作目录
WORKDIR /app

# 安装依赖
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# 复制代码
COPY . .

# 暴露端口
EXPOSE 8000

# 启动命令
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

---

#### requirements.txt

```
fastapi==0.104.1
uvicorn==0.24.0
torch==2.1.0
transformers==4.35.0
pydantic==2.5.0
prometheus-client==0.19.0
mlflow==2.8.0
```

---

#### 构建和运行

```bash
# 构建镜像
docker build -t nlp-inference:latest .

# 运行容器
docker run -p 8000:8000 nlp-inference:latest

# 推送到仓库
docker tag nlp-inference:latest registry.example.com/nlp-inference:latest
docker push registry.example.com/nlp-inference:latest
```

---

### 9. CI/CD 流水线

#### GitHub Actions 示例

```yaml
name: ML Pipeline

on:
  push:
    branches: [ main ]

jobs:
  train:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2

    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: 3.9

    - name: Install dependencies
      run: |
        pip install -r requirements.txt

    - name: Run tests
      run: |
        pytest tests/

    - name: Train model
      run: |
        python train.py

    - name: Build Docker image
      run: |
        docker build -t nlp-inference:${{ github.sha }} .

    - name: Push to registry
      run: |
        docker push nlp-inference:${{ github.sha }}

    - name: Deploy
      run: |
        kubectl set image deployment/nlp-inference \
          nlp-inference=nlp-inference:${{ github.sha }}
```

---

## 💻 实战练习（15分钟）

---

### 练习1：构建 FastAPI 推理服务

```python
from fastapi import FastAPI
from pydantic import BaseModel
from transformers import pipeline

app = FastAPI()

# 加载模型
classifier = pipeline("sentiment-analysis")

class TextRequest(BaseModel):
    text: str

@app.post("/predict")
async def predict(request: TextRequest):
    """情感分析接口"""
    result = classifier(request.text)[0]
    return {
        "label": result["label"],
        "score": result["score"]
    }

@app.get("/")
async def root():
    return {"message": "NLP Inference API"}
```

---

### 练习2：使用 MLflow 跟踪实验

```python
import mlflow
import mlflow.sklearn
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score

def train_with_mlflow(X_train, y_train, X_test, y_test, params):
    """使用 MLflow 跟踪训练"""
    with mlflow.start_run():
        # 记录参数
        mlflow.log_params(params)

        # 训练模型
        model = LogisticRegression(**params)
        model.fit(X_train, y_train)

        # 预测
        y_pred = model.predict(X_test)

        # 记录指标
        accuracy = accuracy_score(y_test, y_pred)
        mlflow.log_metric("accuracy", accuracy)

        # 保存模型
        mlflow.sklearn.log_model(model, "model")

        return model

# 使用
params = {"C": 1.0, "max_iter": 100}
model = train_with_mlflow(X_train, y_train, X_test, y_test, params)
```

---

### 练习3：实现模型监控

```python
from prometheus_client import Counter, Histogram, start_http_server
import time

# 定义指标
request_counter = Counter('api_requests_total', 'Total API requests')
request_latency = Histogram('api_request_latency_seconds', 'API request latency')

def monitored_predict(text):
    """带监控的预测函数"""
    start_time = time.time()
    request_counter.inc()

    try:
        result = model.predict(text)
        request_latency.observe(time.time() - start_time)
        return result
    except Exception as e:
        request_counter.inc()
        raise e

# 启动监控
start_http_server(8000)
```

---

### 练习4：A/B 测试分析

```python
import numpy as np
from scipy import stats

def analyze_ab_test(control_metrics, treatment_metrics):
    """分析 A/B 测试结果"""
    # 基础统计
    control_mean = np.mean(control_metrics)
    treatment_mean = np.mean(treatment_metrics)
    uplift = (treatment_mean - control_mean) / control_mean * 100

    print(f"对照组均值: {control_mean:.4f}")
    print(f"实验组均值: {treatment_mean:.4f}")
    print(f"提升: {uplift:.2f}%")

    # 显著性检验
    t_stat, p_value = stats.ttest_ind(control_metrics, treatment_metrics)
    print(f"\nt统计量: {t_stat:.4f}")
    print(f"p值: {p_value:.4f}")

    if p_value < 0.05:
        print("✅ 结果显著!")
        if treatment_mean > control_mean:
            print("🏆 实验组获胜")
        else:
            print("🏆 对照组获胜")
    else:
        print("❌ 结果不显著")

# 使用
control = [0.85, 0.87, 0.84, 0.86, 0.88]
treatment = [0.88, 0.90, 0.87, 0.89, 0.91]
analyze_ab_test(control, treatment)
```

---

## 🎯 面试精选

---

### Q1: MLOps vs DevOps

**问题**：MLOps 和 DevOps 的区别？

**答案**：

| 维度 | DevOps | MLOps |
|------|--------|-------|
| **代码版本** | 源代码 | 代码 + 数据 + 模型 |
| **测试** | 单元测试、集成测试 | 模型评估、A/B测试 |
| **部署** | 服务部署 | 模型部署 + 特征工程 |
| **监控** | 服务监控 | 模型漂移监控 |
| **复现性** | 容易 | 困难（数据、随机性） |

---

### Q2: 模型部署方式选择

**问题**：如何选择模型部署方式？

**答案**：

| 部署方式 | 适用场景 |
|---------|---------|
| **API** | 在线实时服务 |
| **批量推理** | 离线批量处理 |
| **边缘部署** | 低延迟、隐私要求高 |
| **Serverless** | 请求量不确定、成本低 |

---

### Q3: 如何监控模型性能？

**问题**：生产环境中如何监控模型？

**答案**：

**监控指标**：
1. **服务指标**：延迟、吞吐量、错误率
2. **业务指标**：准确率、召回率、转化率
3. **数据指标**：数据漂移、分布变化
4. **资源指标**：CPU、内存、GPU使用率

**工具**：
- Prometheus + Grafana
- MLflow
- Evidently AI
- Arize

---

### Q4: A/B 测试如何实施？

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

---

### Q5: 如何处理模型漂移？

**问题**：生产环境中如何检测和处理模型漂移？

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

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| AI工程化定义 | 🎯 已理解 |
| 模型部署方式 | 🎯 已掌握 |
| 模型版本管理 | 🎯 已理解 |
| 模型监控与观察 | 🎯 已掌握 |
| 模型评估 | 🎯 已掌握 |
| A/B测试 | 🎯 已掌握 |
| 容器化部署 | 🎯 已理解 |
| CI/CD流水线 | 🎯 已了解 |

---

## 📝 今天的作业

1. ✅ 构建 FastAPI 推理服务
2. ✅ 使用 MLflow 跟踪实验
3. ✅ 实现模型监控
4. ✅ 分析 A/B 测试结果

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
| 第7天 | 2026-04-06 | 综合面试题 | 📅 |

---

**学习进度**：📊 第6天

---

*最后更新：2026-04-05*
