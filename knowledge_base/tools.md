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

### 连接前检查

```powershell
# Windows PowerShell：测试端口连通性
Test-NetConnection 服务器IP -Port 22
# TcpTestSucceeded : True 表示端口可访问
```

### 连接服务器

```bash
# 基本连接
ssh username@server_ip

# 指定端口
ssh -p 22 username@server_ip

# 首次连接会询问是否信任服务器指纹，输入 yes
```

### 登录后常用检查命令

```bash
whoami          # 当前用户名
hostname        # 服务器主机名
pwd             # 当前目录
ls -lah         # 列出文件（含隐藏文件）
```

### VS Code Remote SSH

**配置文件示例**（`~/.ssh/config`）：

```sshconfig
Host lab-server
    HostName 222.198.156.91
    User username
    Port 22
```

**使用步骤**：

1. 在 VS Code 中安装 `Remote - SSH` 扩展
2. 命令面板 → `Remote-SSH: Connect to Host` → 选择 `lab-server`
3. 打开远程目录，即可像本地一样编辑

**排错原则**：命令行 SSH 成功后再排查 VS Code，不要因 VS Code 失败而重装服务器环境

## conda 环境管理

### 基本命令

```bash
# 创建环境
conda create -n env_name python=3.10 -y

# 激活环境
conda activate env_name

# 停用环境
conda deactivate

# 查看所有环境
conda env list

# 安装包
conda install package_name

# 导出环境
conda env export > environment.yml

# 从文件恢复环境
conda env create -f environment.yml

# 删除环境
conda remove -n env_name --all
```

### 换源（国内镜像）

**配置 Conda 使用清华 TUNA**：

```bash
cat > ~/.condarc <<'EOF'
channels:
  - conda-forge
  - nodefaults
show_channel_urls: true
channel_priority: strict
custom_channels:
  conda-forge: https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud
EOF

# 清理索引并检查
conda clean -i
conda config --show-sources
```

**配置 pip 使用清华 TUNA**：

```bash
python -m pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
python -m pip config list
```

**注意事项**：

- Conda 和 pip 需要**分别配置**镜像
- **PyTorch GPU 版本**要用官方 CUDA wheel 地址，不能用普通 PyPI 镜像
- 不混用多个国内镜像，避免包版本冲突

### PowerShell 中 conda activate 不生效的解决

**现象**：`conda activate env_name` 后，Python 版本没变

**原因**：PowerShell 未正确初始化 conda

**解决方法**：

```powershell
# 方法一：初始化 PowerShell
conda init powershell
# 关闭并重新打开 PowerShell

# 方法二：直接使用完整路径调用
& "D:\anaconda\envs\env_name\python.exe" [命令]
```

## PyTorch 与 CUDA 环境

### GPU 检查（nvidia-smi）

```bash
nvidia-smi
```

**关键输出**：

- `Driver Version`：NVIDIA 驱动版本
- `CUDA Version`：驱动支持的**上限**（不是当前使用的版本）
- `Memory-Usage`：显存使用
- `GPU-Util`：GPU 利用率
- `Processes`：正在使用 GPU 的进程

### CUDA 版本兼容性

```
NVIDIA 驱动 → 支持 CUDA 上限 → 决定可安装的 PyTorch CUDA 版本
```

**规则**：PyTorch 的 CUDA 版本 ≤ 驱动支持的 CUDA 版本

**例子**：

- 驱动支持 CUDA 12.6 → 可装 cu126, cu121, cu118
- 驱动支持 CUDA 12.3 → 可装 cu121, cu118（不能装 cu126）

### 安装 PyTorch

```bash
# CPU 版（约 123MB）
python -m pip install torch==2.12.1 --index-url https://download.pytorch.org/whl/cpu

# GPU 版 - CUDA 12.6（约 2.4GB）
python -m pip install torch==2.12.1 --index-url https://download.pytorch.org/whl/cu126

# GPU 版 - CUDA 12.1
python -m pip install torch==2.5.1 --index-url https://download.pytorch.org/whl/cu121
```

### 验证 PyTorch 安装

```python
import torch
print("PyTorch:", torch.__version__)              # 版本
print("CUDA runtime:", torch.version.cuda)        # PyTorch 内嵌的 CUDA 版本
print("CUDA available:", torch.cuda.is_available())  # GPU 是否可用
print("GPU:", torch.cuda.get_device_name(0))      # GPU 型号
print("GPU count:", torch.cuda.device_count())    # GPU 数量
```

### 常见问题

**问题：`ModuleNotFoundError: No module named 'torch'`**

- 原因：环境未激活，或安装失败
- 解决：检查当前 Python 路径 `which python` / `Get-Command python`

**问题：`torch.cuda.is_available()` 返回 False**

- 检查是否装的是 CPU 版本（版本号后缀是 `+cpu`）
- 检查驱动是否正常（`nvidia-smi`）
- 重新安装 GPU 版本

**问题：`Could not find a version that satisfies the requirement torch==X.Y.Z`**

- 该 PyTorch 版本在指定 CUDA 源不存在
- 查看可用版本，选择兼容版本

### GPU 共享使用规范

```bash
# 1. 先检查空闲情况
nvidia-smi

# 2. 指定 GPU 编号运行（不要默认用 GPU 0）
CUDA_VISIBLE_DEVICES=0 python train.py

# 3. 持续监控 GPU
watch -n 1 nvidia-smi
```

**注意**：

- 不结束、暂停、抢占其他用户的进程
- 显存不足时等待，不强行运行
- 使用结束确认训练结束，不留后台进程

## NAS 使用

- **访问地址**：`http://10.242.0.50`
- **登录**：使用个人 NAS 账号
- **共享目录**：`SharedFolder` 存放课程材料

**使用边界**：

- ✅️ 用于数据、模型和长期共享材料
- ❌ 不替代 Git 代码版本管理
- ❌ 不上传 Token、密码、私钥等敏感信息
- ❌ 不随意删除、移动、覆盖公共文件

## 敏感信息管理

**绝对不能放入以下位置**：

| 位置         | 密码/Token/私钥 |
| ------------ | --------------- |
| Git 仓库     | ❌               |
| NAS 公共目录 | ❌               |
| 代码文件     | ❌               |
| Notebook     | ❌               |
| 截图         | ❌               |
| 群聊         | ❌               |

**安全操作 Token 的方式**：

```bash
# 隐藏输入 Token（终端不显示）
read -s LAB_AGENT_TOKEN
export LAB_AGENT_TOKEN

# 只检查是否加载，不打印内容
python -c "import os; print('Token loaded:', bool(os.getenv('LAB_AGENT_TOKEN')))"

# 使用完毕清除
unset LAB_AGENT_TOKEN
```

## Zotero 文献管理

（第 5 次课补充）


## 常见问题记录

（遇到问题后补充）
