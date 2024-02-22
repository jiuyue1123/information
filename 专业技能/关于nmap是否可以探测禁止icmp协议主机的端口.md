# 关于nmap是否可以探测禁止icmp协议主机的端口

## 摘要

本实验旨在探索利用nmap工具来探测禁止icmp协议的主机的开放端口。icmp协议是一种常用的网络协议，但在某些情况下，由于网络安全策略的限制，主机可能会禁止icmp协议。在这种情况下，使用传统的ping命令可能无法正常工作。因此，本实验将采用nmap工具来绕过icmp协议的限制，尝试探测该类主机的开放端口，以验证nmap在这种场景下的可行性和准确性。

## 实验环境说明

现有装有RHEL8系统的两台主机A和B，ip地址如下：

A（192.168.76.136）

B（43.143.122.96）

其中主机A安装nmap，B安装nginx并开启服务端口开放在80，安装mysql并开启服务端口开放在3306。

## 实验过程

1. 首先使用nmap初始化扫描B主机，记录结果result1。

   ![image-20230803212009992](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20230803212009992.png)

2. B主机启用防火墙策略，设置丢弃icmp包。

   初始化防火墙使其全部开放，然后设置丢弃icmp包的策略。

   ![image-20230803210911012](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20230803210911012.png)

   再使用A主机的nmap扫描B主机。记录结果result2。

   对比result1和result2的扫描结果，分析nmap在探测禁止icmp协议的主机的开放端口时的表现。

   ![image-20230803212338431](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20230803212338431.png)

   对比扫描结果，可以发现在设置了禁止icmp协议的情况下，使用nmap依然能够正确检测到B主机上开放的80和3306端口。这表明nmap在可以不进行ping探测，然后有效地进行端口扫描。

   