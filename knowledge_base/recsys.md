# 推荐系统笔记

## 基础概念

### 核心要素
- **用户（User）**：推荐系统的服务对象
- **物品（Item）**：被推荐的对象（电影、商品、文章等）
- **交互数据（Interaction）**：用户与物品的行为记录（点击、购买、评分等）

### 推荐任务类型
- **点击率预测（CTR）**：预测用户点击某物品的概率
- **Top-K 推荐**：为用户推荐 K 个最可能感兴趣的物品
- **序列推荐**：基于用户历史行为序列预测下一个物品
- **召回（Recall）**：从海量物品中快速筛选候选集
- **排序（Ranking）**：对候选物品进行精细排序

### 重要术语

**数据相关**
- **显式反馈（Explicit Feedback）**：用户明确表达的偏好（评分、点赞）
- **隐式反馈（Implicit Feedback）**：从行为推断的偏好（点击、浏览）
- **负采样（Negative Sampling）**：构造负样本用于模型训练

**模型相关**
- **Embedding**：将用户/物品映射到低维向量空间
- **协同过滤（Collaborative Filtering）**：基于用户-物品交互矩阵的推荐
- **内容推荐（Content-based）**：基于物品特征的推荐

**评价指标**
- **AUC**：分类模型的评价指标
- **Recall@K**：推荐列表中相关物品的召回率
- **NDCG**：考虑排序位置的评价指标
- **HR（Hit Rate）**：命中率

## 推荐系统流程

1. **数据收集**：用户行为、物品特征、上下文信息
2. **特征工程**：构造用户特征、物品特征、交互特征
3. **模型训练**：选择合适的推荐算法并训练
4. **离线评估**：在测试集上评估模型效果
5. **在线部署**：A/B 测试验证实际效果

## 经典模型

（待补充学习）
- 协同过滤（UserCF、ItemCF）
- 矩阵分解（MF）
- 深度学习推荐模型（DeepFM、Wide&Deep）
- 图神经网络推荐（LightGCN）
- 序列推荐（GRU4Rec、BERT4Rec）
- 大模型增强推荐（P5、CLLM4Rec）

## 实验范式

### 深度学习推荐模型训练流程

#### 1. 数据准备

```python
# 读取交互数据
interactions = pd.read_csv('interactions.csv')

# ID映射：原始ID → 连续索引
user_to_index = {u: i for i, u in enumerate(interactions.user_id.unique())}
item_to_index = {v: i for i, v in enumerate(interactions.item_id.unique())}

# 数据划分：train / validation / test
# 训练集：学习参数
# 验证集：调参和选择最佳模型
# 测试集：最终评估（只看一次）
```

#### 2. 创建DataLoader

```python
from torch.utils.data import TensorDataset, DataLoader

train_set = TensorDataset(train_features, train_labels)
train_loader = DataLoader(
    train_set,
    batch_size=256,
    shuffle=True,  # 训练时打乱
    num_workers=0
)
```

#### 3. 定义模型

```python
import torch.nn as nn

class RecommenderModel(nn.Module):
    def __init__(self, num_users, num_items, embedding_dim):
        super().__init__()
        self.user_embedding = nn.Embedding(num_users, embedding_dim)
        self.item_embedding = nn.Embedding(num_items, embedding_dim)
    
    def forward(self, user_ids, item_ids):
        user_vectors = self.user_embedding(user_ids)
        item_vectors = self.item_embedding(item_ids)
        scores = (user_vectors * item_vectors).sum(dim=1)
        return scores
```

#### 4. 训练循环

```python
model = RecommenderModel(num_users, num_items, embedding_dim=64)
criterion = nn.BCEWithLogitsLoss()
optimizer = torch.optim.Adam(model.parameters(), lr=0.001)

for epoch in range(num_epochs):
    # 训练阶段
    model.train()
    for batch in train_loader:
        user_ids, item_ids, labels = batch
        
        # 前向传播
        logits = model(user_ids, item_ids)
        loss = criterion(logits, labels)
        
        # 反向传播与参数更新
        optimizer.zero_grad()  # 1. 清除旧梯度
        loss.backward()        # 2. 计算新梯度
        optimizer.step()       # 3. 更新参数
    
    # 验证阶段
    model.eval()
    with torch.no_grad():
        valid_loss = evaluate(model, valid_loader)
        
    # 保存最佳模型
    if valid_loss < best_valid_loss:
        best_state = model.state_dict()
```

#### 5. 关键概念

- **Batch**：一次训练使用的样本子集（如256条）
- **Epoch**：完整遍历训练集一次
- **Logit**：模型输出的原始分数（未经sigmoid）
- **Loss**：衡量预测与真实标签的差距（必须是标量）
- **梯度**：Loss对每个参数的偏导数，指示参数调整方向

#### 6. 数据流中的Shape变化

```
全部数据:  [N, F]         N个样本，F个特征
一个batch: [B, F]         B个样本的batch
模型输出:  [B, 1]         每个样本一个logit
squeeze:   [B]            去掉维度1
标签:      [B]            与logits对齐
Loss:      []             标量
```

#### 7. 训练vs评估

| 阶段      | 模式                               | 梯度计算 | 参数更新 | Dropout | BatchNorm         |
| --------- | ---------------------------------- | -------- | -------- | ------- | ----------------- |
| 训练      | `model.train()`                    | ✓        | ✓        | 启用    | 使用当前batch统计 |
| 验证/测试 | `model.eval()` + `torch.no_grad()` | ✗        | ✗        | 关闭    | 使用全局统计      |

#### 8. 如何判断训练正常

- Loss持续下降
- 验证集表现合理
- 无明显过拟合（验证loss不持续上升）
- 测试集结果与验证集接近

## 学习资源

- 推荐系统经典论文
- 相关开源代码库
- 推荐系统综述文章

