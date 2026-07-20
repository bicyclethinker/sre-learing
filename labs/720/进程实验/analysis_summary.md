# 2026-07-20 Linux 进程与服务实验复盘

## 一、总体评价

本次练习覆盖了三块内容：

1. Bash 作业控制：`jobs`、`fg`、`bg`
2. 系统进程观察：`ps`、`pstree`、PID、PPID、STAT
3. systemd 服务与后台进程：`systemctl`、`httpd`、`nohup`

整体完成度：

| 实验                   | 完成度 | 评价                                                         |
| ---------------------- | ------ | ------------------------------------------------------------ |
| 练习一：作业与进程状态 | 约 70% | 核心操作基本做了，但违反了部分限制，状态记录不完整           |
| 练习二：服务与后台监控 | 约 45% | 安装、启动、观察服务做了，但“监控任务”和“退出终端后验证”没有真正完成 |
| 练习三：故障排查       | 未完成 | 当前确实还比较难，需要先搭建一个明确的实验环境               |

本次练习的主要问题不是“命令敲得少”，而是：

> 每一步究竟想验证什么，没有提前规定清楚；命令执行之后，也经常缺少最终确认。

------

# 二、练习一分析：作业、PID 与进程状态

## 1. 已经完成的部分

你成功启动了多个后台任务：

```bash
ping baidu.com > null &
ping qq.com > null &
ping weixin.com > null &
```

之后使用了：

```bash
jobs
fg 1
bg 1
fg 1
```

这说明你已经实际练习了：

- 创建后台作业
- 查看当前 Shell 的作业表
- 把后台作业拉到前台
- 暂停前台作业
- 让停止的作业在后台继续
- 再次将其拉回前台

这是本次最有价值的一部分。

你还多次使用：

```bash
pstree -p
ps -axo pid,ppid,stat,command
```

说明你已经知道：

- `jobs` 是 Shell 视角
- `ps` 是操作系统进程视角
- `pstree` 用于观察父子进程关系

这个方向是正确的。

------

## 2. 没有完全达到要求的部分

### 问题一：三个任务一开始全是后台任务

题目要求模拟：

- 一个前台任务
- 一个普通后台任务
- 一个输出重定向的后台任务

但你执行的是：

```bash
ping baidu.com > null &
ping qq.com > null &
ping weixin.com > null &
```

三个任务全部带有 `&`，因此全部一开始就在后台运行。

更符合题意的启动方式应当是：

```bash
ping baidu.com
```

这个先作为前台任务，按下：

```text
Ctrl+Z
```

将其暂停。

另外两个任务再使用后台形式：

```bash
ping qq.com &
ping weixin.com > weixin.log 2>&1 &
```

------

### 问题二：没有完整记录每个任务的信息

题目要求记录：

```text
job编号、PID、PPID、STAT、COMMAND
```

你虽然执行了相关命令，但没有整理成明确对应关系。

建议以后用一条命令直接观察指定进程：

```bash
ps -p 1668,1669,1670 -o pid,ppid,stat,command
```

而不是从全部进程里用 `grep` 慢慢找。

最终应当记录成类似：

| Job  | PID  | PPID          | STAT | COMMAND         |
| ---- | ---- | ------------- | ---- | --------------- |
| %1   | 1668 | 当前 Bash PID | S    | ping baidu.com  |
| %2   | 1669 | 当前 Bash PID | S    | ping qq.com     |
| %3   | 1670 | 当前 Bash PID | S    | ping weixin.com |

实际 PID 应以当时输出为准。

------

### 问题三：没有明确记录状态变化

执行：

```bash
fg 1
bg 1
fg 1
```

操作方向正确，但每次变化后应立即执行：

```bash
jobs -l
ps -p PID -o pid,ppid,stat,tty,command
```

例如暂停后，可能看到：

```text
STAT=T
```

其中 `T` 表示进程处于停止状态。

重新放到后台后，通常会重新变为：

```text
STAT=S
```

或者：

```text
STAT=R
```

只做 `fg`、`bg` 而不查看状态，会导致实验变成“做了操作”，却没有真正验证现象。

------

### 问题四：使用了 `kill -9`

你执行了：

```bash
kill -9 1668
```

但题目明确限制：

```text
不允许使用 kill -9
```

`-9` 对应 `SIGKILL`，进程无法捕获、处理或拒绝这个信号。

本实验要求正常终止，应优先使用：

```bash
kill PID
```

或者明确写：

```bash
kill -15 PID
```

`-15` 是 `SIGTERM`，属于正常终止请求。

你后面执行了：

```bash
kill -15 5517
```

这一步比 `kill -9` 更符合要求。

但是终止后还应验证：

```bash
ps -p 5517
```

如果没有进程信息，才说明它确实退出。

------

### 问题五：使用 `pkill ping` 范围过大

你执行了：

```bash
pkill ping
```

这会按照进程名称终止所有匹配的 `ping` 进程。

在自己的虚拟机实验中危害不大，但它不符合“精确管理进程”的练习目标。系统中如果还有其他人启动的 `ping`，也可能一起被终止。

实验中应优先针对 PID：

```bash
kill -15 PID1 PID2 PID3
```

这样你清楚自己终止的是哪些进程。

------

# 三、`ps` 命令使用分析

## 1. 有价值的探索

你尝试了多种格式：

```bash
ps aux
ps -axo pid
ps -axo pid,ppid,stat,command
ps auxf
ps auxfH
ps -efH
```

这不是完全机械重复，因为你在尝试观察：

- 进程字段
- 父子关系
- 树状结构
- 不同 `ps` 语法

尤其是：

```bash
ps -axo pid,ppid,stat,command
```

是本次实验中非常合适的一条命令。

建议现阶段主要掌握以下三种即可：

```bash
ps aux
ps -ef
ps -eo pid,ppid,stat,tty,%cpu,%mem,command
```

不需要同时记太多相似组合。

------

## 2. `ps -p` 的 PID 写法错误

你执行了：

```bash
ps -p 1669 1669 1670
```

这种写法不规范。

多个 PID 应使用逗号：

```bash
ps -p 1668,1669,1670
```

配合指定字段：

```bash
ps -p 1668,1669,1670 -o pid,ppid,stat,command
```

------

## 3. `grep` 多个 PID 的写法错误

你执行了：

```bash
ps -axo pid,ppid,stat,command | grep 1668 1669 1670
```

`grep` 会把：

```text
1668
```

当成匹配模式，把：

```text
1669
1670
```

当成文件名。

所以这种写法不能表示“匹配三个 PID”。

你又尝试：

```bash
grep 1668,1669,1670
```

它会查找完整字符串：

```text
1668,1669,1670
```

进程列表中通常不存在这样的字符串，因此也匹配不到。

正确方法有两种。

方法一，直接使用 `ps -p`：

```bash
ps -p 1668,1669,1670 -o pid,ppid,stat,command
```

这是最推荐的方法。

方法二，使用扩展正则：

```bash
ps -eo pid,ppid,stat,command | grep -E '1668|1669|1670'
```

不过这种方式还可能显示 `grep` 自己，所以本题直接使用 `ps -p` 更好。

------

## 4. 重定向文件使用混乱

你先执行：

```bash
touch mark
```

然后反复使用：

```bash
ps ... | grep 1668 > mark
ps ... | grep 1669 > mark
ps ... | grep 1670 > mark
```

`>` 每次都会先清空原来的内容，因此最终通常只保留最后一次写入的结果。

后面你开始使用：

```bash
>> mark
```

这说明你已经意识到：

- `>` 是覆盖
- `>>` 是追加

这属于一次有效的纠错过程。

不过为了避免混乱，可以一次性执行：

```bash
ps -p 1668,1669,1670 -o pid,ppid,stat,command > mark
```

------

# 四、`null` 文件的问题

你执行了：

```bash
touch null
ping baidu.com > null &
```

这里的 `null` 只是当前目录下一个名字叫 `null` 的普通文件。

它不是 Linux 中用于丢弃输出的特殊设备。

真正的空设备是：

```bash
/dev/null
```

如果不想保留输出，应写：

```bash
ping baidu.com > /dev/null 2>&1 &
```

如果想保留日志，应使用有意义的文件名：

```bash
ping baidu.com > baidu_ping.log 2>&1 &
```

另外，你让多个后台任务同时写入同一个 `null` 文件：

```bash
ping baidu.com > null &
ping qq.com > null &
ping weixin.com > null &
```

这会带来两个问题：

1. 每条命令启动时都可能截断文件
2. 多个进程同时写入，日志内容会相互混合

这正好违反了题目中的：

```text
不允许让多个后台任务覆盖同一个日志文件
```

正确做法是分别写入：

```bash
ping baidu.com > baidu.log 2>&1 &
ping qq.com > qq.log 2>&1 &
ping weixin.com > weixin.log 2>&1 &
```

------

# 五、练习二分析：HTTP 服务与后台任务

## 1. 已完成的内容

你先检查了程序：

```bash
which httpd
```

发现没有后，执行：

```bash
yum install httpd
```

安装完成后再次检查：

```bash
which httpd
```

这个流程是正确的：

```text
检查是否安装 → 安装 → 再次确认
```

你还使用了：

```bash
systemctl status httpd
systemctl start httpd
```

并通过以下命令观察进程：

```bash
ps aux | grep httpd
ps auxf | grep httpd
pstree -p
ps -efH | grep httpd
```

这说明你已经观察到了 Apache 通常不是只有一个进程，而可能包含：

- 主进程
- 多个工作进程
- 父子进程结构

这一点很有价值。

------

## 2. 服务名拼写错误

你执行了：

```bash
systemctl status hpptd
```

正确名称是：

```bash
httpd
```

这属于普通拼写错误，不严重，但你的历史中这种错误出现频率偏高，例如：

```bash
hpptd
nophup
bgpstree
```

当前最需要养成的习惯是：

> 命令失败后先看报错，不要立即连续猜新写法。

可以通过 Tab 补全降低拼写错误：

```bash
systemctl status htt<Tab>
```

------

## 3. 混用了手动启动和 systemd 启动

你先直接执行：

```bash
httpd
```

随后又执行：

```bash
systemctl start httpd
```

这两者不是完全相同的管理方式。

直接执行：

```bash
httpd
```

是手动运行程序。

执行：

```bash
systemctl start httpd
```

是让 systemd 根据服务单元管理它。

如果服务由 systemd 启动，后续应该优先使用：

```bash
systemctl stop httpd
systemctl restart httpd
systemctl status httpd
```

而不是：

```bash
pkill httpd
```

因为 `pkill httpd` 绕过了 systemd 的服务管理逻辑。

你这次无意中练到了一个很重要的区别：

```text
运行了一个 httpd 进程
```

不一定等于：

```text
httpd.service 正常运行
```

------

## 4. 不能只用 `ps | grep` 判断服务正常

你多次使用：

```bash
ps aux | grep httpd
```

它只能证明：

> 当前存在名称或命令行中包含 `httpd` 的进程。

不能证明服务真的能对外提供 Web 页面。

完整验证至少应该包括：

```bash
systemctl is-active httpd
```

以及本机访问测试：

```bash
curl -I http://127.0.0.1
```

如果返回类似：

```text
HTTP/1.1 403 Forbidden
```

或者：

```text
HTTP/1.1 200 OK
```

都说明 HTTP 服务确实响应了请求。

还可以检查端口：

```bash
ss -lntp | grep ':80'
```

你这次没有执行这些验证，所以只能说明“看到了 httpd 进程”，还不能确认服务完整正常。

------

## 5. 没有完成重启前后 PID 对比

题目要求：

```text
重启服务，比较重启前后的 PID
```

但历史中没有明显看到：

```bash
systemctl restart httpd
```

也没有分别保存重启前后的主进程 PID。

推荐方式：

```bash
systemctl show httpd -p MainPID
systemctl restart httpd
systemctl show httpd -p MainPID
```

或者：

```bash
pidof httpd
```

但 `pidof httpd` 可能返回多个 PID，因此观察服务主进程时，`systemctl show` 更直接。

------

## 6. `nohup` 练习只完成了一半

你先误写：

```bash
nophup ping baidu.com > null &
```

随后纠正为：

```bash
nohup ping baidu.com > null &
```

这是一次有效纠错。

但这个任务只是持续执行 `ping`，并不是题目要求的：

> 定期把 HTTP 服务状态写入日志。

真正的监控任务可以使用：

```bash
nohup bash -c 'while true; do
    echo "===== $(date) ====="
    systemctl is-active httpd
    sleep 10
done' > /root/httpd_monitor.log 2>&1 &
```

这条命令涉及之前没有系统学习过的循环，因此如果作为新手实验，我会直接把它作为“环境搭建命令”提供给你，而不是要求你独立写出来。

你真正需要独立完成的是：

- 记录监控进程 PID
- 退出终端
- 重新登录
- 使用 `ps` 找到它
- 确认 `jobs` 中没有它
- 使用 `kill -15` 终止它

------

## 7. 没有验证退出终端后仍然运行

题目要求：

```text
退出当前终端后仍能继续运行
重新登录后找到监控进程
```

历史中没有看到完整的：

```text
启动 nohup 任务
记录 PID
exit
重新 SSH 登录
ps 查看 PID
```

因此这一项还没有完成。

特别要注意：

```bash
nohup command &
```

只能让进程具备脱离终端继续运行的条件。

你必须真正退出当前 Shell 并重新登录，才能完成验证。

------

## 8. 没有检查服务日志

题目要求查看服务相关日志，但历史中没有出现：

```bash
journalctl -u httpd
```

推荐使用：

```bash
journalctl -u httpd --since "10 minutes ago"
```

也可以查看最近部分：

```bash
journalctl -u httpd -n 30
```

HTTP 服务自身还可能有日志：

```bash
ls -l /var/log/httpd/
tail /var/log/httpd/error_log
tail /var/log/httpd/access_log
```

------

# 六、有效重复与机械重复

## 有效重复

以下操作属于有效重复：

### 1. `ps` 不同格式对比

```bash
ps auxf
ps auxfH
ps -efH
ps -axo pid,ppid,stat,command
```

你是在比较不同进程展示方式。

### 2. `>` 和 `>>` 的区别验证

你先覆盖 `mark`，后面改成追加，体现了对重定向行为的实际观察。

### 3. `jobs`、`fg`、`bg` 组合

这些命令必须在真实作业状态中反复使用，不能只背概念。

### 4. httpd 安装前后检查

```bash
which httpd
yum install httpd
which httpd
```

这是标准的验证闭环。

------

## 可能偏机械的重复

### 1. 完全重复执行

```bash
wc -l /root/.bash_history
wc -l /root/.bash_history
```

如果两次之间没有修改文件，这种重复基本没有新信息。

### 2. 多次执行 `pstree`

```bash
pstree
pstree -p
```

如果每次执行前没有改变进程状态，那么观察价值有限。

建议执行前先写标记：

```bash
echo "暂停前"
pstree -p

echo "暂停后"
pstree -p
```

### 3. 连续尝试多个 `ps` 参数

```bash
ps -auxo
ps -auxo pid
ps -axo pid
```

这部分带有探索性质，但因为没有围绕明确问题展开，容易变成“猜参数”。

更好的方式是先明确问题：

```text
我现在需要看 PID、PPID、STAT 和 COMMAND。
```

然后直接查帮助或使用：

```bash
ps -eo pid,ppid,stat,command
```

------

# 七、错误习惯总结

## 1. 拼写错误比较多

出现了：

```bash
hpptd
nophup
bgpstree
```

解决办法：

- 多使用 Tab 补全
- 命令报错后先读第一行报错
- 不要立即连续猜写法

------

## 2. 终止范围过大

出现了：

```bash
pkill ping
pkill httpd
```

建议新手阶段优先：

```bash
kill -15 具体PID
```

只有明确希望终止某个名称的全部进程时，才考虑 `pkill`。

------

## 3. 过早使用 `kill -9`

出现了：

```bash
kill -9 1668
```

正确顺序应为：

```bash
kill -15 PID
sleep 1
ps -p PID
```

只有进程无法正常退出，并且确认不会造成数据损坏时，才考虑：

```bash
kill -9 PID
```

------

## 4. 操作后验证不足

例如：

```bash
kill -15 5517
```

后面没有立即执行：

```bash
ps -p 5517
```

又例如启动服务后只看了进程，没有执行：

```bash
systemctl is-active httpd
curl -I http://127.0.0.1
```

以后每个操作应形成三步：

```text
操作前检查
执行操作
操作后验证
```

------

## 5. 服务与普通进程管理混在一起

看到 `httpd` 进程后，直接使用：

```bash
pkill httpd
```

容易忽略这个进程可能由 systemd 管理。

以后先判断：

```bash
systemctl status httpd
```

如果属于 systemd 服务，应使用：

```bash
systemctl stop httpd
```

如果只是自己启动的测试进程，才使用：

```bash
kill -15 PID
```

------

# 八、排错意识评价

## 做得比较好的地方

你已经表现出初步排错意识：

1. 使用 `which httpd` 判断是否安装
2. 命令拼错后进行了修正
3. 使用 `pstree` 观察父子关系
4. 使用多种 `ps` 格式观察进程
5. 发现覆盖问题后开始使用 `>>`
6. `nophup` 失败后改为 `nohup`

说明你不是只会照抄，而是在尝试根据现象调整操作。

------

## 需要加强的地方

### 1. 没有充分阅读报错

例如：

```bash
grep 1668 1669 1670
```

通常会直接提示找不到名为 `1669`、`1670` 的文件。

如果认真读取错误，就可以判断：

> grep 把后面的数字当成文件名了。

### 2. 没有使用退出状态

命令执行后可以观察：

```bash
echo $?
```

一般：

```text
0 表示成功
非 0 表示失败
```

### 3. 缺少服务专用排错工具

本次几乎没有使用：

```bash
systemctl is-active
journalctl -u
curl
ss
```

这四个命令比单独执行 `ps | grep` 更能判断服务是否正常。

------

# 九、本次应掌握的核心关系

## 1. Job 编号

例如：

```text
[1]
```

Job 编号只存在于当前 Shell 的作业表中。

使用：

```bash
jobs
fg %1
bg %1
```

进行管理。

它不是系统级编号。

------

## 2. PID

PID 是操作系统为进程分配的编号。

使用：

```bash
ps
kill
```

管理。

同一个进程可能同时具有：

```text
Job 编号 %1
PID 1668
```

但两者不是同一个概念。

------

## 3. PPID

PPID 是父进程 PID。

当前 Bash 启动的普通命令，其 PPID 通常就是当前 Bash 的 PID。

可以使用：

```bash
echo $$
ps -p 目标PID -o pid,ppid,command
```

进行比较。

------

## 4. STAT

常见状态：

| 状态 | 含义                         |
| ---- | ---------------------------- |
| R    | 正在运行或等待 CPU           |
| S    | 可中断睡眠，正在等待某个事件 |
| T    | 已停止，例如被 `Ctrl+Z` 暂停 |
| Z    | 僵尸进程                     |
| D    | 不可中断睡眠，通常在等待 I/O |
| +    | 位于前台进程组               |
| s    | 会话领导进程                 |
| l    | 多线程进程                   |

一个状态可能包含多个字符，例如：

```text
Ss
S+
Sl
```

------

## 5. 前台与后台

前台任务占用当前终端，通常接收键盘输入和终端信号。

后台任务不占用命令提示符，可以使用：

```bash
command &
```

启动。

但后台不等于脱离终端。

------

## 6. 停止与运行

按下：

```text
Ctrl+Z
```

通常向前台进程发送停止信号。

此时进程还存在，只是不再继续执行。

使用：

```bash
bg %1
```

让它在后台继续运行。

使用：

```bash
fg %1
```

让它回到前台。

------

# 十、当前几乎没有主动使用的重要命令

建议下一次练习补充：

```bash
jobs -l
ps -p PID -o pid,ppid,stat,tty,command
pgrep -a 进程名
systemctl is-active httpd
systemctl show httpd -p MainPID
systemctl restart httpd
journalctl -u httpd -n 30
curl -I http://127.0.0.1
ss -lntp
tty
```

不要求一次全部记住。

其中下一轮最重要的是：

```bash
jobs -l
ps -p PID -o pid,ppid,stat,tty,command
systemctl is-active httpd
journalctl -u httpd
```

------

# 十一、简化版练习三

原练习三对你目前来说确实偏难。可以改为下面这个明确、可搭建的版本。

## 实验目标

观察一个 `nohup` 进程在退出原终端后：

- `ps` 仍然能看到
- 新 Shell 的 `jobs` 看不到
- PPID 可能发生变化
- TTY 可能变成 `?`

------

## 第一步：由题目提供环境搭建命令

在当前终端执行：

```bash
nohup sleep 1000 > /tmp/nohup_sleep.log 2>&1 &
echo $! > /tmp/nohup_sleep.pid
```

这里的新知识：

- `$!` 表示最近一个后台进程的 PID
- PID 被保存进 `/tmp/nohup_sleep.pid`
- `nohup` 使进程忽略终端挂断信号

------

## 第二步：在原终端观察

你独立执行：

```bash
jobs -l
cat /tmp/nohup_sleep.pid
ps -p "$(cat /tmp/nohup_sleep.pid)" -o pid,ppid,stat,tty,command
```

记录当前：

```text
PID
PPID
STAT
TTY
Job 编号
```

------

## 第三步：退出并重新登录

执行：

```bash
exit
```

重新使用 SSH 登录虚拟机。

------

## 第四步：重新检查

执行：

```bash
jobs
ps -p "$(cat /tmp/nohup_sleep.pid)" -o pid,ppid,stat,tty,command
pstree -sp "$(cat /tmp/nohup_sleep.pid)"
```

此时重点观察：

1. `jobs` 为什么为空
2. `ps` 为什么还能看到进程
3. PPID 是否改变
4. TTY 是否为 `?`

------

## 第五步：写出两个假设

在查看答案前，先写：

```text
假设一：jobs 只能看到当前 Shell 创建并登记的作业。
假设二：重新登录产生了一个新的 Bash，它没有原来 Shell 的作业表。
```

------

## 第六步：正常清理

执行：

```bash
kill -15 "$(cat /tmp/nohup_sleep.pid)"
ps -p "$(cat /tmp/nohup_sleep.pid)"
rm -f /tmp/nohup_sleep.pid /tmp/nohup_sleep.log
```

如果 `ps` 不再显示目标进程，说明清理完成。

------

# 十二、一句话总结

这次你已经真正接触到了：

> Shell 作业表、系统进程表和 systemd 服务管理是三个不同的管理层次。

下一步不是继续大量尝试更多 `ps` 参数，而是把每次实验固定成：

```text
先记录状态 → 执行一个操作 → 再记录状态 → 比较变化 → 最后清理
```