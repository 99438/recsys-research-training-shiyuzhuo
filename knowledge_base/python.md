# Python 笔记

> 本笔记主要记录推荐系统相关的特定用法和代码片段。

## 推荐系统相关 Python 使用

### 数据处理（pandas）

推荐系统常见数据处理操作：

```python
import pandas as pd

# 读取交互数据
interactions = pd.read_csv('user_item_interactions.csv')

# 查看基本信息
print(interactions.head())
print(interactions.info())

# 数据统计
print(f"用户数: {interactions['user_id'].nunique()}")
print(f"物品数: {interactions['item_id'].nunique()}")
print(f"交互数: {len(interactions)}")

# 数据筛选和分组
user_stats = interactions.groupby('user_id').agg({
    'item_id': 'count',
    'rating': 'mean'
}).rename(columns={'item_id': 'interaction_count', 'rating': 'avg_rating'})
```

### PyTorch 推荐模型基础

#### Tensor基础操作

Tensor是PyTorch中的核心数据结构，用于表示多维数组：

```python
import torch

# 创建Tensor
x = torch.tensor([1.2, -0.7, 0.5])              # 从列表创建
zeros = torch.zeros(2, 3)                       # 全0
ones = torch.ones(2, 3)                         # 全1
random = torch.rand(2, 3)                       # [0,1)均匀分布
randn = torch.randn(2, 3)                       # 标准正态分布

# 查看属性
print(x.shape)          # torch.Size([3])
print(x.ndim)           # 1
print(x.dtype)          # torch.float32
print(x.device)         # cpu
print(x.numel())        # 3 (元素总数)

# 形状变换
x = torch.tensor([[1, 2, 3], [4, 5, 6]])
flattened = x.reshape(-1)           # [2,3] → [6]
transposed = x.transpose(0, 1)      # [2,3] → [3,2]
batched = x.unsqueeze(0)            # [2,3] → [1,2,3]
unbatched = batched.squeeze(0)      # [1,2,3] → [2,3]

# 拼接
a = torch.tensor([[1, 2], [3, 4]])
b = torch.tensor([[5, 6], [7, 8]])
cat_result = torch.cat([a, b], dim=0)     # [2,2] + [2,2] → [4,2]
stack_result = torch.stack([a, b], dim=0)  # [2,2] + [2,2] → [2,2,2]
```

#### 数据加载：Dataset与DataLoader

```python
from torch.utils.data import Dataset, DataLoader, TensorDataset

# 方法1：使用TensorDataset（适合小规模数据）
features = torch.randn(240, 2)
labels = torch.randint(0, 2, (240,)).float()
dataset = TensorDataset(features, labels)

# 方法2：自定义Dataset类
class InteractionDataset(Dataset):
    def __init__(self, user_ids, item_ids, labels):
        self.user_ids = user_ids
        self.item_ids = item_ids
        self.labels = labels
    
    def __len__(self):
        return len(self.labels)
    
    def __getitem__(self, idx):
        return {
            'user_id': self.user_ids[idx],
            'item_id': self.item_ids[idx],
            'label': self.labels[idx]
        }

# 创建DataLoader
train_loader = DataLoader(
    dataset,
    batch_size=16,
    shuffle=True,           # 训练时打乱
    num_workers=0,          # 数据加载线程数
    drop_last=False         # 是否丢弃最后不完整的batch
)

# 使用DataLoader
for batch_features, batch_labels in train_loader:
    print(batch_features.shape)  # [16, 2]
    print(batch_labels.shape)    # [16]
    break
```

#### 推荐模型常用组件

**Embedding层：ID转向量**

```python
import torch.nn as nn

# 用户和物品Embedding
num_users, num_items = 1000, 5000
embedding_dim = 64

user_embedding = nn.Embedding(num_users, embedding_dim)
item_embedding = nn.Embedding(num_items, embedding_dim)

# 使用（索引必须是torch.long类型）
user_ids = torch.tensor([0, 1, 2], dtype=torch.long)
user_vectors = user_embedding(user_ids)  # [3] → [3, 64]

# 用户-物品点积匹配
item_ids = torch.tensor([10, 20, 30], dtype=torch.long)
item_vectors = item_embedding(item_ids)
scores = (user_vectors * item_vectors).sum(dim=1)  # [3]
```

**线性层**

```python
# 特征融合
linear = nn.Linear(in_features=128, out_features=64)
x = torch.randn(16, 128)  # [batch_size, input_dim]
output = linear(x)         # [16, 64]
```

**激活函数**

```python
# ReLU：max(0, x)
relu = nn.ReLU()
x = torch.tensor([-1, 0, 1, 2])
print(relu(x))  # [0, 0, 1, 2]

# Sigmoid：用于二分类
sigmoid = nn.Sigmoid()
logits = torch.tensor([0.5, -0.5, 2.0])
probs = sigmoid(logits)  # [0.62, 0.38, 0.88]
```

#### 训练循环模板

```python
import torch
import torch.nn as nn
from torch.optim import Adam

# 1. 定义模型
model = nn.Sequential(
    nn.Linear(10, 64),
    nn.ReLU(),
    nn.Linear(64, 1)
)

# 2. 定义损失函数和优化器
criterion = nn.BCEWithLogitsLoss()  # 二分类
optimizer = Adam(model.parameters(), lr=0.001)

# 3. 训练循环
for epoch in range(num_epochs):
    model.train()
    for batch in train_loader:
        features, labels = batch
        
        # 前向传播
        logits = model(features).squeeze(1)
        loss = criterion(logits, labels)
        
        # 反向传播
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()
    
    # 验证
    model.eval()
    with torch.no_grad():
        for batch in valid_loader:
            features, labels = batch
            logits = model(features).squeeze(1)
            # 计算验证指标
```

#### 设备管理（CPU/GPU）

```python
# 检查GPU是否可用
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
print(f"Using device: {device}")

# 将模型和数据移动到GPU
model = model.to(device)
x = x.to(device)

# 训练时的设备管理
for batch in train_loader:
    features, labels = batch
    features = features.to(device)
    labels = labels.to(device)
    # 训练代码
```

### 项目代码阅读技巧

推荐系统代码常见结构：

```
project/
├── data/              # 数据处理
│   ├── dataset.py     # 数据集定义
│   └── dataloader.py  # 数据加载
├── models/            # 模型定义
│   ├── base.py        # 基类
│   └── xxx_model.py   # 具体模型
├── trainers/          # 训练器
├── utils/             # 工具函数
├── configs/           # 配置文件
└── main.py            # 入口脚本
```

阅读顺序：

1. README → 了解项目概况
2. configs/ → 理解实验配置
3. main.py → 找到运行入口
4. data/ → 理解数据格式
5. models/ → 理解模型结构


## 学习资源

- PyTorch 官方文档
- RecBole 推荐系统库文档

