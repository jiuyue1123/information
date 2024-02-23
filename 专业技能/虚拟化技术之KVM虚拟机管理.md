# 虚拟化技术之KVM虚拟机管理

## 引言

想象一下，你置身于一个充满无限可能的世界，你可以在同一台计算机上运行多个独立的操作系统，仿佛一台计算机就是一个完整的宇宙。这就是虚拟化技术的魔力！虚拟化技术通过将物理服务器划分成多个虚拟机，将计算资源有效地利用起来，提供了灵活性、可伸缩性和资源隔离的优势。

在众多虚拟化解决方案中，KVM（Kernel-based Virtual Machine）脱颖而出。作为一种基于内核的虚拟化技术，KVM将Linux操作系统转变为一个强大的虚拟化平台，为我们带来了探索和创造的新机会。无论是构建开发环境、部署服务器还是进行网络实验，KVM都是我们的得力助手。

注意：本文最后有一个常见问题，可以自助解决问题

## KVM概述：操作系统的妙手

在纷繁复杂的虚拟化技术中，KVM以其高性能、稳定性和开放性而备受推崇。它利用硬件虚拟化扩展（如Intel的VT-x和AMD的AMD-V）来实现虚拟化，让虚拟机能够直接访问硬件资源，同时提供了强大的管理工具和API，使我们能够轻松地管理和控制虚拟机。

KVM不仅仅是一个简单的虚拟化平台，它也是一个完整的生态系统。众多的开源软件（如QEMU）与KVM相结合，提供了丰富的功能和灵活性。无论是创建虚拟机、安装操作系统还是管理资源，KVM都能够让我们事半功倍。

现在，让我们带上探险的帽子，一起深入探索KVM虚拟机管理的奇妙冒险吧！对于那些渴望发挥想象力、发现未知领域的人来说，KVM将成为我们重新定义现实世界的妙手。

让我们开始我们的KVM虚拟机管理之旅吧，从安装KVM开始，探索无限可能的虚拟世界！

## KVM虚拟机管理

在KVM虚拟机管理中，我们可以轻松地创建、配置和管理虚拟机，监视和优化资源的使用，实现虚拟机的迁移和调整。无论是个人使用还是企业级应用，KVM都为我们提供了一个强大而灵活的虚拟化平台。

### 从安装KVM开始

在安装KVM之前，我们需要确保计算机的硬件支持虚拟化技术。通常，现代的处理器都具备硬件虚拟化扩展功能，如Intel的VT-x或AMD的AMD-V。您可以通过 BIOS/UEFI设置或CPU信息查看工具来确认是否启用了硬件虚拟化扩展功能。

在Linux中我们可以检查egrep -i '(vmx|svm|lm)' /proc/cpuinfo来确定是否支持虚拟化技术，当结果中前二者有其一，第三个必有时，则已确定 Linux 主机的硬件支持虚拟化。

![image-20240222194640674](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222194640674.png) 

如果想在虚拟机下进行实验，那么请确保您开启支持。在虚拟机关机的情况下，开启虚拟化的支持。

 ![image-20240222194651642](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222194651642.png)

#### KVM组件

KVM（Kernel-based Virtual Machine）是一种基于内核的虚拟化技术，它由一组关键组件组成，用于实现创建、管理和执行虚拟机的功能。这些组件包括KVM内核模块、QEMU虚拟机模拟器、libvirt和virt-manager等工具。下面来依次介绍这些组件。

1. KVM内核模块：KVM的核心组件是一个内核模块，它嵌入在Linux内核中。这个内核模块提供了虚拟化的基本功能，包括创建和管理虚拟机，虚拟设备的模拟和执行虚拟机指令等。
2. QEMU（Quick Emulator）：QEMU是一个用于模拟和执行虚拟机的开源软件。KVM利用QEMU作为虚拟机监控器（VMM），用于管理虚拟设备和执行虚拟机指令。QEMU提供了广泛的设备模拟支持，使虚拟机可以访问和使用不同类型的硬件设备。
3. libvirt：libvirt是一个用于管理虚拟化平台的开源工具集。它提供了一组API和命令行工具，用于创建、配置和管理虚拟机。libvirt支持多种虚拟化技术，包括KVM、Xen、QEMU等，使管理虚拟机变得更加统一和便捷。
4. virt-manager：virt-manager是一个图形化的管理工具，用于管理KVM虚拟化环境。它提供了一个直观的界面，使用户可以轻松地创建、配置和监视虚拟机。virt-manager集成了libvirt，为用户提供了强大的虚拟机管理功能。
5. virsh：virsh是libvirt的命令行界面工具，可以通过命令行进行虚拟机的管理和操作。通过virsh命令，用户可以创建、启动、停止、迁移虚拟机，以及查看和修改虚拟机的配置等。

在KVM中，KVM内核模块是核心组件，它嵌入在Linux内核中，实现虚拟机的创建和管理。QEMU虚拟机模拟器则充当了虚拟机监控器（VMM），负责模拟和执行虚拟机指令。libvirt是一个用于管理虚拟化平台的工具集，提供了API和命令行工具，用于创建和配置虚拟机。

而virt-manager是一个图形化的虚拟机管理工具，它提供了简便易用的界面，帮助用户轻松创建、配置和监视虚拟机。Virsh是命令行界面工具，相比virt-manager，virsh非常灵活和强大，适用于有经验的管理员和需要自动化管理的场景。通过脚本和批处理命令，用户可以批量操作虚拟机，实现自动化管理。

#### 开始安装

首先安装这些组件，很多系统都已经集成了包管理工具，我们可以使用包管理工具安装。以CentOS7为例。

1. 首先更新软件列表

```sudo yum update```

2. 安装以下软件

qemu-kvm：包含了QEMU虚拟机模拟器和KVM模块。

libvirt：libvirt以及其相关的依赖项。

virt-manager：图形化管理工具。

libvirt-client：其中包含了virsh工具。

tigervnc：VNC软件

qemu-img：qemu-img是QEMU提供的一个命令行工具，用于创建、转换和处理磁盘镜像文件。它可以执行以下操作：

virt-install: virt-install是一个辅助脚本工具，用于帮助在主机操作系统上安装和配置虚拟机。：

```sudo yum install qemu-kvm libvirt virt-client virt-manager qemu-img qemu-install -y```

3. 验证安装

在命令行输入qemu-kvm –version出现版本号说明成功

Virt-manager输入名字就可以启动，像下面这样。

![image-20240222194716529](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222194716529.png)

### 创建虚拟机

#### 虚拟机的名称和配置

首先，为您的虚拟机取一个独特的名称，就像给一个新生命命名一样。您可以选择一个有意义的名字，例如“test”。这将成为您虚拟机的身份标识。

接下来，配置虚拟机的基本参数，例如

- CPU：选择虚拟机的处理器核心数，这将决定虚拟机的计算能力。根据您的需求，您可以分配1个、2个或更多核心。
- 内存：分配给虚拟机的内存大小，以确保它具有足够的资源来执行任务。可以设置为2048 MB或更多，根据您的应用程序要求调整。
- 磁盘：为虚拟机指定一个磁盘，为其提供存储空间。选择合适的大小和类型，例如30 GB的硬盘或SSD，以满足虚拟机的存储需求。

我们使用virt-install来配置这些信息，以下是一些常用的virt-install的参数信息和用法：

- --name：设置虚拟机的名称。

- --ram：指定虚拟机的内存大小（以MB为单位）。

- --vcpus：设置虚拟机的CPU数量。

- --disk：指定存储设备及其属性；格式为--disk /some/storage/path,opt1=val1，opt2=val2等；常用的选项有：  

- - device：设备类型，如cdrom、disk或floppy等，默认为disk；  
  - bus：磁盘总结类型，其值可以为ide、scsi、usb、virtio或xen；  
  - perms：访问权限，如rw、ro或sh（共享的可读写），默认为rw；  
  - size：新建磁盘映像的大小，单位为GB；  
  - cache：缓存模型，其值有none、writethrouth（缓存读）及writeback（缓存读写）；  
  - format：磁盘映像格式，如raw、qcow2、vmdk等；       
  - sparse：磁盘映像使用稀疏格式，即不立即分配指定大小的空间；       
  - --nodisks：不使用本地磁盘，在LiveCD模式中常用；  

注意：如果您在virt-install命令中指定了一个已经存在的虚拟磁盘文件路径，virt-install将使用该现有的虚拟磁盘，而不会自动创建新的虚拟磁盘。如果指定了一个不存在的虚拟磁盘路径则会创建它。

当然你也可以使用qemu-img创建一个disk。

qemu-img是一个用于处理和管理QEMU虚拟磁盘镜像的命令行工具。它提供了多种参数和选项，用于创建、转换、检查、修复以及操作虚拟磁盘镜像文件。

下面是一些常用的qemu-img命令参数和说明：

- create：用于创建新的虚拟磁盘镜像。

- - -f <format> / --format <format>：指定创建的虚拟磁盘镜像的格式，如qcow2、raw、vmdk等。
  - <filename>：指定创建的虚拟磁盘镜像的文件名。
  - <size>：指定创建的虚拟磁盘镜像的大小，可使用多种单位，如10G、10240M等。

- convert：用于转换虚拟磁盘镜像的格式。

- - -f <format> / --format <format>：指定源虚拟磁盘镜像的格式。
  - -O <format> / --output-format <format>：指定转换后的目标虚拟磁盘镜像的格式。
  - <source>：指定源虚拟磁盘镜像的文件名。
  - <destination>：指定目标虚拟磁盘镜像的文件名。

- info：用于显示虚拟磁盘镜像的详细信息。

- - <filename>：指定要显示信息的虚拟磁盘镜像文件名。

- check：用于检查虚拟磁盘镜像的完整性。

- - <filename>：指定要检查的虚拟磁盘镜像文件名。

- repair：用于修复损坏的虚拟磁盘镜像。

- - <filename>：指定要修复的虚拟磁盘镜像文件名。

- resize：用于调整虚拟磁盘镜像的大小。

- - <filename>：指定要调整大小的虚拟磁盘镜像文件名。
  - <size>：指定新的虚拟磁盘镜像大小，可使用多种单位，如10G、10240M等。

这些只是一些常见的qemu-img命令参数，qemu-img还提供其他参数和选项，可以使用qemu-img --help命令查看完整的帮助信息。

现在我们并没有制定镜像，我们先来启动一下试试

![image-20240222194740976](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222194740976.png)

```shell
virt-install --name test --vcpus 1 --ram 1024 –disk \
/home/nanak/disk/test,size=10,format=qcow2 --boot hd
```

由于现在没有安装系统，所以会一直卡在那里，ctrl+c终止它。

### 完善虚拟机

除了设置名称和基础配置之外。你还需要挂载ISO镜像并且完成安装。使用网络，以及连接到虚拟机。

#### 挂载ISO镜像文件

只需要使用virt-install指定--cdrom即可，另外使--os-type指定操作系统类型，--os-variant指定具体的系统变种如centos。另外使用--graphics指定vnc方式显示

```shell
sudo virt-install \

--name <虚拟机名称> \

--ram <内存大小> \

--vcpus <虚拟CPU数量> \

--disk path=<磁盘路径>,size=<磁盘大小> \

--location <ISO镜像路径> \

--network <网络选项> \

--graphics <图形选项> \

--os-type <操作系统类型> \

--os-variant <操作系统变种>`
```



```sudo virt-install --virt-type=kvm --name centos7 --vcpus 1 --ram 1024 --disk path=/data/disk/vm.qcow2,size=10,format=qcow2 --location /data/images/CentOS-7-x86_64-Minimal-2009.iso --boot cdrom --graphics vnc --os-type linux --os-variant=centos7.0```

![image-20240222194811204](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222194811204.png)

![image-20240222194820218](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222194820218.png)

然后你可以用vnc连接并且安装虚拟机，或者使用virt-manager来连接虚拟机

vncviewer :0或者virt-manager

安装完成之后需要设置boot方式为hd（磁盘启动）来使用虚拟机

使用virsh edit 客户机的名字，打开虚拟机配置

![image-20240222194834588](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222194834588.png)

更改如下部分

![image-20240222194848319](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222194848319.png)

为

![image-20240222194859315](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222194859315.png)

保存退出，重新启动虚拟机。成功

```sudo virsh reset <domain-name>强制重启```

![image-20240222194911932](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222194911932.png)

### 存储管理

虚拟机可以挂在多种块存储。为了方便管理不同的块存储，libvirt设计了存储池（storage pool）的概念。常见的存储池包括：目录存储池、文件系统存储池、逻辑卷存储池、iSCSI存储池、RBD存储池、ZFS存储池等。

下面讲一讲逻辑卷存储池

逻辑卷（Logical Volume）是在操作系统层面上创建的一种虚拟磁盘卷，它将物理存储设备（如硬盘驱动器或分区）划分成更灵活、可管理的单元。逻辑卷提供了一种将存储资源进行抽象和管理的方法，使用户可以更加方便地进行存储管理和分配。

逻辑卷组（Logical Volume Group，LVM）是逻辑卷的集合，它是逻辑卷管理器（Logical Volume Manager，LVM）的一个概念。LVM 是一种软件和技术，允许用户在操作系统级别创建、管理和扩展逻辑卷和逻辑卷组。

如果是逻辑卷（lvm）存储池，每个存储池代表一个逻辑卷组（vg），而卷是一个逻辑卷lv

**先创建一个VG**

```sudo vgcreate mtvg /dev/sdb```

 ![image-20240222194950661](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222194950661.png)

**创建一个池**

```sudo virsh pool-define-as guest_images_lvm logical --target centos7 --source-dev /dev/sdb --source-name myvg ```

![image-20240222195003778](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222195003778.png)

这是一个使用 virsh 命令行工具进行虚拟化管理的命令示例，用于定义一个名为 myvg 的存储池（storage pool）。它将该存储池类型设置为 logical（逻辑卷）并关联到逻辑卷。

对于逻辑卷（Logical Volume）存储池的存储方案，原设备可以是卷组（Volume Group，VG）或逻辑卷（Logical Volume，LV）。

- pool-define-as：这是virsh命令的子命令，用于定义一个新的存储池。
- guest_images_lvm：这是新创建的存储池的名称，你可以根据需要选择一个合适的名称。
- logical：这是命令中指定的存储池类型。在这种情况下，使用的是逻辑卷     (logical volume) 存储池。
- --target centos7：此参数指定了存储池的目标。在这里，centos7被指定为存储池的目标名称。
- --source-dev /dev/sdb：这是指定存储池源设备的参数。在这里，/dev/sdb被指定为存储池的源设备。
- --source-name myvg：这是指定存储池源设备名称的参数。在这里，myvg被指定为存储池源设备的名称。

综上所述，该命令的目标是创建一个名为guest_images_lvm的存储池，类型为逻辑卷存储池 (logical volume)，目标名称为centos7，源设备为/dev/sdb，源设备名称为myvg。

**启动成功之后可以输入以下命令查看**

![image-20240222195015221](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222195015221.png)

**创建一个存储块**

```sudo virsh vol-cretae-as guest_images_lvm volume1 8G```

使用sudo virsh vol-list guest_images_lvm可以查询存储块情况，使用lvs可以查看创建的逻辑块。

![image-20240222195035886](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222195035886.png)

![image-20240222195051459](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222195051459.png)

**挂载存储块**

![image-20240222195058713](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222195058713.png)

注意：需要启动客户机之后在挂载否则报错

在客户机查看，成功显示

![image-20240222195108550](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222195108550.png)

**扩容**

```sudo lvresize -L 10G /dev/myvg/volume1```

![image-20240222195118598](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222195118598.png)

然后需要重新设置块大小

```sudo virsh blockresize --path /dev/myvg/volume1 --size 10G centos7```

![image-20240222195123682](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222195123682.png)

![image-20240222195129529](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222195129529.png)

注意：因为我已经输入过了，所以这里提示什么都没有做

### 快照管理

快照就像拍照片一样，将虚拟机当前的状态记录到文件之中，后续可以使用这些文件恢复到拍摄时的状态。对虚拟机进行重大配置更改时、可以通过快照的方式保存当前状态，一遍迅速恢复到之前的状态。

KVM快照按快照对象分为内存快照和磁盘快照；安装快照时，虚拟机状态可以分为离线快照（对关机状态的虚拟机进行快照）和在线快照（对运行虚拟机进行快照）。安装快照的保存方式分为内部快照（保存在镜像文件中）和外部快照（保存到指定的单独文件中）。

下面分别讲解一下，快照的创建、删除、恢复、查询。

#### 内存快照与磁盘快照的区别

1. 内存快照（Memory Snapshot）：
        内存快照是指将当前系统的内存状态保存到一个文件或存储介质中。它包含了操作系统和运行的应用程序在某一时刻的内存内容。内存快照通常用于故障排除、恢复和调试目的。通过保存内存快照，可以在系统崩溃或错误发生时还原系统状态，或者在调试过程中分析内存数据。一些虚拟化平台（如VMware和VirtualBox）提供了内存快照功能。
2. 磁盘快照（Disk Snapshot）：
        磁盘快照是指对硬盘或存储设备的当前状态进行拍摄和保存。磁盘快照通常用于备份和还原数据、复制虚拟机以及提供数据一致性。它可以在特定时间点创建一个虚拟磁盘镜像，记录该时间点上整个磁盘的状态，并且可以随时还原到该时间点的状态。磁盘快照可以减少数据恢复时间、保护系统数据免受意外删除或更改，并提供故障转移和灾难恢复的能力。

需要注意的是，内存快照和磁盘快照是不同的概念，用途和操作也不同。内存快照主要用于保存和还原系统的内存状态，而磁盘快照则用于保存和还原磁盘的状态。两者在故障排除、数据恢复和系统复制等方面都具有重要的作用。

#### 内存快照

输入sudo virsh save centos7 mem.snap

![image-20240222195145513](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222195145513.png)

以下命令可以恢复内存数据

![image-20240222195152017](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240222195152017.png)

注意：在对虚拟机进行内存快照后，虚拟机将处于关闭状态。只能对处于关闭状态的虚拟进行内存快照恢复。内存快照后，如果需要对磁盘进行修改可能会导致系统故障。