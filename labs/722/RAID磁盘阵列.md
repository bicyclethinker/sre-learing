

# RAID实验

## RAID0

- 创建RAID0磁盘阵列，文件系统，挂载

  ```shell
  #这里要注意,理论上要写/dev/md0,如果写成下面这个样子的话，会在/dev目录下面创建一个md文件。md文件里面会有md0文件夹。这是我自己操作失误导致的
  mdadm -Cv md0 -a yes -n 2 -l 0 /dev/nvme0n2 /dev/nvme0n3
  
  mkfs.ext4 /dev/md0
  mkdir RAID0
  #挂载操作
  mount /dev/md/md0 /mnt/RAID0
  #写入默认配置
  echo "/dev/md127 /mnt/RAID0 ext4 defaults 0 0" >> /etc/fstab
  ```


![image-20260722181840886](RAID%E7%A3%81%E7%9B%98%E9%98%B5%E5%88%97.assets/image-20260722181840886.png)

![image-20260722183118122](RAID%E7%A3%81%E7%9B%98%E9%98%B5%E5%88%97.assets/image-20260722183118122.png)

![image-20260722183246305](RAID%E7%A3%81%E7%9B%98%E9%98%B5%E5%88%97.assets/image-20260722183246305.png)

- 重启后，依然挂载

![image-20260722183648477](RAID%E7%A3%81%E7%9B%98%E9%98%B5%E5%88%97.assets/image-20260722183648477.png)

磁盘状态 

![image-20260722183834275](RAID%E7%A3%81%E7%9B%98%E9%98%B5%E5%88%97.assets/image-20260722183834275.png)

停止md0阵列

![image-20260722184147535](RAID%E7%A3%81%E7%9B%98%E9%98%B5%E5%88%97.assets/image-20260722184147535.png)

清除数据。需要注意的是，其实可以不清除数据，直接用fdisk再进行分盘等操作= =

![image-20260722184341158](RAID%E7%A3%81%E7%9B%98%E9%98%B5%E5%88%97.assets/image-20260722184341158.png)

## RAID10

创建一个有热备盘的raid10磁盘阵列

![image-20260722184956857](RAID%E7%A3%81%E7%9B%98%E9%98%B5%E5%88%97.assets/image-20260722184956857.png)

需要注意的是：

`-n`后面的参数是活动盘的参数

`-x`后面的参数是热备盘的参数

不能搞错,会报错哦！

设备信息

![image-20260722185133322](RAID%E7%A3%81%E7%9B%98%E9%98%B5%E5%88%97.assets/image-20260722185133322.png)

![image-20260722185331454](RAID%E7%A3%81%E7%9B%98%E9%98%B5%E5%88%97.assets/image-20260722185331454.png)

- 模拟md0出现损坏

![image-20260722185451684](RAID%E7%A3%81%E7%9B%98%E9%98%B5%E5%88%97.assets/image-20260722185451684.png)

- 移除nvme0n2

![image-20260722185936070](RAID%E7%A3%81%E7%9B%98%E9%98%B5%E5%88%97.assets/image-20260722185936070.png)

- 添加新的磁盘nvme0n7

  ![image-20260722190254374](RAID%E7%A3%81%E7%9B%98%E9%98%B5%E5%88%97.assets/image-20260722190254374.png)

