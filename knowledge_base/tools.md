# 工具使用笔记

## 科研工具栈

根据培训课程，需要掌握的工具：

### 笔记管理
- **Obsidian**：本地知识库管理（主力工具）
- **Markdown**：基础标记语言

### 版本控制
- **Git**：代码版本管理
- **GitHub**：代码托管与协作

### 文献管理
- **Zotero**：文献管理工具（第 5 次课重点）
- **Google Scholar**：文献检索

### 开发工具
- **VS Code**：代码编辑器
- **SSH**：远程服务器连接
- **conda**：Python 环境管理

### 其他工具
- **PixPin**：截图工具
- **AI/Agent**：辅助工具（需明确使用边界）

## Git 基础

### 完整仓库初始化流程

#### 1. 初始化本地仓库

```bash
# 进入项目目录
cd /path/to/your/project

# 初始化 Git 仓库
git init

# 查看当前状态
git status
```

#### 2. 首次提交

```bash
# 添加所有文件到暂存区
git add .

# 或添加指定文件
git add <file_path>

# 查看暂存区状态
git status

# 提交到本地仓库
git commit -m "Initial commit: 初始化仓库结构"
```

#### 3. 创建 GitHub 远程仓库

在 GitHub 网站操作：
1. 登录 GitHub
2. 点击右上角 "+" → "New repository"
3. 填写仓库名（如 `recsys-research-training-shiyuzhuo`）
4. 选择 Public（公开）或 Private（私有）
5. **不要勾选** "Initialize with README"（本地已有文件）
6. 点击 "Create repository"

#### 4. 关联并推送到远程

```bash
# 添加远程仓库
git remote add origin https://github.com/99438/recsys-research-training-shiyuzhuo.git

# 查看远程仓库配置
git remote -v

# 如果默认分支是 master，改为 main
git branch -M main

# 首次推送（-u 参数用于设置上游分支）
git push -u origin main
```

### 日常 Git 工作流

```bash
# 1. 查看当前状态
git status

# 2. 查看具体修改内容
git diff

# 3. 添加修改到暂存区
git add .                    # 添加所有修改
git add <file>               # 添加指定文件

# 4. 提交到本地仓库
git commit -m "简洁的提交说明"

# 5. 推送到远程仓库
git push
```

### 常用 Git 命令

```bash
# 查看提交历史
git log                      # 详细历史
git log --oneline            # 简洁历史
git log --graph --oneline    # 图形化历史

# 分支操作
git branch                   # 查看本地分支
git branch -a                # 查看所有分支（包括远程）
git branch <branch_name>     # 创建新分支
git checkout <branch_name>   # 切换分支
git checkout -b <branch>     # 创建并切换到新分支

# 远程操作
git remote -v                # 查看远程仓库
git pull                     # 拉取远程更新
git push                     # 推送到远程
git clone <url>              # 克隆远程仓库

# 撤销操作（谨慎使用）
git checkout -- <file>       # 撤销工作区修改
git reset HEAD <file>        # 取消暂存
git reset --soft HEAD^       # 撤销最近一次提交（保留修改）
git reset --hard HEAD^       # 撤销最近一次提交（丢弃修改）

# 查看配置
git config --list            # 查看所有配置
git config user.name         # 查看用户名
git config user.email        # 查看邮箱
```

### 忽略文件（.gitignore）

在项目根目录创建 `.gitignore` 文件：

```gitignore
# Python
__pycache__/
*.py[cod]
*.so
.Python
env/
venv/

# Jupyter Notebook
.ipynb_checkpoints

# 数据文件
*.csv
*.pkl
*.h5
data/

# 模型文件
*.pth
*.pt
models/

# 日志
*.log

# 系统文件
.DS_Store
Thumbs.db

# IDE
.vscode/
.idea/
```

### Git 最佳实践

1. **提交信息规范**
   - 用简洁的语言描述本次修改
   - 格式：`类型: 简短描述`
   - 例如：`feat: 添加论文阅读笔记模板`、`fix: 修复日志日期错误`

2. **提交频率**
   - 完成一个小功能就提交
   - 避免一次提交太多修改
   - 每日学习结束后至少提交一次

3. **分支管理**
   - `main` 分支保持稳定
   - 新功能可以创建新分支开发
   - 完成后合并到 main

4. **敏感信息**
   - 不要提交密码、密钥等敏感信息
   - 使用 `.gitignore` 排除敏感文件

## SSH 使用

（还不熟悉TT，第 3 次课补充）

### 连接服务器

```bash
ssh username@server_ip
```

### 常用命令

（待补充）

## conda 环境管理

（第 3 次课补充）

### 基本命令

```bash
# 创建环境
conda create -n env_name python=3.8

# 激活环境
conda activate env_name

# 安装包
conda install package_name

# 导出环境
conda env export > environment.yml

# 删除环境
conda remove -n env_name --all
```

## Zotero 文献管理

（第 5 次课补充）


## 常见问题记录

（遇到问题后补充）

