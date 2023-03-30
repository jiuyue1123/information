Git作为目前最流行的版本控制工具之一，对于软件开发团队来说是不可或缺的。然而，在国内由于网络环境的特殊性，许多小伙伴可能会遇到无法正常使用Git的问题。本文将介绍一些解决国内小伙伴无法使用Git的方法，帮助他们克服这一困扰。

自己虽然有梯子，可以正常访问github页面，但是在发现“git clone”命令速度特别慢，有时还经常卡掉。本文通过设置git 代理，解决被墙问题。确认你梯子的代理端口号。我是用的是clash，它的端口号默认是7890

![image-20240121122459697](https://nanak-img.oss-cn-beijing.aliyuncs.com/img/image-20240121122459697.png)

Git代理有两种设置方式，分别是全局代理和只对Github代理，建议只对github 代理。

# 全局代理

```bash
#使用http代理 
git config --global http.proxy http://127.0.0.1:7890
git config --global https.proxy https://127.0.0.1:7890
```

# 只对Github代理（推荐）

```bash
git config --global http.https://github.com.proxy https://127.0.0.1:7890
```

取消代理
`git config --global --unset http.proxy git config --global --unset https.proxy`
