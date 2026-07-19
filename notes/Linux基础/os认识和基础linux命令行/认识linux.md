# 操作系统

- 操作系统是一组软件，它通过分配资源来管理计算机的硬件和应用程序。

## shell

 ### shell的定义

![image-20260714175547128](%E8%AE%A4%E8%AF%86linux.assets/image-20260714175547128.png)



### shell使用的两种方式

- 命令行
- 脚本文件



### bashshell提示符

- ```shell
  [root@localhost ~]# 
  ```

解释：用户名|主机名|目录名|操作权限（#超级管理员，$普通用户）

# Linux

## linux基本常识

- linux对大小写敏感

- linux对空格敏感，但空格只是作为分隔符而已

  ```shell
  #下面两条指令的效果是一样
  ls -a            -l
  ls -a -l 
  ```

  

- 相对路径/绝对路径

  .       | 当前目录
  ..      | 上一级目录
  ./test1  |当前目录下的 test1
  ../test1 | 上一级目录下的 test1





## linux目录结构

![image-20260716092310806](%E8%AE%A4%E8%AF%86linux.assets/image-20260716092310806.png)

## linux基础命令

- ls/cd/pwd/clear/echo/poweroff/reboot/whoami/history/help/man

- 基本格式 ： 命令|选项|参数

| 命令 | 全称                 |
| ---- | -------------------- |
| ls   | list directory       |
| cd   | change directory     |
| pwd  | print work directory |















### ls命令

参数简写|全称（功能）

-a			| all（列出所有的文件）

-l 			|long listing

-r			 |reverse(反向排序)

-t			  |time(修改时间排序,新修改的在前)

-A 			 |almost all(不显示./..)

-F 			| classify(附加类型标志)

-R 			|recursive(递归显示子目录的内容)

-h 			|human-reable

-d  			|看目录自己

常用-lh,-lha

### cd命令

- cd~ 回到家目录
- cd ..  回到上一层
- cd  /usr//bin基本用法

### pwd

- 显示当前的工作目录

### clear

- 清除屏幕

### echo

- 格式：

  echo|选项|字符串

- 用处

   用于字符串的输出

- 常用举例

  - echo -n "helloLinux"  #不输出结尾的换行符
  - echo -e "hello\tLinux" #解释转义字符

- 异常的例子

  - echo -e "C:\\\Users\\\root"

    预判输出：C:\Users\root

    实际输出：

![image-20260714200620945](%E8%AE%A4%E8%AF%86linux.assets/image-20260714200620945.png)

​			解释：shell先把两层\\\变成了一层

C：\Users\root

\r被解释为回车符

C:\Users

最后输出ootUsers



```
echo "linux" > 2.txt #覆写文件，会覆盖
echo "hello,linux" >> 2.txt #追加文件mo
```





## 系统命令

### poweroff

- 关机

选项|含义

-n|不执行文件系统的同步操作

-w|仅仅记录关机信息到/var/log/wtmp中

-d|不把记录写到var/log/wtmp文件中

-f|强制关机



### reboot

- 重启计算机

选项同上



### whoami

- 用于显示自身用户名称

### 快捷键

ctrl 快捷键|作用

D|exit

L|清屏

A|光标移动到最前端

E|光标到最后

U|删除光标前面的所有字符

K|删除光标后面的所有字符

R|搜索历史命令，利用关键词



## 帮助命令

### history

选项|含义

-c|删除所有的history命令

-a|将新增的命令写入hisfiles

-r|将hisfiles内容读入到shell的history记忆中

-w|将目前的history记忆的内容写入到hisfiles



### help·

- 显示命令的帮助信息

  help [-dms] [内置命令]

| 选项 |                     含义                      |
| :--- | :-------------------------------------------: |
| `-d` |            输出每个主题的简短描述             |
| `-m` |        以伪 man 手册的格式显示使用方法        |
| `-s` | 为每一个匹配 PATTERN 模式的主题仅显示一个用法 |





## man

- 显示在线帮助手册页

快捷键

- / 从上至下搜索某一个关键词
- ？从下至上搜索某一个关键词？linux
- n 定位到上一个搜索到的关键词
- N 定位到下一个搜索到的关键词

### alias

- 用于设置指令的别名

## 压缩和解压缩命令


## **环境变量**

- #### **通俗解释**

  环境变量就像"全局设置"

  想象你的电脑是一家餐厅：

  每个员工（程序）都需要知道"仓库在哪里"、"老板电话是多少"

  与其每次都去问，不如把这些信息贴在公告栏上

  环境变量就是这块公告栏 —— 所有程序都能随时去查

#### **什么是环境变量**

- 需要注意的是：环境变量是属于进程的，而不是某个用户或者系统

变量名 = 值

PATH = /usr/bin:/usr/local/bin

可以通过env命令查看所有的环境变量

#### **最重要的环境变量**

```shell
# 1. PATH —— 最重要！决定命令去哪找
echo $PATH
# /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin

# 2. HOME —— 当前用户的家目录
echo $HOME     # /home/july

```

#### **临时和永久设置环境变量**

```shell
export MY_NAME="July"
echo $MY_NAME    # July

# 关掉终端就消失了！

```

#### **永久设置**

```
# 编辑 ~/.bashrc 或 ~/.bash_profile
echo 'export MY_NAME="July"' >> ~/.bashrc
source ~/.bashrc   # 让修改立即生效

```

#### **理解Path变量的追加**

```
export PATH=$PATH:/opt/my-tools/bin
# 注意：是追加，不是覆盖！

```

