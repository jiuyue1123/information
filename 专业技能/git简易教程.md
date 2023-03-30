Git的工作流程包括以下几个主要步骤：

1. 初始化仓库：在开始使用Git之前，需要在项目目录中初始化一个Git仓库。可以通过命令`git init`来创建一个新的仓库，或者使用`git clone`从远程仓库克隆一个已有的仓库到本地。

2. 添加文件：将项目中的文件添加到Git仓库中，以便进行版本控制。使用`git add`命令将文件添加到暂存区。

3. 提交更改：使用`git commit`命令将暂存区中的文件提交到本地仓库。每次提交都会生成一个唯一的提交记录，包含了提交者的信息、提交时间以及提交的文件变动。

4. 分支管理：Git使用分支来支持并行开发和多个版本的管理。可以使用`git branch`命令创建、切换和删除分支。使用`git merge`命令将不同分支的代码合并到一起。

5. 远程操作：Git可以与远程仓库进行交互，方便团队协作和代码共享。使用`git remote`命令添加、删除和管理远程仓库。使用`git push`命令将本地仓库的修改推送到远程仓库，使用`git pull`命令从远程仓库获取最新的修改。

6. 解决冲突：当多个人同时修改同一个文件时，可能会产生冲突。Git提供了工具和命令来解决冲突，例如`git diff`用于查看文件差异，`git merge`用于合并分支，`git rebase`用于变基操作等。

7. 版本回退：Git允许回退到之前的任意版本，以便恢复代码或查看历史记录。使用`git log`命令查看提交记录，使用`git reset`命令回退到指定的版本。

8. 标签管理：Git支持给特定的提交打上标签，以便于版本标识和发布。使用`git tag`命令创建、查看和删除标签。

以上是Git的基本工作流程，通过这些步骤，可以实现代码的版本控制、团队协作和代码管理。根据具体的项目需求和团队规模，工作流程可能会有所调整和扩展。

本篇为简易教程，所以详细的请看我写的另一篇文章《如何使用git》

# 初始化设置

在第一次使用git之前我们需要做一些设置，具体如下。

## 设置昵称和邮箱

在使用 Git 提交代码时，每次提交都会记录提交者的信息，包括昵称和邮箱。为了确保提交记录的准确性，需要在 Git 中设置昵称和邮箱。可以使用以下命令进行设置：

```shell
git config --global user.name "Your Name"
git config --global user.email "yourname@example.com"
```

## 配置ssh连接github

为了能够与远程仓库进行安全的通信，特别是连接到 GitHub 上的仓库，可以配置 SSH 密钥。SSH 密钥是一种加密机制，用于验证你的身份并确保安全连接。github要求验证身份才可以使用。

1. 生成密钥对

   使用以下命令来生成密钥对：

   ```shell
   ssh-keygen -t rsa
   ```

   期间会询问一些问题，你可以根据提示进行设置。这里我一路回车

   ![image-20240121113718217](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240121113718217.png)

2. 将公钥添加到github

   公钥生成的位置上面已经告诉你了，这里打开`C:\Users\nanak/.ssh/id_rsa.pub`将里面的内容复制。

   打开github，点击右上角头像，打开设置

   ![image-20240121113943940](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240121113943940.png)

   找到SSH and GPG keys，点击New SSH key的按钮，把公钥粘贴进去。记得设置title。

   ![image-20240121114101274](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240121114101274.png)

   ![image-20240121114237963](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240121114237963.png)

3. 测试

   输入`ssh -T git@github.com`进行验证，如下说明成功

   ![image-20240121114357171](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240121114357171.png)

在使用 Git 进行版本控制时，通常需要进行以下操作：

# 新建远程仓库
如果你还没有在远程仓库托管代码，可以在代码托管平台（如 GitHub、GitLab 等）上创建一个新的远程仓库。在创建仓库时，可以设置仓库的名称、描述等信息。

![image-20240121114749963](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240121114749963.png)

![image-20240121114934929](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240121114934929.png)

# 初始化本地仓库
在本地项目目录中，使用 `git init` 命令初始化一个新的 Git 仓库。这会在项目目录中创建一个 `.git` 目录，用于存储 Git 的版本控制信息。只有在本地存在仓库之后才可以关联远程仓库。

接下来重命名本地分支，这是因为最新版本的本地git分支是master，而github默认是main，我们确保一致。

![image-20240121115917655](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240121115917655.png)

# 关联远程仓库
使用 `git remote add` 命令将本地仓库与远程仓库进行关联。例如，如果你的远程仓库位于 GitHub 上，可以使用以下命令关联：
```
git remote add origin <远程仓库地址>
```
将 `<远程仓库地址>` 替换为你的远程仓库的 URL。

origin是一个别名，这个名字可以自定义但通常使用origin作为远程仓库别名来使用。

# 拉取最新请求
在开始工作之前，可以使用 `git pull` 命令从远程仓库拉取最新的代码和提交。这可以确保你的本地仓库与远程仓库保持同步，避免冲突和代码丢失。

![](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240121115353814.png)

如上图，这时本地仓库刚刚初始化，并没有做任何操作。而远程仓库有一个README.md文件。这时我们需要使用 `git pull` 命令从远程仓库拉取最新的代码和提交。如果不这样做，之后的命令可能会报错（比如`git push`操作）。

有可能会出现如下图的问题。

![image-20240121115651560](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240121115651560.png)

这时我们只需要设置一下从远程仓库pull到main分支。

![image-20240121120016043](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240121120016043.png)

我们也可以，执行 `git branch --set-upstream-to=origin/<branch> main` 命令：根据输出信息，可以执行类似于 `git branch --set-upstream-to=origin/main main` 的命令，将本地仓库的 `main` 分支与远程仓库的 `main` 分支进行关联。这将设置远程仓库的 `main` 分支作为本地仓库 `main` 分支的上游分支，使得后续的 `git pull` 命令可以直接与远程仓库的 `main` 分支进行交互。

![image-20240121120141182](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240121120141182.png)

这样本地文件夹和远程仓库内容一样了。

# 提交请求
在进行代码修改后，可以使用以下步骤将修改提交到远程仓库：

1. 使用 `git add` 命令将修改的文件添加到暂存区。

   我新建了一个名叫test的文件，使用`git add .`添加到暂存区。等待提交

2. 使用 `git commit` 命令将暂存区的文件提交到本地仓库。可以在提交时提供一条有意义的提交消息，描述本次提交的内容。

   `git commit -m "本次提交的消息"`

3. 使用 `git push` 命令将本地仓库的提交推送到远程仓库。这会将你的修改上传到远程仓库，使其可供他人访问和合并。

   ![image-20240121120537009](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240121120537009.png)

   如果出现这样的情况，说明git push并没有成功。这是因为没有设置上游分支导致的。我们使用

   ```shell
   git push -u origin main
   ```

   这个命令将推送当前分支 `main` 到远程仓库，并将远程仓库的 `main` 分支设置为当前分支 `main` 的上游分支。这样，之后你就可以直接使用 `git push` 命令推送代码，而不需要每次都指定远程仓库和分支。

   ![image-20240121120810183](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240121120810183.png)

提交请求后，其他人可以查看你的修改并进行代码审查、合并等操作。

以上是使用 Git 进行版本控制的一般流程。具体的操作可能会根据项目和团队的需求有所调整。





