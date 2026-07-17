# 30 分钟学会你现在需要的 Git

学完后你应该能完成：

```
创建学习仓库
→ 编写 Markdown
→ 查看修改
→ 提交版本
→ 推送 GitHub
→ 修改错了能够恢复
```

暂时不用深入学习：

- 复杂分支模型
- rebase
- cherry-pick
- submodule
- Git 底层对象
- 多人协作冲突

------

# 第 0～5 分钟：理解 Git 的核心模型

Git 不是单纯的“上传工具”，而是一个**文件版本记录系统**。

你需要理解四个区域：

```
工作区
  ↓ git add
暂存区
  ↓ git commit
本地仓库
  ↓ git push
GitHub 远程仓库
```

## 1. 工作区

就是你正在编辑的普通文件：

```
linux-sre-learning/
├── README.md
├── notes/
└── daily/
```

你修改了文件，但 Git 还没有正式记录。

## 2. 暂存区

通过：

```
git add 文件名
```

告诉 Git：

> 我准备把这个版本的文件提交。

暂存区相当于“本次提交的购物车”。

## 3. 本地仓库

执行：

```
git commit
```

Git 才会生成一个正式版本。

## 4. 远程仓库

执行：

```
git push
```

把本地版本发送到 GitHub。

## 最重要的区别

```
git add .
```

不是保存版本。

```
git commit -m "..."
```

才是在本地创建版本。

```
git push
```

才是上传到 GitHub。

------

# 第 5～10 分钟：创建第一个学习仓库

打开 Linux 终端，执行：

```
mkdir -p ~/linux-sre-learning
cd ~/linux-sre-learning
```

初始化 Git 仓库：

```
git init
```

查看状态：

```
git status
```

你可能会看到：

```
On branch master
No commits yet
```

有些 Git 默认分支叫 `master`，建议统一修改成 `main`：

```
git branch -M main
```

## 配置 Git 身份

只需要配置一次：

```
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的邮箱"
```

查看配置：

```
git config --global --list
```

这不是登录 GitHub，只是说明提交由谁创建。

------

# 第 10～15 分钟：完成第一次提交

创建基础文件：

```
mkdir -p notes daily history/raw history/cleaned labs reviews
touch README.md
touch .gitignore
```

编辑 README：

```
vim README.md
```

写入：

```
# Linux SRE Learning

记录 Linux 集训过程中的：

- 主题笔记
- 每日复盘
- history 分析
- 场景练习
- 故障排查
```

保存退出。

查看状态：

```
git status
```

你会看到未跟踪文件：

```
Untracked files:
  README.md
```

## 把文件加入暂存区

```
git add README.md
```

再次查看：

```
git status
```

现在文件会显示在：

```
Changes to be committed
```

## 创建提交

```
git commit -m "docs: initialize learning repository"
```

查看提交记录：

```
git log
```

简洁查看：

```
git log --oneline
```

你会看到类似：

```
3a1b921 docs: initialize learning repository
```

前面的字符串是提交编号，后面是提交说明。

------

# 第 15～20 分钟：掌握每天使用的固定流程

现在创建一篇笔记：

```
vim notes/file-permissions.md
```

写入：

```
# 文件权限

## 查看权限

```bash
ls -l
stat 文件名
```

## 修改权限

```
chmod 640 file
保存后执行：

```bash
git status
```

## 查看具体修改了什么

```
git diff
```

`git diff` 会显示：

- 删除的内容前面是 `-`
- 新增的内容前面是 `+`

确认没有问题后：

```
git add notes/file-permissions.md
git commit -m "notes: add file permissions notes"
```

再推送到 GitHub：

```
git push
```

因此你每天最常用的流程只有：

```
git status
git diff
git add .
git commit -m "notes: summarize file permissions"
git push
```

建议不要机械地直接 `git add .`。

先执行：

```
git status
git diff
```

确认没有敏感信息和垃圾文件，再添加。

------

# 第 20～25 分钟：学会修改错误

这是 Git 最实用的部分。

------

## 情况一：文件改乱了，还没有 git add

例如你错误修改了：

```
notes/file-permissions.md
```

查看修改：

```
git diff notes/file-permissions.md
```

不想保留修改：

```
git restore notes/file-permissions.md
```

文件会恢复到最近一次提交的状态。

注意：

```
git restore
```

会丢失当前未提交修改，执行前先确认。

------

## 情况二：已经 git add，但还没有 commit

例如：

```
git add notes/file-permissions.md
```

后来发现不应该加入本次提交。

把它移出暂存区：

```
git restore --staged notes/file-permissions.md
```

这不会删除你的修改，只是取消 `git add`。

模型：

```
已经 git add
    ↓
git restore --staged
    ↓
回到工作区修改状态
```

------

## 情况三：刚提交，提交说明写错了

例如：

```
git commit -m "update"
```

修改最近一次提交说明：

```
git commit --amend -m "notes: add file permissions examples"
```

尽量只对**尚未 push 的提交**这样操作。

------

## 情况四：想查看旧版本

查看提交记录：

```
git log --oneline
```

查看某次提交具体修改：

```
git show 提交编号
```

例如：

```
git show 3a1b921
```

查看某个文件以前的内容：

```
git show 3a1b921:notes/file-permissions.md
```

暂时不要随便使用：

```
git reset --hard
```

它可能直接删除尚未提交的工作。

你现在主要使用：

```
git restore 文件
git restore --staged 文件
```

------

# 第 25～30 分钟：连接 GitHub

## 第一步：在 GitHub 创建仓库

在 GitHub 新建仓库：

```
linux-sre-learning
```

建议：

```
Visibility: Private
```

因为 history 可能包含：

- IP 地址
- 用户名
- 主机名
- SSH 地址
- Token
- 数据库连接信息

创建空仓库时，不要勾选自动生成 README，因为本地已经有 README。

------

## 第二步：连接远程仓库

GitHub 会给出一个仓库地址，通常类似：

```
https://github.com/用户名/linux-sre-learning.git
```

执行：

```
git remote add origin https://github.com/用户名/linux-sre-learning.git
```

查看远程仓库：

```
git remote -v
```

第一次推送：

```
git push -u origin main
```

以后只需要：

```
git push
```

`-u` 的作用是把本地 `main` 与远程 `origin/main` 建立默认关联。

------

# `.gitignore` 必须学会

你的原始 history 不应该上传。

编辑：

```
vim .gitignore
```

写入：

```
# 原始 history
history/raw/

# 密钥和敏感文件
.env
*.pem
*.key
id_rsa
id_ed25519

# Vim 临时文件
*.swp
*~

# 编辑器配置
.vscode/
.idea/

# Windows 文件
Thumbs.db
Desktop.ini

# macOS 文件
.DS_Store
```

提交：

```
git add .gitignore
git commit -m "chore: ignore raw history and sensitive files"
git push
```

## `.gitignore` 的作用

它只能忽略**还没有被 Git 跟踪的文件**。

假如某文件已经提交过，再加入 `.gitignore` 不会自动停止跟踪。

需要执行：

```
git rm --cached 文件名
```

对于目录：

```
git rm -r --cached history/raw
```

然后提交：

```
git commit -m "chore: stop tracking raw history"
```

------

# 你每天真正需要记住的 8 个命令

```
git status
git diff
git add 文件
git commit -m "说明"
git log --oneline
git push
git pull
git restore 文件
```

------

# 每天固定操作模板

## 开始学习前

先进入仓库：

```
cd ~/linux-sre-learning
```

获取 GitHub 上的最新版本：

```
git pull
```

个人只有一台机器时，通常不会产生变化，但形成习惯没有坏处。

## 学习过程中

正常编辑 Markdown：

```
vim daily/2026-07-17.md
vim notes/file-permissions.md
```

## 学习结束后

```
git status
git diff
```

确认内容。

添加文件：

```
git add daily/2026-07-17.md
git add notes/file-permissions.md
```

创建提交：

```
git commit -m "notes: review file permissions and user management"
```

推送：

```
git push
```

------

# 提交信息怎么写

格式：

```
类型: 做了什么
```

常用类型：

| 类型     | 用途               |
| -------- | ------------------ |
| `notes`  | 整理知识笔记       |
| `lab`    | 完成实验或场景     |
| `review` | 每日或周期复盘     |
| `fix`    | 修正错误内容       |
| `docs`   | README 或说明文档  |
| `chore`  | 调整目录、忽略文件 |
| `script` | 编写脚本           |

示例：

```
git commit -m "notes: explain chmod numeric permissions"
git commit -m "lab: complete shared directory exercise"
git commit -m "review: summarize three-day learning cycle"
git commit -m "fix: correct directory execute permission notes"
```

不要写：

```
git commit -m "修改"
git commit -m "更新"
git commit -m "111"
```

因为以后无法通过提交记录判断自己学了什么。

------

# Git 和 GitHub 的区别

| Git                | GitHub                 |
| ------------------ | ---------------------- |
| 安装在电脑上的工具 | 一个网站和远程仓库平台 |
| 管理本地版本       | 保存和展示远程版本     |
| 不联网也能使用     | 通常需要联网           |
| 使用 commit        | 使用 push 上传         |
| 可以查看历史版本   | 可以备份、展示和协作   |

一句话：

```
Git 负责版本管理，GitHub 负责远程保存和协作。
```

------

# 关于分支，你现在只需要知道这些

查看分支：

```
git branch
```

你当前使用：

```
main
```

个人学习仓库初期只使用 `main`。

不要为了显得专业，每天创建一个分支。

以后进行较大的仓库结构修改时，可以：

```
git switch -c restructure-notes
```

修改完成后回到主分支：

```
git switch main
```

但你现在前两周完全可以不使用分支。

------

# `git pull` 的本质

```
git pull
```

相当于：

```
从 GitHub 下载新的提交
+
合并到本地分支
```

使用多个设备时，例如：

- 酒店电脑
- 自己的笔记本
- Linux 虚拟机

每次开始编辑前应当：

```
git pull
```

每次结束后：

```
git push
```

标准习惯：

```
先 pull
再修改
再 commit
最后 push
```

------

# 30 分钟实战验收

现在独立完成下面任务：

## 任务一：初始化仓库

```
mkdir ~/linux-sre-learning
cd ~/linux-sre-learning
git init
git branch -M main
```

## 任务二：创建文件

```
mkdir -p notes daily history/raw history/cleaned labs reviews
touch README.md .gitignore
```

## 任务三：完成第一次提交

```
git status
git add README.md .gitignore
git commit -m "docs: initialize Linux learning repository"
```

## 任务四：修改并查看差异

```
vim README.md
git status
git diff
```

## 任务五：完成第二次提交

```
git add README.md
git commit -m "docs: describe learning repository"
git log --oneline
```

## 任务六：测试撤销

先随便修改 README：

```
echo "错误内容" >> README.md
```

查看：

```
git diff
```

恢复：

```
git restore README.md
```

确认错误内容消失：

```
cat README.md
```

能独立完成这六项，你已经掌握当前学习系统需要的 Git 基础。

------

# 最终记忆口诀

```
status 看状态
diff 看改动
add 选内容
commit 存版本
push 上传
pull 下载
restore 撤销
log 看历史
```

每天只重复这一条链路：

```
git pull
git status
git diff
git add .
git commit -m "notes: ..."
git push
```

但执行 `git add .` 前，永远先看：

```
git status
git diff
```

避免把原始 history、密钥或垃圾文件上传到 GitHub。