---
title: linux LVM扩容笔记
date: 2016-08-11 13:41:10
tags: Linux
---

虚机上安装oracle 11g时，提示空间不足。虚机系统是redhat6.6，安装时选的是LVM方式管理分区，通过lvm命令能够方便的实现磁盘扩容。本文是对lvm扩容的操作步骤做个记录。

步骤：

1. 添加磁盘；
2. 创建分区；
3. 创建物理卷（pv）；
4. 扩容卷组（vg）；
5. 扩容逻辑分区（lv）；
6. resize2fs 扩大分区

### 具体步骤

1. 查看硬盘信息
``` shell
[root@you 桌面]# fdisk -l

Disk /dev/sda: 21.5 GB, 21474836480 bytes
255 heads, 63 sectors/track, 2610 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x0000acb9

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *           1          64      512000   83  Linux
Partition 1 does not end on cylinder boundary.
/dev/sda2              64        2611    20458496   8e  Linux LVM

Disk /dev/sdb: 16.1 GB, 16106127360 bytes 
255 heads, 63 sectors/track, 1958 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000


Disk /dev/mapper/vg_you-lv_root: 18.8 GB, 18798870528 bytes
255 heads, 63 sectors/track, 2285 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000


Disk /dev/mapper/vg_you-lv_swap: 2147 MB, 2147483648 bytes
255 heads, 63 sectors/track, 261 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

```
输出的信息可以看到新添加到磁盘，**Disk /dev/sdb: 16.1 GB**

2. 磁盘分区
``` shell
[root@localhost Desktop]# fdisk /dev/sdb

# 查看磁盘分区情况（分区前）
Command (m for help): p

Disk /dev/sdb: 16.1 GB, 16106127360 bytes
255 heads, 63 sectors/track, 1958 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0xe5d82e32

   Device Boot      Start         End      Blocks   Id  System

# 创建分区
Command (m for help): n
Command action
   e   extended
   p   primary partition (1-4)
p
Partition number (1-4): 1
First cylinder (1-1958, default 1): 1
Last cylinder, +cylinders or +size{K,M,G} (1-1958, default 1958): 1958

# 查看磁盘分区情况（分区后）
Command (m for help): p

Disk /dev/sdb: 16.1 GB, 16106127360 bytes
255 heads, 63 sectors/track, 1958 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0xeecf4ffe

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1               1        1958    15727603+  83  Linux

```

3.创建物理卷


``` shell
# 查看物理卷
[root@you 桌面]# pvdisplay
  --- Physical volume ---
  PV Name               /dev/sda2
  VG Name               vg_you
  PV Size               19.51 GiB / not usable 3.00 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              4994
  Free PE               0
  Allocated PE          4994
  PV UUID               1xYCsO-8Ch0-aFGl-0bzh-pfvM-VTVC-uekyMh

# 创建pv卷
[root@you 桌面]# pvcreate /dev/sdb1
  Physical volume "/dev/sdb1" successfully created
[root@you 桌面]# pvdisplay
  --- Physical volume ---
  PV Name               /dev/sda2
  VG Name               vg_you
  PV Size               19.51 GiB / not usable 3.00 MiB
  Allocatable           yes (but full)
  PE Size               4.00 MiB
  Total PE              4994
  Free PE               0
  Allocated PE          4994
  PV UUID               1xYCsO-8Ch0-aFGl-0bzh-pfvM-VTVC-uekyMh
   
  "/dev/sdb1" is a new physical volume of "15.00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdb1
  VG Name               
  PV Size               15.00 GiB
  Allocatable           NO
  PE Size               0   
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               HhEaL2-IZfZ-5dKy-1ue1-NEhI-KV5G-Yo3x1L
```

4.扩展卷组（vg）空间

``` shell
# 查看当前卷组
[root@you 桌面]# vgdisplay
  --- Volume group ---
  VG Name               vg_you
  System ID             
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               19.51 GiB
  PE Size               4.00 MiB
  Total PE              4994
  Alloc PE / Size       4994 / 19.51 GiB
  Free  PE / Size       0 / 0   
  VG UUID               IMx06H-EeOV-ZmOO-Myhe-Snlg-q5WO-Au6YOv

# 扩展卷组vg_you
[root@you 桌面]# vgextend vg_you /dev/sdb1
  Volume group "vg_you" successfully extended
[root@you 桌面]# vgdisplay
  --- Volume group ---
  VG Name               vg_you
  System ID             
  Format                lvm2
  Metadata Areas        2
  Metadata Sequence No  4
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                2
  Act PV                2
  VG Size               34.50 GiB
  PE Size               4.00 MiB
  Total PE              8833
  Alloc PE / Size       4994 / 19.51 GiB
  Free  PE / Size       3839 / 15.00 GiB
  VG UUID               IMx06H-EeOV-ZmOO-Myhe-Snlg-q5WO-Au6YOv
```
对比vgdisplay输出的信息，可以看到 **VG Size               34.50 GiB** 大小发生了改变

5.扩展逻辑卷
``` shell
[root@you 桌面]# lvdisplay
  --- Logical volume ---
  LV Path                /dev/vg_you/lv_root
  LV Name                lv_root
  VG Name                vg_you
  LV UUID                AJdx2o-BwXD-4p2t-wvzA-3RGB-dzQP-KimmOx
  LV Write Access        read/write
  LV Creation host, time you, 2016-08-01 04:21:33 +0800
  LV Status              available
  # open                 1
  LV Size                17.51 GiB
  Current LE             4482
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:0
   
  --- Logical volume ---
  LV Path                /dev/vg_you/lv_swap
  LV Name                lv_swap
  VG Name                vg_you
  LV UUID                n6YVEB-AV6w-69rO-0Owe-89k9-ztTe-dMnP7q
  LV Write Access        read/write
  LV Creation host, time you, 2016-08-01 04:21:46 +0800
  LV Status              available
  # open                 1
  LV Size                2.00 GiB
  Current LE             512
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:1

# 扩展逻辑卷
[root@you 桌面]# lvextend /dev/vg_you/lv_root /dev/sdb1
  Size of logical volume vg_you/lv_root changed from 17.51 GiB (4482 extents) to 32.50 GiB (8321 extents).
  Logical volume lv_root successfully resized

# 扩展后
[root@you 桌面]# lvdisplay
  --- Logical volume ---
  LV Path                /dev/vg_you/lv_root
  LV Name                lv_root
  VG Name                vg_you
  LV UUID                AJdx2o-BwXD-4p2t-wvzA-3RGB-dzQP-KimmOx
  LV Write Access        read/write
  LV Creation host, time you, 2016-08-01 04:21:33 +0800
  LV Status              available
  # open                 1
  LV Size                32.50 GiB
  Current LE             8321
  Segments               2
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:0
   
  --- Logical volume ---
  LV Path                /dev/vg_you/lv_swap
  LV Name                lv_swap
  VG Name                vg_you
  LV UUID                n6YVEB-AV6w-69rO-0Owe-89k9-ztTe-dMnP7q
  LV Write Access        read/write
  LV Creation host, time you, 2016-08-01 04:21:46 +0800
  LV Status              available
  # open                 1
  LV Size                2.00 GiB
  Current LE             512
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           253:1
```
6.扩容分区
``` shell
[root@you 桌面]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/vg_you-lv_root
                       18G  3.5G   13G  22% /
tmpfs                 935M  528K  935M   1% /dev/shm
/dev/sda1             477M   34M  419M   8% /boot

[root@you 桌面]# resize2fs /dev/vg_you/lv_root 
resize2fs 1.41.12 (17-May-2010)
Filesystem at /dev/vg_you/lv_root is mounted on /; on-line resizing required
old desc_blocks = 2, new_desc_blocks = 3
Performing an on-line resize of /dev/vg_you/lv_root to 8520704 (4k) blocks.
The filesystem on /dev/vg_you/lv_root is now 8520704 blocks long.

[root@you 桌面]# df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/vg_you-lv_root
                       32G  3.5G   27G  12% /
tmpfs                 935M  528K  935M   1% /dev/shm
/dev/sda1             477M   34M  419M   8% /boot
```
（完）