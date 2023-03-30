# 如何使用git

# 介绍

Git是一个分布式版本控制系统，广泛用于协同开发和管理代码。它可以记录代码的版本历史，轻松地回滚到之前的版本，以及合并来自不同开发者的修改。使用Git，开发团队可以更好地协同工作，同时最大程度地减少代码冲突和数据丢失的风险。

Git的工作流程基于本地仓库和远程仓库的概念。每个开发者都有一个本地仓库，可以在本地对代码进行修改、提交和查看历史记录等操作。远程仓库则用于团队成员之间的代码共享和合作，可以在远程仓库中查看最新的代码、推送自己的修改、拉取他人的修改等。

Git的核心概念包括提交（commit）、分支（branch）和合并（merge）。提交用于保存代码的一个版本，它包含了修改的内容以及对应的提交信息。分支是指开发者在代码库中创建的一个独立的工作线，可以独立开发和提交代码。合并则是将一个分支的修改合并到另一个分支中，保留不同分支上的不同修改。

# 安装和配置Git

## Linux 平台上安装

Git 的工作需要调用 curl，zlib，openssl，expat，libiconv 等库的代码，所以需要先安装这些依赖工具。

在有 yum 的系统上（比如 Fedora）或者有 apt-get 的系统上（比如 Debian 体系），可以用下面的命令安装：

各 Linux 系统可以使用其安装包管理工具（apt-get、yum 等）进行安装：

### Debian/Ubuntu

Debian/Ubuntu Git 安装命令为：

```shell
$ apt-get install libcurl4-gnutls-dev libexpat1-dev gettext \
  libz-dev libssl-dev

$ apt-get install git

$ git --version
git version 1.8.1.2
```

### Centos/RedHat

如果你使用的系统是 Centos/RedHat 安装命令为：

```shell
$ yum install curl-devel expat-devel gettext-devel \
  openssl-devel zlib-devel

$ yum -y install git-core

$ git --version
git version 1.7.1
```

### 源码安装

我们也可以在官网下载源码包来安装，最新源码包下载地址：https://git-scm.com/download

安装指定系统的依赖包：

```shell
########## Centos/RedHat ##########
$ yum install curl-devel expat-devel gettext-devel \
  openssl-devel zlib-devel

########## Debian/Ubuntu ##########
$ apt-get install libcurl4-gnutls-dev libexpat1-dev gettext \
  libz-dev libssl-dev
```

解压安装下载的源码包：

```shell
$ tar -zxf git-1.7.2.2.tar.gz
$ cd git-1.7.2.2
$ make prefix=/usr/local all
$ sudo make prefix=/usr/local install
```

------

## Windows 平台上安装

在 Windows 平台上安装 Git 同样轻松，有个叫做 msysGit 的项目提供了安装包，可以到 GitHub 的页面上下载 exe 安装文件并运行：

安装包下载地址：https://gitforwindows.org/

官网慢，可以用国内的镜像：https://npm.taobao.org/mirrors/git-for-windows/。

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/20140127131250906.png)

完成安装之后，就可以使用命令行的 git 工具（已经自带了 ssh 客户端）了，另外还有一个图形界面的 Git 项目管理工具。

在开始菜单里找到"Git"->"Git Bash"，会弹出 Git 命令窗口，你可以在该窗口进行 Git 操作。

------

## Mac 平台上安装

在 Mac 平台上安装 Git 最容易的当属使用图形化的 Git 安装工具，下载地址为：

http://sourceforge.net/projects/git-osx-installer/

安装界面如下所示：



![18333fig0107-tn](assets/18333fig0107-tn.png)

# Git 工作流程

本章节我们将为大家介绍 Git 的工作流程。

一般工作流程如下：

- 克隆 Git 资源作为工作目录。
- 在克隆的资源上添加或修改文件。
- 如果其他人修改了，你可以更新资源。
- 在提交前查看修改。
- 提交修改。
- 在修改完成后，如果发现错误，可以撤回提交并再次修改并提交。

下图展示了 Git 的工作流程：

![image-20230807195909124](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20230807195909124.png)

# Git工作区、暂存区和版本库

在使用Git之前必须了解一下概念：

- 工作区：就是你电脑上能看见的目录
- 暂存区：一般存放在.git文件夹下面的index文件中，所以我们也把暂存区叫做索引。
- 版本库：工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库

下面这个图展示了工作区、版本库中的暂存区和版本库之间的关系：

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/1352126739_7909.jpg)

- 图中左侧为工作区，右侧为版本库。在版本库中标记为 "index" 的区域是暂存区（stage/index），标记为 "master" 的是 master 分支所代表的目录树。
- 图中我们可以看出此时 "HEAD" 实际是指向 master 分支的一个"游标"。所以图示的命令中出现 HEAD 的地方可以用 master 来替换。
- 图中的 objects 标识的区域为 Git 的对象库，实际位于 ".git/objects" 目录下，里面包含了创建的各种对象及内容。
- 当对工作区修改（或新增）的文件执行 **git add** 命令时，暂存区的目录树被更新，同时工作区修改（或新增）的文件内容被写入到对象库中的一个新的对象中，而该对象的ID被记录在暂存区的文件索引中。
- 当执行提交操作（git commit）时，暂存区的目录树写到版本库（对象库）中，master 分支会做相应的更新。即 master 指向的目录树就是提交时暂存区的目录树。
- 当执行 **git reset HEAD** 命令时，暂存区的目录树会被重写，被 master 分支指向的目录树所替换，但是工作区不受影响。
- 当执行 **git rm --cached <file>** 命令时，会直接从暂存区删除文件，工作区则不做出改变。
- 当执行 **git checkout .** 或者 **git checkout -- <file>** 命令时，会用暂存区全部或指定的文件替换工作区的文件。这个操作很危险，会清除工作区中未添加到暂存区中的改动。
- 当执行 **git checkout HEAD .** 或者 **git checkout HEAD <file>** 命令时，会用 HEAD 指向的 master 分支中的全部或者部分文件替换暂存区和以及工作区中的文件。这个命令也是极具危险性的，因为不但会清除工作区中未提交的改动，也会清除暂存区中未提交的改动。

# 创建仓库

Git 使用 **git init** 命令来初始化一个 Git 仓库，Git 的很多命令都需要在 Git 的仓库中运行，所以 **git init** 是使用 Git 的第一个命令。

在执行完成 **git init** 命令后，Git 仓库会生成一个 .git 目录，该目录包含了资源的所有元数据，其他的项目目录保持不变。

### 使用方法

使用当前目录作为 Git 仓库，我们只需使它初始化。

```shell
git init
```

该命令执行完后会在当前目录生成一个 .git 目录。

使用我们指定目录作为Git仓库。

```shell
git init newrepo
```

初始化后，会在 newrepo 目录下会出现一个名为 .git 的目录，所有 Git 需要的数据和资源都存放在这个目录中。

如果当前目录下有几个文件想要纳入版本控制，需要先用 git add 命令告诉 Git 开始对这些文件进行跟踪，然后提交：

```shell
$ git add *.c
$ git add README
$ git commit -m '初始化项目版本'
```

以上命令将目录下以 .c 结尾及 README 文件提交到仓库中。

> **注：** 在 Linux 系统中，commit 信息使用单引号 **'**，Windows 系统，commit 信息使用双引号 **"**。
>
> 所以在 git bash 中 **git commit -m '提交说明'** 这样是可以的，在 Windows 命令行中就要使用双引号 **git commit -m "提交说明"**。

# git clone

我们使用 **git clone** 从现有 Git 仓库中拷贝项目（类似 **svn checkout**）。

克隆仓库的命令格式为：

```shell
git clone <repo>
```

如果我们需要克隆到指定的目录，可以使用以下命令格式：

```shell
git clone <repo> <directory>
```

**参数说明：**

- **repo:**Git 仓库。
- **directory:**本地目录。

比如，要克隆 Ruby 语言的 Git 代码仓库 Grit，可以用下面的命令：

```shell
$ git clone https://github.com/schacon/grit.git
```

执行该命令后，会在当前目录下创建一个名为grit的目录，其中包含一个 .git 的目录，用于保存下载下来的所有版本记录。

如果要自己定义要新建的项目目录名称，可以在上面的命令末尾指定新的名字：

```shell
$ git clone https://github.com/schacon/grit.git mygrit
```

# 配置

git 的设置使用 **git config** 命令。

显示当前的 git 配置信息：

```shell
$ git config --list
credential.helper=osxkeychain
core.repositoryformatversion=0
core.filemode=true
core.bare=false
core.logallrefupdates=true
core.ignorecase=true
core.precomposeunicode=true
```

编辑 git 配置文件:

```shell
$ git config -e    # 针对当前仓库 
```

或者：

```shell
$ git config -e --global   # 针对系统上所有仓库
```

设置提交代码时的用户信息：

```shell
$ git config --global user.name "runoob"
$ git config --global user.email test@runoob.com
```

如果去掉 **--global** 参数只对当前仓库有效。

***注意：使用之前先配置用户信息***

# Git基本操作

在介绍基本操作之前，我们先来一张图。

![img](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/git-command.jpg)

**说明：**

- workspace：工作区
- staging area：暂存区/缓存区
- local repository：版本库或本地仓库
- remote repository：远程仓库

本地仓库和远程仓库是 Git 中的两个概念，用于管理和存储代码的版本控制系统。

* 本地仓库（Local Repository）：本地仓库是指位于你的本地计算机上的存储库，用于保存你的代码版本历史和相关的元数据。当你在本地计算机上使用 Git 进行版本控制时，所有的代码和提交历史都保存在本地仓库中。你可以在本地仓库中执行各种 Git 操作，如创建分支、提交更改、合并分支等。本地仓库通常位于你的项目目录中的 `.git` 文件夹中。

* 远程仓库（Remote Repository）：远程仓库是指位于远程服务器上的存储库，用于与团队成员共享代码和协作开发。远程仓库可以托管在各种代码托管平台，如 GitHub、GitLab、Bitbucket 等。通过将本地仓库与远程仓库进行关联，你可以将本地的代码推送（push）到远程仓库，或者从远程仓库拉取（pull）最新的代码到本地仓库。这样可以方便团队成员之间的协作和代码共享。

本地仓库和远程仓库之间可以进行数据的传输和同步。你可以将本地仓库的更改推送到远程仓库，使其他人能够看到和获取你的代码更新。同时，你也可以从远程仓库拉取最新的代码到本地仓库，使你的本地仓库保持与远程仓库同步。

总结起来，本地仓库是位于本地计算机上的存储库，用于保存代码版本历史，而远程仓库是位于远程服务器上的存储库，用于与团队共享代码和协作开发。通过将本地仓库与远程仓库进行关联，可以实现代码的传输、同步和协同工作。

## 创建仓库命令

| 命令        | 说明                                   |
| :---------- | :------------------------------------- |
| `git init`  | 初始化仓库                             |
| `git clone` | 拷贝一份远程仓库，也就是下载一个项目。 |

## 提交与修改

Git 的工作就是创建和保存你的项目的快照及与之后的快照进行对比。

下表列出了有关创建与提交你的项目的快照的命令：

| 命令         | 说明                                     |
| :----------- | :--------------------------------------- |
| `git add`    | 添加文件到暂存区                         |
| `git status` | 查看仓库当前的状态，显示有变更的文件。   |
| `git diff`   | 比较文件的不同，即暂存区和工作区的差异。 |
| `git commit` | 提交暂存区到本地仓库。                   |
| `git reset`  | 回退版本。                               |
| `git rm`     | 将文件从暂存区和工作区中删除。           |
| `git mv`     | 移动或重命名工作区文件。                 |

### 提交日志

| 命令               | 说明                                 |
| :----------------- | :----------------------------------- |
| `git log`          | 查看历史提交记录                     |
| `git blame <file>` | 以列表形式查看指定文件的历史修改记录 |

### 远程操作

| 命令         | 说明               |
| :----------- | :----------------- |
| `git remote` | 远程仓库操作       |
| `git fetch`  | 从远程获取代码库   |
| `git pull`   | 下载远程代码并合并 |
| `git push`   | 上传远程代码并合并 |

这些命令的使用可以在[菜鸟教程](https://www.runoob.com/git/git-basic-operations.html)查看

# 分支管理

分支管理请看[菜鸟教程](https://www.runoob.com/git/git-branch.html)

# Git 标签

Git标签请看[菜鸟教程](https://www.runoob.com/git/git-tag.html)