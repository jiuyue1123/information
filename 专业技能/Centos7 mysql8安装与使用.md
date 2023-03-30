# Centos7 mysql8安装与使用

## 1. CentOS系统概述

### CentOS简介

CentOS (Community Enterprise Operating System) 是一种基于 Red Hat Enterprise Linux (RHEL) 而重新编译的 Linux 发行版，因此，它能为终端用户提供一个与 RHEL 完全相同的免费替代品。CentOS 由社区全球的自愿者积极维护，它在稳定性和可设置性方面都表现出色。

### CentOS版本选择

由于 MySQL 在不同版本的 CentOS 中的安装方式略有不同，因此， 我们需要选择正确的版本进行安装。在本文中，我们将使用 CentOS 7 版本进行演示。

## 2. MySQL简介

### MySQL的历史

MySQL 是一款关系型数据库管理系统，它最初由瑞典的 MySQL AB 公司开发。随后，MySQL AB 公司被 Sun Microsystems（又被 Oracle 收购）收购。MySQL 的特性包括易于使用、高效、可扩展和高可用性，被广泛用于各种大中型网站和企业级应用程序中。

### MySQL的特性和用途

MySQL 数据库支持多种操作系统和编程语言，包括 Linux、Windows 和 macOS，同时支持 PHP、Java、Python 等多种编程语言。它被广泛地用于存储和管理大型数据集，同时提供与 Web 应用程序的快速集成和易于管理的工具。

## 3. MySQL安装

a)   使用wget下载存储库安装包

命令如下

sudo yum install wget && wget https://dev.mysql.com/get/mysql80-community-release-el7-7.noarch.rpm

执行的结果呈下图

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image002.jpg)

b)   使用rpm安装存储库安装包

我们使用wget下载之后在当前目录下面有一个mysql开头的rpm包，我们使用rpm安装。命令是

rpm -ivh 包全名

此命令中各选项的参数的含义为：

l -i：安装（install）

l -v：显示更详细的信息（verbose）

l -h：打印进度（hash）

如下图

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image004.jpg)

c)   检索软件列表

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image006.jpg)

我们发现有mysql-community-server这个是mysql社区版服务端程序，我们安装这个程序。

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image008.jpg)

**注：如果下载很慢的话请看后文中问题解决的部分**

现在mysql就已经安装好了，让我们启动服务，并且设置开机自启

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image010.jpg)

## 4. MySQL的使用

### 进入MySQL命令行工具

MySQL 命令行工具是一种在终端命令行下操作 MySQL 的工具。它提供了一系列的命令和参数来操作数据库，如创建、删除、修改数据库和表，插入、更新、删除数据等等。除此之外，MySQL 命令行工具还可以执行 SQL 脚本和备份数据库。通常情况下，MySQL 命令行工具默认安装在 MySQL 服务的所在机器上，并且可以通过 Terminal （终端）来调用。本工具可以大大方便开发人员和管理人员对 MySQL 数据库进行操作和管理。因此这是我们必须要掌握的工具。命令如图

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image012.jpg)

-u表示选择要使用的用户名，-p表示需要输入密码进入。

问：什么第一次进入就需要密码？可是我们不知道密码。

答：其实在centos8之后的版本之中首次进入是不需要密码的，在centos7中则会生成一份默认密码，它在/var/log/mysqld.log日志文件中。

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image014.jpg)

复制粘贴回车一气呵成

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image016.jpg)

### MySQL更改密码

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '<new_password>';

这句SQL语句的作用是更改MySQL用户‘root’@‘localhost’的身份验证方式为mysql_native_password，并将其密码设置为‘<new_password>’其中‘root’@‘localhost’表示MySQL中的用户和主机。‘root’是用户名，‘localhost‘是主机名。MYSQL允root用户从本地主机访问数据库服务器。IDENTIFIED WITH mysql_native_password用于指定身份验证方式，它将root用户的身份验证方式设置为默认的密码加密方式mysql_native_password。 BY‘<new_password>’用于指定新密码，这里<new_password>需要替换为您要设置的密码。在此之后，MySQL将哈希该密码并存储在mysql.user系统表中。

 

 

**在 MySQL 中，提供了多种身份验证方式用于验证用户身份。以下是 MySQL 支持的一些常见身份验证方式：**

1. **mysql_native_password**：该方法使用Sha1散列函数对密码进行加密，是MySQL的默认身份验证方法。
2. **sha256_password**：该方法使用SHA-256散列函数对密码进行加密，并支持使用密钥的公共密钥算法进行加密。
3. **caching_sha2_password**：该方法使用SHA-256散列函数对密码进行加密，并支持缓存加密数据以提高性能。

如果没有指定身份验证方式，则MySQL将使用默认的身份验证方式 mysql_native_password。

需要注意的是，在 MySQL 5.7.6及以上版本中，默认身份验证插件已更改为 caching_sha2_password。而在 MySQL 8.0.11及以上版本中，则同时支持 caching_sha2_password 和 sha256_password 并成为MySQL 的两个官方身份验证方式，而 mysql_native_password 只用于向后兼容。

因此，如果您在MySQL中创建用户时未指定身份验证方式，则默认使用 mysql_native_password。所以你可以在上面的SQL语句使用ALTER USER 'root'@'localhost' IDENTIFIED BY '<new_password>';

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image018.jpg)

当出现 “ERROR 1819 (HY000): Your password does not satisfy the current policy requirements” 错误消息时，意味着您正在尝试使用不符合 MySQL 密码策略要求的密码对用户进行身份验证。

MySQL 5.7.6及以上版本中默认启用了密码策略，要求密码必须具备以下要求：

1. 密码长度必须超过8个字符。
2. 密码中必须包含大小写字母、数字和特殊字符。
3. 避免使用串行数量较多的相似字符。

 

**设置密码策略**

要设置密码策略可以按照以下步骤进行：

1. 连接到 MySQL：

mysql -u 用户名 -p

2. 使用以下命令查看全局密码策略设置：

SHOW VARIABLES LIKE 'validate_password%';

这将显示当前的密码策略设置。在默认情况下，密码策略 validate_password.policy 被设置为“MEDIUM”级别，并且需要密码长度为 8 个字符以上。

3. 要更改密码策略，可以使用以下命令之一：

l 策略设置为“LOW”级别，这将允许使用任何长度和任何强度的密码：

SET GLOBAL validate_password.policy=LOW;

l 策略设置为“MEDIUM”级别，这将要求密码长度至少为 8 个字符，并具有数字、小写字母、大写字母和特殊字符：

SET GLOBAL validate_password.policy=MEDIUM;

l 策略设置为“STRONG”级别，这将要求密码长度至少为 12 个字符，并具有数字、小写字母、大写字母、特殊字符和完整的字典单词：

SET GLOBAL validate_password.policy=STRONG;

注意：密码策略级别使用大小写敏感的字符串值。如果策略级别值被拼错，命令将不起作用。

**注意：当使用超级用户（如 root 用户）访问 MySQL 时，如果存在用户密码过期的情况，可能会出现此错误消息：ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.**

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image020.jpg) ![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image022.jpg)

### MySQL远程访问

默认情况下，MySQL 只能通过本地主机进行访问，要想从其他主机访问 MySQL 数据库，需要进行一些配置。本文将介绍如何在 CentOS 上配置 MySQL 以允许远程访问

1. 先登录mysql命令行

mysql -uroot -p

2. 使用mysql数据库

use mysql;

 

3. 先查看一下host访问权限(host默认是仅localhost访问权限),设置并刷新权限

select host,user from user; 

\# 允许所有主机，都可以通过用户为root用户，密码为默认数据库登录密码，进行访问数据库

update user set host='%' where user='root';

 

\# 再次查看host访问权限，即可看到允许哪些主机有访问权限等

select host,user from user; 

\# 刷新权限，这一句很重要，使修改生效，如果没有写，则还是不能进行远程连接。这句表示从mysql数据库的grant表中重新加载权限数据，因为MySQL把权限都放在了cache中，所以，做完修改后需要重新加载。

flush privileges;

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/clip_image024.jpg)

4. 使用navicat测试连接，连接成功

好的，以下是 MySQL 权限管理的内容：

## 5. MySQL 权限管理

MySQL 的权限管理可以让您控制用户对数据库服务器和表的访问权限。通过定义用户、密码和访问权限，您可以保护 MySQL 数据库中的敏感数据。本文将介绍如何创建、修改和删除用户，以及授予用户访问数据库和表的权限。

 

1) 创建新用户

使用以下命令创建一个新用户：

CREATE USER '<new_username>'@'<host>' IDENTIFIED BY '<password>';

其中，new_username 是您要创建的新用户名，host 是该用户被允许登录的主机名或 IP 地址，password 是您为该用户设置的密码。

例如，以下命令将创建一个名为 john 的用户，该用户的密码为 123456，并允许该用户从任何主机连接到 MySQL 服务器：

CREATE USER 'john'@'%' IDENTIFIED BY '123456';

2) 移除用户

使用以下命令删除一个用户：

DROP USER '<existing_username>'@'<host>';

其中，existing_username 是您要删除的已存在的用户名，host 是该用户登录的主机名或 IP 地址。

例如，以下命令将删除具有名为 john 的用户：

DROP USER 'john'@'%';

3) 授予权限

使用以下命令授予用户访问数据库或表的权限：

GRANT <permission> ON <database_name>.<table_name> TO '<existing_username>'@'<host>';

其中，permission 是用户被授予的访问权限，database_name 是您要授予访问权限的数据库名称，table_name 是数据库中您要授予访问权限的表名，existing_username 是一个已存在的用户名，host 是该用户登录的主机名或 IP 地址。

例如，以下命令将授予具有名为 john 的用户在 test 数据库中所有表的 SELECT 权限：

GRANT SELECT ON test.* TO 'john'@'%';

4) 撤销权限

使用以下命令撤销用户对数据库或表的访问权限：

REVOKE <permission> ON <database_name>.<table_name> FROM '<existing_username>'@'<host>';

其中，permission 是用户被授予的访问权限，database_name 是数据库名称，table_name 是数据库中表的名称，existing_username 是一个已存在的用户名，host 是该用户登录的主机名或 IP