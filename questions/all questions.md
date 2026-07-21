# 一些奇思妙想











# 我盲猜很重要，先记录下来

## 直接启动和systemctl启动的区别？


##  

直接运行

```
httpd 
```

只是运行程序本身。

通常缺少 systemd 提供的：

- 统一状态管理
- 自动重启
- 启动依赖
- 服务日志关联
- 开机自动启动
- 正确的停止流程
- cgroup 进程组织

停止时你可能需要自己查 PID：

```
ps -ef | grep httpd
kill PID
```

------

systemctl 启动

```
systemctl start httpd
```

systemd 根据：

```
httpd.service
```

中的规则启动和管理服务。

可以统一执行：

```
systemctl status httpd
systemctl restart httpd
systemctl stop httpd
journalctl -u httpd
```

所以对于系统服务，通常优先使用：

```
systemctl
```

而不是直接运行程序。

### 

```
当前 Bash
│
├── sleep 1000 &
│      ├── jobs 能看到
│      └── ps 能看到
│
└── systemctl start httpd
       │
       └── systemctl 请求 systemd
                │
                └── systemd 启动 httpd
                       ├── jobs 看不到
                       ├── ps 能看到
                       └── systemctl 能管理
```

最核心的一句话：

> `jobs` 关心“是不是我的 Shell 作业”，`ps` 关心“系统里有没有这个进程”，`systemctl` 关心“这个进程是不是一个由 systemd 管理的服务”。

## 删除和创建的本质是什么？

执行：

```
rm a.txt
```

底层主要是在进行类似 `unlink()` 的操作。

`unlink` 这个名字非常准确：

> 解除一个文件名与 inode 之间的链接。

原来：

```
a.txt ─┐
       ├──→ inode 1001 ──→ 数据 block
b.txt ─┘
```

执行：

```
rm a.txt
```

之后变成：

```
a.txt 目录项被删除

b.txt ──→ inode 1001 ──→ 数据 block
```

inode 1001 的硬链接计数从：

```
2 → 1
```

因此文件数据仍然存在，也仍然属于 inode 1001。

这时：

```
cat b.txt
```

当然还可以正常读取。

从最底层看，存储介质确实通过某种物理状态表示 0 和 1，但删除文件不是简单讨论“某个位变成另一种状态”。

文件系统看到的层次是：

```
磁盘物理状态
    ↓
扇区或逻辑块
    ↓
文件系统 block
    ↓
inode
    ↓
目录项和文件名
```

`rm` 主要修改的是上层管理信息：

```
目录项
inode 链接计数
inode/block 分配状态
文件系统日志
```

它不一定需要立即重写所有保存文件内容的扇区。

因此，“删除”更多是一种：

> 取消管理关系，并允许存储空间重新使用。
