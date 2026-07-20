# 一些奇思妙想











# 我盲猜很重要，先记录下来

- 直接启动和systemctl启动的区别？

> ```bash
> httpd
> ```
>
> 随后又执行：
>
> ```bash
> systemctl start httpd
> ```
>
> 这两者不是完全相同的管理方式。
>
> 直接执行：
>
> ```bash
> httpd
> ```
>
> 是手动运行程序。
>
> 执行：
>
> ```bash
> systemctl start httpd
> ```
>
> 是让 systemd 根据服务单元管理它。
>
> 如果服务由 systemd 启动，后续应该优先使用：
>
> ```bash
> systemctl stop httpd
> systemctl restart httpd
> systemctl status httpd
> ```
>
> 而不是：
>
> ```bash
> pkill httpd
> ```
>
> 因为 `pkill httpd` 绕过了 systemd 的服务管理逻辑。
>
> 你这次无意中练到了一个很重要的区别：
>
> ```text
> 运行了一个 httpd 进程
> ```
>
> 不一定等于：
>
> ```text
> httpd.service 正常运行
> ```
>
> ------
>
> ## 
