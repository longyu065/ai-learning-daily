# AI 知识学习日报

> 每天学习时间：30分钟 | 学习主题：AI工程化 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-29 周六 - 第6天

---

### 🎯 今日主题：AI工程化

---

## 📖 第一部分：AI工程化基础（15分钟）

---

### 1. 什么是 AI 工程化？

**AI 工程化 = AI + DevOps + MLOps**

**目标**：让 AI 模型从实验室走向生产环境

**核心挑战**：
- 🔧 **模型部署**：如何高效服务模型
- 🚀 **性能优化**：降低延迟和成本
- 📊 **监控**：追踪模型性能
- 🔄 **更新**：持续集成和部署
- 📈 **扩展**：处理高并发请求

---

### 2. MLOps 流程

```
数据采集 → 模型训练 → 验证评估 → 部署服务
     ↑                                      ↓
     └────────────── 监控反馈 ←──────────────┘
```

**关键组件**：

| 组件 | 工具 | 说明 |
|------|------|------|
| **数据管理** | DVC, MLflow | 数据版本控制 |
| **实验跟踪** | MLflow, Weights & Biases | 记录实验指标 |
| **模型服务** | TensorFlow Serving, TorchServe | 模型部署 |
| **容器化** | Docker, Kubernetes | 环境一致性 |
| **监控** | Prometheus, Grafana | 性能监控 |

---

### 3. 模型部署

#### REST API 部署

**使用 Flask**：

```python
from flask import Flask, request, jsonify
import torch
from transformers import pipeline

app = Flask(__name__)

# 加载模型
model = pipeline("sentiment-analysis")

@app.route('/predict', methods=['POST'])
def predict():
    """情感分析 API"""
    data = request.json
    text = data.get('text', '')

    result = model(text)[0]

    return jsonify({
        'label': result['label'],
        'score': float(result['score']),
        'text': text
    })

@app.route('/health')
def health():
    """健康检查"""
    return jsonify({'status': 'healthy'})

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

**测试 API**：

```bash
# 启动服务
python app.py

# 测试
curl -X POST http://localhost:5000/predict \
  -H "Content-Type: application/json" \
  -d '{"text": "I love this!"}'
```

---

#### FastAPI 部署（更高效）

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel
from transformers import pipeline

app = FastAPI(title="AI 服务 API")

# 加载模型
model = pipeline("sentiment-analysis")

# 定义请求模型
class PredictionRequest(BaseModel):
    text: str

# 定义响应模型
class PredictionResponse(BaseModel):
    label: str
    score: float
    text: str

@app.post("/predict", response_model=PredictionResponse)
async def predict(request: PredictionRequest):
    """情感分析 API"""
    if not request.text:
        raise HTTPException(status_code=400, detail="Text is required")

    result = model(request.text)[0]

    return PredictionResponse(
        label=result['label'],
        score=float(result['score']),
        text=request.text
    )

@app.get("/")
async def root():
    return {"message": "AI 服务正在运行"}

if __name__ == '__main__':
    import uvicorn
    uvicorn.run(app, host='0.0.0.0', port=5000)
```

---

### 4. 容器化部署

#### Dockerfile

```dockerfile
# 使用 Python 官方镜像
FROM python:3.10-slim

# 设置工作目录
WORKDIR /app

# 复制依赖文件
COPY requirements.txt .

# 安装依赖
RUN pip install --no-cache-dir -r requirements.txt

# 复制代码
COPY app.py .

# 暴露端口
EXPOSE 5000

# 启动服务
CMD ["python", "app.py"]
```

**requirements.txt**：
```
flask==2.3.0
torch==2.0.0
transformers==4.30.0
```

---

#### 构建和运行

```bash
# 构建镜像
docker build -t ai-service:latest .

# 运行容器
docker run -p 5000:5000 ai-service:latest

# 后台运行
docker run -d -p 5000:5000 --name ai-service ai-service:latest

# 查看日志
docker logs ai-service

# 停止容器
docker stop ai-service
```

---

### 5. 模型优化

#### 模型量化

**减少模型大小，加速推理**

```python
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer

# 加载模型
model_name = "gpt2"
model = AutoModelForCausalLM.from_pretrained(model_name)
tokenizer = AutoTokenizer.from_pretrained(model_name)

print(f"原始模型大小: {sum(p.numel() for p in model.parameters()) / 1e6:.2f}M")

# 量化模型（8位量化）
quantized_model = torch.quantization.quantize_dynamic(
    model,
    {torch.nn.Linear},
    dtype=torch.qint8
)

print(f"量化后模型大小: {sum(p.numel() for p in quantized_model.parameters()) / 1e6:.2f}M")

# 对比推理速度
import time

# 原始模型
start = time.time()
output = model.generate(**tokenizer("Hello", return_tensors="pt"))
original_time = time.time() - start

# 量化模型
start = time.time()
output = quantized_model.generate(**tokenizer("Hello", return_tensors="pt"))
quantized_time = time.time() - start

print(f"原始模型推理时间: {original_time:.4f}s")
print(f"量化模型推理时间: {quantized_time:.4f}s")
print(f"加速比: {original_time/quantized_time:.2f}x")
```

---

#### 模型蒸馏

**大模型知识迁移到小模型**

```python
from transformers import DistilBertForSequenceClassification, BertForSequenceClassification

# 教师（大模型）
teacher_model = BertForSequenceClassification.from_pretrained('bert-base-uncased')

# 学生（小模型）
student_model = DistilBertForSequenceClassification.from_pretrained('distilbert-base-uncased')

print(f"教师模型参数量: {sum(p.numel() for p in teacher_model.parameters()) / 1e6:.2f}M")
print(f"学生模型参数量: {sum(p.numel() for p in student_model.parameters()) / 1e6:.2f}M")

# 训练学生模型（蒸馏）
def distillation_loss(teacher_logits, student_logits, temperature=2.0):
    """
    蒸馏损失：让学生的输出接近教师的输出
    """
    soft_teacher = torch.nn.functional.softmax(teacher_logits / temperature, dim=-1)
    soft_student = torch.nn.functional.log_softmax(student_logits / temperature, dim=-1)

    kl_loss = torch.nn.functional.kl_div(soft_student, soft_teacher, reduction='batchmean')
    return kl_loss * (temperature ** 2)
```

---

### 6. 实验跟踪

#### MLflow

**记录实验指标和模型**：

```python
import mlflow
import mlflow.pytorch
import torch
import torch.nn as nn

# 启动 MLflow
mlflow.start_run()

# 定义模型
model = nn.Linear(10, 1)
criterion = nn.MSELoss()
optimizer = torch.optim.Adam(model.parameters())

# 记录参数
mlflow.log_param("learning_rate", 0.001)
mlflow.log_param("epochs", 100)

# 训练
for epoch in range(100):
    # ... 训练代码 ...

    # 记录指标
    mlflow.log_metric("train_loss", loss.item(), step=epoch)

# 记录模型
mlflow.pytorch.log_model(model, "model")

# 结束运行
mlflow.end_run()
```

**查看实验**：

```bash
# 启动 MLflow UI
mlflow ui

# 访问 http://localhost:5000
```

---

### 7. 模型监控

**监控指标**：

| 指标 | 说明 |
|------|------|
| **延迟** | 响应时间 |
| **吞吐量** | 每秒处理请求数 |
| **准确率** | 模型预测准确性 |
| **数据漂移** | 输入数据分布变化 |
| **资源使用** | CPU、内存、GPU 使用率 |

**监控代码示例**：

```python
import time
from collections import deque
from prometheus_client import start_http_server, Counter, Histogram

# Prometheus 指标
REQUEST_COUNT = Counter('api_requests_total', 'Total requests')
REQUEST_LATENCY = Histogram('api_latency_seconds', 'Request latency')
ERROR_COUNT = Counter('api_errors_total', 'Total errors')

class ModelMonitor:
    def __init__(self, window_size=100):
        self.window_size = window_size
        self.latencies = deque(maxlen=window_size)
        self.predictions = deque(maxlen=window_size)
        self.accuracy_history = deque(maxlen=window_size)

    def record_prediction(self, true_label, predicted_label, latency):
        """记录预测"""
        self.latencies.append(latency)
        self.predictions.append((true_label, predicted_label))
        REQUEST_COUNT.inc()

        if true_label != predicted_label:
            ERROR_COUNT.inc()

    def get_metrics(self):
        """获取指标"""
        avg_latency = sum(self.latencies) / len(self.latencies) if self.latencies else 0

        # 计算准确率
        correct = sum(1 for t, p in self.predictions if t == p)
        accuracy = correct / len(self.predictions) if self.predictions else 0

        return {
            'avg_latency': avg_latency,
            'accuracy': accuracy,
            'predictions_count': len(self.predictions)
        }

# 使用
monitor = ModelMonitor()

@REQUEST_LATENCY.time()
def predict(text):
    start = time.time()

    # 模型预测
    prediction = model(text)

    latency = time.time() - start

    # 记录
    monitor.record_prediction(
        true_label=true_label,
        predicted_label=prediction,
        latency=latency
    )

    return prediction

# 启动 Prometheus 监控
start_http_server(8000)
print("Prometheus metrics at http://localhost:8000")
```

---

### 8. 自动化 CI/CD

#### GitHub Actions 示例

```yaml
name: AI Model CI/CD

on:
  push:
    branches: [main]

jobs:
  train:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2

      - name: Set up Python
        uses: actions/setup-python@v2
        with:
          python-version: '3.10'

      - name: Install dependencies
        run: |
          pip install -r requirements.txt

      - name: Train model
        run: |
          python train.py

      - name: Save model
        run: |
          git config user.name "GitHub Actions"
          git config user.email "actions@github.com"
          git add models/
          git commit -m "Update model"
          git push

  deploy:
    needs: train
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to production
        run: |
          # 部署命令
          ./deploy.sh
```

---

## 💻 第二部分：实战练习（15分钟）

---

### 练习1：创建简单的 AI 服务

```python
from fastapi import FastAPI
from pydantic import BaseModel
import torch
from transformers import pipeline

app = FastAPI()

# 加载模型
classifier = pipeline("text-classification", model="distilbert-base-uncased-finetuned-sst-2-english")

class TextRequest(BaseModel):
    text: str

@app.post("/classify")
async def classify(request: TextRequest):
    result = classifier(request.text)[0]
    return {
        "label": result['label'],
        "score": float(result['score'])
    }

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
```

---

### 练习2：模型量化

```python
import torch
from transformers import AutoModelForCausalLM, AutoTokenizer

# 加载 GPT-2
model = AutoModelForCausalLM.from_pretrained("gpt2")
tokenizer = AutoTokenizer.from_pretrained("gpt2")

# 原始模型
print(f"原始模型: {sum(p.numel() for p in model.parameters()):,} 参数")

# 量化
quantized = torch.quantization.quantize_dynamic(
    model, {torch.nn.Linear}, dtype=torch.qint8
)

print(f"量化模型: {sum(p.numel() for p in quantized.parameters()):,} 参数")

# 测试推理
input_text = "The future of AI is"
input_ids = tokenizer.encode(input_text, return_tensors="pt")

# 生成
output_ids = quantized.generate(input_ids, max_length=50)
output_text = tokenizer.decode(output_ids[0], skip_special_tokens=True)
print(f"\n生成文本: {output_text}")
```

---

### 练习3：简单的监控

```python
import time
from collections import deque

class SimpleMonitor:
    def __init__(self, max_size=100):
        self.latencies = deque(maxlen=max_size)
        self.requests = deque(maxlen=max_size)

    def track(self, func):
        def wrapper(*args, **kwargs):
            start = time.time()
            result = func(*args, **kwargs)
            latency = time.time() - start

            self.latencies.append(latency)
            self.requests.append(time.time())

            return result
        return wrapper

    def stats(self):
        if not self.latencies:
            return {}

        return {
            'total_requests': len(self.requests),
            'avg_latency': sum(self.latencies) / len(self.latencies),
            'p50_latency': sorted(self.latencies)[len(self.latencies)//2],
            'p95_latency': sorted(self.latencies)[int(len(self.latencies)*0.95)],
            'p99_latency': sorted(self.latencies)[int(len(self.latencies)*0.99)]
        }

# 使用
monitor = SimpleMonitor()

@monitor.track
def mock_prediction(text):
    time.sleep(0.1)  # 模拟推理时间
    return "positive"

# 测试
for _ in range(10):
    mock_prediction("test text")

print(monitor.stats())
```

---

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| AI 工程化概念 | 🎯 已理解 |
| MLOps 流程 | 🎯 已理解 |
| 模型部署（REST API） | 🎯 已掌握 |
| 容器化（Docker） | 🎯 已掌握 |
| 模型优化（量化、蒸馏） | 🎯 已了解 |
| 实验跟踪（MLflow） | 🎯 已了解 |
| 模型监控 | 🎯 已掌握 |
| CI/CD 自动化 | 🎯 已了解 |

---

## 📝 今天的作业

1. ✅ 创建一个简单的 FastAPI AI 服务
2. ✅ 尝试模型量化
3. ✅ 实现简单的监控系统
4. ✅ 学习 Docker 基础命令

---

## 🚀 本周学习进度

| 天数 | 日期 | 主题 | 状态 |
|------|------|------|------|
| 第1天 | 2026-03-24 | 机器学习基础 | ✅ |
| 第2天 | 2026-03-25 | 深度学习 | ✅ |
| 第3天 | 2026-03-26 | NLP | ✅ |
| 第4天 | 2026-03-27 | 计算机视觉 | ✅ |
| 第5天 | 2026-03-28 | 大语言模型 | ✅ |
| 第6天 | 2026-03-29 | AI工程化 | ✅ |
| 第7天 | 2026-03-30 | 综合面试题 | 📅 |

---

**学习进度**：📊 第6天/第2周

---

*最后更新：2026-03-29*
