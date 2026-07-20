# 一些奇思妙想











# 我盲猜很重要，先记录下来

## 直接启动和systemctl启动的区别？


##  -  核心区别

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
