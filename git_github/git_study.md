# git核心模型

```
工作区
  |add
暂存区
  |commit 版本提交
本地仓库
  |push 远程仓库
github仓库
```



# git流程

## 创建git仓库，查看状态

```git
git init #git初始化
git status #查看当前目录的状态
git branch -M main #修改主分支的名字为main
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



## 连接远程仓库

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

# 

## git add

- 把工作区的文件放到暂存区

```
git add .
git add 文件
```

## git commit 

- 把暂存区的文件提交一个本地仓库版本

```
git commit -m "提交说明"
```

## git push

- 把本地仓库提交到github远程仓库









