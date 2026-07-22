# RAID 

## RAID0**（条带化，Striping）**

- **特点**: 将数据分块并在多个磁盘上并行存储，提升读写性能。
- **优点**: 极大提高了读写速度，因为数据在多个磁盘上并行操作。
- **缺点**: 没有冗余，任何一个磁盘的损坏都会导致整个 RAID 失效，数据丢失。
- **适用场景**: 需要高性能、数据安全性需求不高的场景，比如视频编辑、临时数据存储等。



![image-20260722174109987](RAID&LVM.assets/image-20260722174109987.png)

## **RAID 1（镜像,**

## **Mirroring）**

- **特点**: 将数据完全复制到另一块磁盘上，提供 1:1 的数据冗余。
- **优点**: 数据安全性高，任何一个磁盘损坏时，数据仍可以从镜像磁盘中恢复。
- **缺点**: 存储效率较低，实际可用存储空间为所有磁盘容量的一半。
- **适用场景**: 需要高数据可靠性和快速恢复的场合，比如数据库、操作系统磁盘等。

![image-20260722174227545](RAID&LVM.assets/image-20260722174227545.png)

## **RAID 5（带奇偶校验的条带化，Striping with Parity）**

- **特点**: 将数据和奇偶校验数据分布在所有磁盘上，提供冗余的同时提升性能。
- **优点**: 提供数据冗余，同时存储效率较高（磁盘数量越多，效率越好），能够在一个磁盘损坏的情况下恢复数据。
- **缺点**: 写入性能稍差，尤其是在发生设备故障时，重建过程较慢。
- **适用场景**: 需要兼顾性能和数据冗余的场景，如文件服务器和应用服务器。
- 写性能*1，读性能\*（N-1）

![image-20260722174319628](RAID&LVM.assets/image-20260722174319628.png)

## **RAID 10（RAID 1+0，镜像与条带化的组合）**

- **特点**: 将 RAID 1（镜像）和 RAID 0（条带化）结合使用，既提供数据冗余又提升了性能。
- **优点**: 既有 RAID 0 的高性能，又有 RAID 1 的高安全性。能够快速恢复数据。
- **缺点**: 存储效率较低，要求至少 4 块磁盘，实际可用空间为所有磁盘容量的一半。
- **适用场景**: 需要高性能和高冗余的关键应用场景，如数据库、虚拟化环境等。
- 读写性能*N（N为RAID0的硬盘数，此例为2），不能同时坏数据块1a与2a

![image-20260722174626228](RAID&LVM.assets/image-20260722174626228.png)

## **硬件磁盘阵列（Hardware RAID）**

### 特点

- **专用硬件控制器**：硬件 RAID 使用专门的 RAID 控制器卡（RAID Controller），通常是插入主板的 PCIe 扩展卡或者集成在服务器主板上的芯片。该控制器管理磁盘阵列的所有操作并执行 RAID 级别的计算。
- **独立于操作系统**：硬件 RAID 完全独立于操作系统，RAID 控制器负责所有 RAID 相关操作，操作系统只看到一个逻辑磁盘。
- **硬件加速**：硬件 RAID 卡通常带有专用处理器（ASIC 或 FPGA）和缓存，用来加速 RAID 操作，提高性能

![image-20260722174722407](RAID&LVM.assets/image-20260722174722407.png)

## **软件磁盘阵列（Software RAID）**

### **特点**

- **由操作系统实现**：软件 RAID 通过操作系统内核或专门的 RAID 软件来管理和实现 RAID 功能。Linux 的 `mdadm`、Windows 的动态磁盘管理和 macOS 的磁盘工具等都支持软件 RAID。
- **没有专用硬件**：软件 RAID 不需要专用的 RAID 控制器，所有 RAID 计算和管理工作都由主机的 CPU 执行。
- **灵活性高**：软件 RAID 通常可以在不同的硬件平台之间迁移，因为 RAID 配置数据存储在磁盘上，而不是特定的控制器中。

## **硬件 RAID 和软件 RAID 的对比**

| **对比项**   | **硬件 RAID**                            | **软件 RAID**                      |
| ------------ | ---------------------------------------- | ---------------------------------- |
| **实现方式** | 专用 RAID 控制器                         | 操作系统或软件实现                 |
| **性能**     | 高，特别是在 RAID 5/6 等级别中           | 较低，占用主机 CPU 资源            |
| **磁盘管理** | 独立于操作系统，系统只看到一个逻辑磁盘   | 依赖于操作系统的磁盘管理           |
| **成本**     | 高，需购买 RAID 控制器                   | 低，无需额外硬件                   |
| **功能支持** | 支持热备盘、硬盘监控、电池缓存等高级功能 | 功能较少，依赖操作系统提供的功能   |
| **灵活性**   | 依赖特定硬件，不易迁移                   | 容易迁移，跨硬件平台使用           |
| **故障恢复** | 控制器损坏可能需要相同型号的控制器       | 可以在不同硬件上恢复               |
| **适用场景** | 企业级、大型存储系统，数据中心           | 个人用户、小型服务器，开发测试环境 |

## *部署磁盘阵列*

###  mdadm磁盘整列

对于Linux上通过软件的形式构建磁盘整列，我们常用的方式是通过mdadm这个工具来完成。当然对于部分Linux发行版本，mdadm可能没有内置安装好，所以我们需要手动安装一下。

``` shell
root@localhost ~]# yum -y install mdadm
```

## mdadm命令（Multiple Devices）

常用参数以及作用

| 参数 | 作用             |
| ---- | ---------------- |
| -a   | 检测设备名称     |
| -n   | 指定设备数量     |
| -l   | 指定RAID级别     |
| -C   | 创建             |
| -v   | 显示过程         |
| -f   | 模拟设备损坏     |
| -r   | 移除设备         |
| -Q   | 查看摘要信息     |
| -D   | 查看详细信息     |
| -S   | 停止RAID磁盘阵列 |
| -x   | 备份盘数量       |

### 创建RAID

```SHELL
mdadm -Cv /dev/md0 -a yes -n 磁盘数量 -l RAID级别 磁盘列表
```

```shell
#RAID0
mdadm -Cv /dev/md0 -a yes -n 2 -l 0 /dev/nvme0n2 /dev/nvme0n3
#RAID1
mdadm -Cv /dev/md0 -a yes -n 2 -l 1 /dev/nvme0n2 /dev/nvme0n3
#RAID10
mdadm -Cv /dev/md0 -a yes -n 4 -l 10 
/dev/nvme0n2 /dev/nvme0n3 /dev/nvme0n4 /dev/nvme0n5
#RAID5+一块热备盘
mdadm -Cv /dev/md0 -n 3 -l 5 -x 1 
/dev/nvme0n2 /dev/nvme0n3 /dev/nvme0n4 /dev/nvme0n5
```

### 创建后：格式化、挂载

RAID 创建后，`/dev/md0` 就可以当作一块普通磁盘使用：

```
mkfs.ext4 /dev/md0
mkdir /mnt/RAID
mount /dev/md0 /mnt/RAID
df -h
```

````shell
#永久挂载
echo "/dev/md0 /mnt/RAID ext4 defaults 0 0" >> /etc/fstab
systemctl daemon-reload
mount -a
````

### 查看 RAID 状态

```
mdadm -D /dev/md0
```

- 重点看

  ```shell
  State              阵列整体状态
  Active Devices     正常参与工作的磁盘
  Working Devices    当前可工作的磁盘
  Failed Devices     故障磁盘数量
  Spare Devices      热备盘数量
  Chunk Size         条带块大小
  Rebuild Status     重建进度
  ```

### 模拟故障与修复

- 标记磁盘故障

  ``` shell
  mdadm /dev/md0 -f /dev/nvme0n2
  ```

- 从阵列移除故障盘

  ```shell
  mdadm /dev/md0 -r /dev/nvme0n2
  ```

- 添加新磁盘

  ```
  mdadm /dev/md0 -a /dev/nvme0n2
  ```

-  删除 RAID 阵列

  ```shell
  umount /dev/md0
  mdadm -S /dev/md0 #停止阵列
  #清除成员盘上的 RAID 元数据
  mdadm --zero-superblock /dev/nvme0n2 /dev/nvme0n3
  lsblk
  ```

  

##         fio 基础测试

```
fio --name=write_test \
--filename=/mnt/RAID/testfile \
--size=1G \
--bs=1M \
--rw=write \
--direct=1 \
--numjobs=1
```

# LVM磁盘阵列

> 辑卷管理器是Linux系统用于对硬盘分区进行管理的一种机制，理论性较强，其创建初衷是为了解决硬盘设备在创建分区后不易修改分区大小的缺陷。尽管对传统的硬盘分区进行强制扩容或缩容从理论上来讲是可行的，但是却可能造成数据的丢失。而LVM技术是在硬盘分区和文件系统之间添加了一个逻辑层，它提供了一个抽象的卷组，可以把多块硬盘进行卷组合并。这样一来，用户不必关心物理硬盘设备的底层架构和布局，就可以实现对硬盘分区的动态调整。

![image-20260722191344441](RAID&LVM.assets/image-20260722191344441.png)

物理卷处于LVM中的最底层，可以将其理解为物理硬盘、硬盘分区或者RAID磁盘阵列，这都可以。卷组建立在物理卷之上，一个卷组可以包含多个物理卷，而且在卷组创建之后也可以继续向其中添加新的物理卷。逻辑卷是用卷组中空闲的资源建立的，并且逻辑卷在建立后可以动态地扩展或缩小空间。这就是LVM的核心理念。

PE最小存储单元，VG通过PV组合好PE，成一个整的；然后LV从VG分出来可供挂载使用

## 部署逻辑卷

常用的LVM部署命令

| 功能/命令 | 物理卷管理 | 卷组管理  | 逻辑卷管理 |
| --------- | ---------- | --------- | ---------- |
| 扫描      | pvscan     | vgscan    | lvscan     |
| 建立      | pvcreate   | vgcreate  | lvcreate   |
| 显示      | pvdisplay  | vgdisplay | lvdisplay  |
| 删除      | pvremove   | vgremove  | lvremove   |
| 扩展      |            | vgextend  | lvextend   |
| 缩小      |            | vgreduce  | lvreduce   |











