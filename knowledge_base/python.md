# Python 笔记

> 注：本人已有 Python 和深度学习基础，本笔记主要记录推荐系统相关的特定用法和代码片段。

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

（待补充实际案例）

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

