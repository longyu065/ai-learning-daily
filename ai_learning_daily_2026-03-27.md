# AI 知识学习日报

> 每天学习时间：30分钟 | 学习主题：计算机视觉 | 推送时间：每天 08:30 (UTC)

---

## 📅 2026-03-27 周四 - 第4天

---

### 🎯 今日主题：计算机视觉

---

## 📖 第一部分：计算机视觉基础（15分钟）

---

### 1. 计算机视觉任务全景图

```
计算机视觉
├── 图像分类
│   ├── 单标签分类
│   └── 多标签分类
├── 目标检测
│   ├── 边界框检测
│   └── 实例分割
├── 语义分割
│   ├── 像素级分类
│   └── 全景分割
├── 图像生成
│   ├── 图像风格迁移
│   ├── GAN
│   └── 扩散模型
└── 视频分析
    ├── 动作识别
    └── 目标跟踪
```

---

### 2. 图像基础知识

#### 像素与图像

```python
import numpy as np
import matplotlib.pyplot as plt

# 创建简单图像
# 3x3 像素，RGB 通道
image = np.array([
    [[255, 0, 0], [0, 255, 0], [0, 0, 255]],  # 红 绿 蓝
    [[255, 255, 0], [0, 255, 255], [255, 0, 255]],  # 黄 青 紫
    [[0, 0, 0], [128, 128, 128], [255, 255, 255]]   # 黑 灰 白
], dtype=np.uint8)

print(f"图像形状: {image.shape}")  # (3, 3, 3)
print(f"数据类型: {image.dtype}")

plt.imshow(image)
plt.title("3x3 RGB 图像")
plt.show()
```

---

#### 图像读取与处理

```python
import cv2
import numpy as np

# 读取图像（OpenCV 是 BGR 格式）
image = cv2.imread('image.jpg')
print(f"图像尺寸: {image.shape}")

# 转换为 RGB
image_rgb = cv2.cvtColor(image, cv2.COLOR_BGR2RGB)

# 调整大小
resized = cv2.resize(image_rgb, (224, 224))

# 灰度化
gray = cv2.cvtColor(image, cv2.COLOR_BGR2GRAY)

# 保存图像
cv2.imwrite('resized.jpg', resized)
```

---

### 3. 图像增强

#### 常用操作

```python
import cv2
import numpy as np

# 水平翻转
flipped = cv2.flip(image, 1)

# 旋转
rows, cols = image.shape[:2]
M = cv2.getRotationMatrix2D((cols/2, rows/2), 45, 1)
rotated = cv2.warpAffine(image, M, (cols, rows))

# 亮度调整
brightness = cv2.convertScaleAbs(image, alpha=1.2, beta=20)

# 对比度调整
contrast = cv2.convertScaleAbs(image, alpha=1.5, beta=0)

# 模糊
blurred = cv2.GaussianBlur(image, (5, 5), 0)

# 边缘检测
edges = cv2.Canny(gray, 100, 200)
```

---

### 4. 经典 CNN 模型

#### VGG16

**特点**：简单、易理解

```python
import torch
import torch.nn as nn
import torchvision.models as models

# 加载预训练模型
vgg16 = models.vgg16(pretrained=True)
print(vgg16)
```

**架构**：
```
输入 → 卷积块(64) → 卷积块(128) → 卷积块(256) × 2
     → 卷积块(512) × 2 → 全连接层 → 输出
```

---

#### ResNet

**特点**：残差连接，解决梯度消失

```python
# 加载 ResNet
resnet = models.resnet50(pretrained=True)
print(resnet)
```

**残差块**：
```
x → Conv1 → ReLU → Conv2 → + → ReLU
 ↑                              ↑
 └──────────────────────────────┘
```

---

#### MobileNet

**特点**：轻量级，移动设备

```python
# 加载 MobileNet
mobilenet = models.mobilenet_v2(pretrained=True)
print(mobilenet)
```

---

### 5. 目标检测

#### 两阶段检测器：Faster R-CNN

```python
# 加载 Faster R-CNN
from torchvision.models.detection import fasterrcnn_resnet50_fpn

model = fasterrcnn_resnet50_fpn(pretrained=True)
model.eval()
```

**流程**：
```
图像 → RPN → 提取候选框 → 分类 + 回归 → 输出
```

---

#### 单阶段检测器：YOLO

```python
# 加载 YOLO
import torch

model = torch.hub.load('ultralytics/yolov5', 'yolov5s')
```

**流程**：
```
图像 → 一次前向传播 → 边界框 + 类别 + 置信度
```

---

### 6. 图像分割

#### 语义分割

**任务**：为每个像素分类

```python
# 加载 DeepLabV3
from torchvision.models.segmentation import deeplabv3_resnet50

model = deeplabv3_resnet50(pretrained=True)
model.eval()
```

---

#### 实例分割

**任务**：区分不同实例

```python
# 加载 Mask R-CNN
from torchvision.models.detection import maskrcnn_resnet50_fpn

model = maskrcnn_resnet50_fpn(pretrained=True)
model.eval()
```

---

### 7. 图像生成

#### GAN（生成对抗网络）

**架构**：
```
生成器（G）: 随机噪声 → 生成图像
判别器（D）: 真实/生成图像 → 判断真假

训练目标：
- G: 欺骗 D
- D: 区分真假
```

---

#### Stable Diffusion

**原理**：扩散模型 + CLIP 文本编码

**使用示例**：

```python
from diffusers import StableDiffusionPipeline
import torch

# 加载模型
pipe = StableDiffusionPipeline.from_pretrained(
    "runwayml/stable-diffusion-v1-5",
    torch_dtype=torch.float16
)
pipe = pipe.to("cuda" if torch.cuda.is_available() else "cpu")

# 生成图像
prompt = "A beautiful sunset over mountains"
image = pipe(prompt).images[0]

# 保存
image.save("generated_image.png")
```

---

### 8. Vision Transformer（ViT）

**原理**：将图像视为序列，应用 Transformer

**流程**：
```
图像 → 切片 → 线性投影 → 位置编码 → Transformer → 分类
```

```python
from transformers import ViTForImageClassification, ViTImageProcessor

# 加载模型
processor = ViTImageProcessor.from_pretrained('google/vit-base-patch16-224')
model = ViTForImageClassification.from_pretrained('google/vit-base-patch16-224')

# 预测
from PIL import Image
image = Image.open('image.jpg')

inputs = processor(images=image, return_tensors="pt")
outputs = model(**inputs)
predicted_class_idx = outputs.logits.argmax(-1).item()

print(f"预测类别: {predicted_class_idx}")
```

---

## 💻 第二部分：实战练习（15分钟）

---

### 练习1：图像分类

```python
import torch
from torchvision import transforms
from torchvision.models import resnet50, ResNet50_Weights
from PIL import Image

# 加载预训练模型
weights = ResNet50_Weights.DEFAULT
model = resnet50(weights=weights)
model.eval()

# 预处理
preprocess = weights.transforms()

# 读取图像
image = Image.open('image.jpg')

# 预处理
input_tensor = preprocess(image)
input_batch = input_tensor.unsqueeze(0)  # 添加 batch 维度

# 预测
with torch.no_grad():
    output = model(input_batch)

# 获取类别
weights.meta['categories'][output[0].softmax(0).argmax()]
top5 = output[0].softmax(0).topk(5)

print("Top 5 预测:")
for idx, (score, class_id) in enumerate(zip(top5.values, top5.indices)):
    category = weights.meta['categories'][class_id]
    print(f"{idx+1}. {category}: {score:.2%}")
```

---

### 练习2：目标检测

```python
import torch
import torchvision
from torchvision.models.detection import fasterrcnn_resnet50_fpn
from PIL import Image
import cv2

# 加载模型
model = fasterrcnn_resnet50_fpn(pretrained=True)
model.eval()

# 读取图像
image = Image.open('image.jpg')
image_tensor = torchvision.transforms.functional.to_tensor(image)

# 预测
with torch.no_grad():
    predictions = model(image_tensor.unsqueeze(0))[0]

# 可视化
image_cv2 = cv2.cvtColor(np.array(image), cv2.COLOR_RGB2BGR)

for box, score, label in zip(predictions['boxes'],
                            predictions['scores'],
                            predictions['labels']):
    if score > 0.5:  # 置信度阈值
        x1, y1, x2, y2 = box.int().tolist()
        cv2.rectangle(image_cv2, (x1, y1), (x2, y2), (0, 255, 0), 2)
        cv2.putText(image_cv2, f"{score:.2f}", (x1, y1-10),
                   cv2.FONT_HERSHEY_SIMPLEX, 0.5, (0, 255, 0), 2)

cv2.imshow('Detection', image_cv2)
cv2.waitKey(0)
cv2.destroyAllWindows()
```

---

### 练习3：图像风格迁移

```python
import torch
from torchvision import transforms
from PIL import Image
from torchvision.models import vgg19, VGG19_Weights

# 加载模型
weights = VGG19_Weights.DEFAULT
vgg = vgg19(weights=weights).features

# 加载图像
content_img = Image.open('content.jpg')
style_img = Image.open('style.jpg')

# 预处理
transform = transforms.Compose([
    transforms.Resize((512, 512)),
    transforms.ToTensor()
])

content = transform(content_img).unsqueeze(0)
style = transform(style_img).unsqueeze(0)

# 提取特征
def get_features(image, model):
    features = {}
    x = image
    for name, layer in model._modules.items():
        x = layer(x)
        features[name] = x
    return features

content_features = get_features(content, vgg)
style_features = get_features(style, vgg)

# 这里简化了风格迁移的完整流程
# 实际应用中需要使用完整的 Neural Style Transfer 算法
```

---

## ✅ 今天你学到了什么

| 知识点 | 掌握程度 |
|--------|---------|
| 计算机视觉任务 | 🎯 已理解 |
| 图像处理基础 | 🎯 已掌握 |
| 图像增强 | 🎯 已掌握 |
| 经典 CNN 模型（VGG, ResNet, MobileNet） | 🎯 已理解 |
| 目标检测（Faster R-CNN, YOLO） | 🎯 已了解 |
| 图像分割（语义、实例） | 🎯 已了解 |
| 图像生成（GAN, Diffusion） | 🎯 已了解 |
| Vision Transformer | 🎯 已了解 |

---

## 📝 今天的作业

1. ✅ 尝试不同的图像增强方法
2. ✅ 使用预训练模型进行图像分类
3. ✅ 理解目标检测的输出格式

---

## 🚀 本周学习进度

| 天数 | 日期 | 主题 | 状态 |
|------|------|------|------|
| 第1天 | 2026-03-24 | 机器学习基础 | ✅ |
| 第2天 | 2026-03-25 | 深度学习 | ✅ |
| 第3天 | 2026-03-26 | NLP | ✅ |
| 第4天 | 2026-03-27 | 计算机视觉 | ✅ |
| 第5天 | 2026-03-28 | 大语言模型 | 📅 |
| 第6天 | 2026-03-29 | AI工程化 | ⏳ |
| 第7天 | 2026-03-30 | 综合面试题 | ⏳ |

---

**学习进度**：📊 第4天/第2周

---

*最后更新：2026-03-27*
