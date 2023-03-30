# 揭秘LVM：革新磁盘管理

## 引言

### 什么是LVM？

LVM(Logical Volume Manager)逻辑卷管理是在Linux2.4内核以上实现的磁盘管理技术。它是**Linux环境下对磁盘分区进行管理的一种机制**。现在不仅仅是Linux系统上可以使用LVM这种磁盘管理机制，对于其它的类UNIX操作系统，以及windows操作系统都有类似与LVM这种磁盘管理软件。

LVM的作用和优势：

* 灵活性：

​		LVM提供了对磁盘和卷的灵活管理。它允许将多个物理硬盘（物理卷）组合成逻辑卷组，并从逻辑卷组中创建逻辑卷，从而更灵活地分配和扩展磁盘空间。

* 容量管理：

  LVM使磁盘容量的管理更方便。可以随时添加、删除或调整逻辑卷的大小，无需复杂的文件系统调整或重新分区，极大地简化了容量管理过程。

* 容错性：

​		LVM提供了数据冗余和备份的功能。通过使用LVM的高级功能，如镜像和快照，可以设置数据的镜像副本或创建快照，以保护数据免受硬件故障或意外删除的影响。

* 管理效率：

​		LVM简化了磁盘管理任务。提供了命令行和图形化工具，轻松创建、删除、移动和调整逻辑卷，使管理员能够更高效地管理磁盘空间，节省时间和精力。

* 虚拟化支持：

​		LVM对虚拟化环境非常有用。可以与虚拟化技术（如KVM或Xen）结合使用，为虚拟机提供灵活的存储管理和动态扩容能力。

## 相关概念

- **Physical Volume(PV)**

**物理卷**，将实际的磁盘分区（partition）系统识别码（system ID）修改为8e后，在通过pvcreate指令转化为LVM最底层的**物理卷**，作为后续空间管理的基础。

- **Volume Group(VG）**

**卷组**，将数个PV进行整合，即形成了VG，在32位的操作系统中，LV的大小与PE的大小有关；在64位的操作系统中，LV几乎没有容量限制。

- **Physical Extent(PE)**

**物理区块**，他是LVM中的最小存储单元。PE类似于文件系统中的block。

- **Logical Volume(LV)**

**逻辑卷**，由VG划分而来，LV的大小与PE的大小及PE的数量有关，Size（LV）= Count（PE）* Size（PE）

## 设置和配置LVM

### 配置虚拟机环境添加一块虚拟磁盘

在VMware里面选中虚拟机设置，添加、选择硬盘。然后按照提示添加完。

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image002.jpg)

### 安装LVM并创建物理卷

- 首先，安装LVM软件包。在大多数Linux发行版中，可以使用包管理器安装LVM软件包。例如，使用以下命令在centos上安装LVM：
       ```sudo yum install lvm2```
- 确认要创建的物理卷的物理分区使用lsblk命令查看

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image004.jpg)

这里使用sdb作为物理卷

- 然后，使用pvcreate命令创建物理卷。例如，将磁盘/dev/sdb分配为物理卷：
      ``` sudo pvcreate /dev/sdb```

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image005.png)

### 创建卷组和逻辑卷

- 使用vgcreate命令创建卷组。例如，创建名为myvg的卷组，并将物理卷/dev/sdb添加到该卷组：
      ``` sudo vgcreate myvg /dev/sdb```

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image006.png)

- 接下来，使用lvcreate命令在卷组myvg上创建逻辑卷。例如，创建一个大小为1GB、名为mylv的逻辑卷：
       ```sudo lvcreate -L 1G -n mylv myvg```

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image007.png)

输入xxdisplay可以查看信息，xx替换成你需要查看的内容的缩写，比如查看PV信息，pvdisplay。

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image009.jpg)

### 格式化成ext4文件格式

```sudo mkfs.ext4 /dev/myvg/mylv```![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image011.jpg)

### 扩展和缩减逻辑卷

- 使用lvextend命令扩展逻辑卷的大小。例如，将mylv逻辑卷的大小增加2GB：
       ```sudo lvextend -L +2G /dev/myvg/mylv```

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image013.jpg)

- 使用resize2fs命令调整文件系统的大小以适应扩展后的逻辑卷。例如，如果逻辑卷上运行的是ext4文件系统，则使用下面的命令：
     ```  sudo resize2fs /dev/myvg/mylv```

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image014.png)

我之前调整过了，所以提示什么都没做

- 使用lvreduce命令缩减逻辑卷的大小。例如，将mylv逻辑卷的大小缩减到1GB：
      ``` sudo lvreduce -L 1G /dev/myvg/mylv```

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image016.jpg)

因为缩减逻辑卷大小可能会出现问题，所以需要确定

sudo resize2fs /dev/myvg/mylv

经过测试文件系统损坏了、需要重新制作文件系统

```sudo mkfs.ext4 /dev/myvg/mylv```

## 迁移逻辑卷

在某些情况下，可能需要将逻辑卷从一个物理卷迁移到另一个物理卷。这可以是由于存储设备的故障、容量不足或者需要重新平衡存储等原因。

以下是一些迁移逻辑卷的常用方法：

使用pvmove命令：pvmove命令可以将逻辑卷上的数据在不中断系统运行的情况下迁移到另一个物理卷上。这个命令会以增量方式复制数据，并在复制完成后自动更新逻辑卷的元数据，使其指向新的物理卷。

```pvmove /dev/source_pv /dev/destination_pv```

这个命令会将逻辑卷上的数据从source_pv物理卷迁移到destination_pv物理卷。

使用vgmove命令：vgmove命令可以将整个卷组迁移到另一个物理卷上。这个命令会将卷组中的所有逻辑卷数据复制到目标物理卷上，并更新卷组的元数据。

```vgmove /dev/source_vg /dev/destination_pv```

这个命令会将source_vg卷组中的所有逻辑卷数据迁移到destination_pv物理卷上。 

使用lvmove命令迁移逻辑卷。例如，将mylv逻辑卷从myvg卷组迁移到newvg卷组：
 ```sudo lvmove /dev/myvg/mylv /dev/newvg/```

 