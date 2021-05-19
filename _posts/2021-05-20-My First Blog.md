---
layout: post
title:  "My first Blog?"
author: Li Jinzhao
categories: [ Jekyll ]
image: assets/images/home.jpg
tags: [sticky]
---

<head>
    <script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
    <script type="text/x-mathjax-config">
        MathJax.Hub.Config({
            tex2jax: {
            skipTags: ['script', 'noscript', 'style', 'textarea', 'pre'],
            inlineMath: [['$','$']]
            }
        });
    </script>
    <link href="/assets/css/rouge.css" rel="stylesheet"/>
</head>
仆从以前到现在配置 (折腾) 网页の完全纪录... ...

下载 `nodejs`

https://nodejs.org/en/download/

下载 `cnpm`

```shell
## 检查 node 是否已经安装成功, 版本号
node -v
## 检查 npm 是否已经安装成功, 版本号
npm -v
## 下载 cnpm
npm install -g cnpm --registry=https://registry.npm.taobao.org
## 检查 cnpm 是否已经安装成功, 版本号
cnpm -v
```

使用 `cnpm` 下载 `hexo`

```shell
cnpm install -g hexo-cli
## 检查 hexo 是否已经安装成功, 版本号
hexo -v
```

下载 `git`

https://git-scm.com/download/win

添加系统 Path 环境变量

```bash
F:\Po_D\Git_protable\cmd
F:\Po_D\Git_protable\bin
```

切换到博客目录

```shell
cd desktop\optics_css_blog
```

生成基础

```shell
hexo init
```

打开 (启动) 博客

```shell
hexo s
```

创建一篇文章

```shell
hexo n "my first blog..."
```

下载工具

```shell
cnpm install --save hexo-deployer-git
```

设置 `_config.yml` 中的 `# deployment`

```yaml
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
type: 'git'
repo: https://github.com/Opticcss/Opticcss.github.io # 仓库地址
branch: main
```





```shell
git config --global user.name "Opticcss"
git config --global user.email "optics_css@foxmail.com"
ssh-keygen
## 下面是密钥生成时的过程和结果
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\a1020/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\a1020/.ssh/id_rsa.
Your public key has been saved in C:\Users\a1020/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:RQFv06DIMcHZBklYjuTF6iBtNygvvy2qx4V7IYDRgEU a1020@LAPTOP-3SQ4QCQT
The key's randomart image is:
+---[RSA 3072]----+
|o=E .*O*..+.     |
|o .o.==+o+ o     |
|.o .o.+.. = .    |
|= = +    o .     |
| * = .  S        |
|. + +            |
| + + .           |
|  *..            |
|+o.+.            |
+----[SHA256]-----+
```

在 `SSH` 中 `add` 一下 `rsa-key`，

运行

```shell
sh ./install.sh git_ssh
```

下载 `Deploy-script` 插件

```shell
sh ./install.sh deploy
```

部署本地到远端

```shell
sh ./up.sh
```







> 更新一下配置
>
> ```shell
> npm un hexo-deployer-git
> npm i hexojs/hexo-deployer-git
> ```
>
> 部署到远端，其中的 `d` 就是 `deploy`
>
> ```shell
> ## 清理已生成文件
> hexo clean
> ## hexo 生成
> hexo g
> ## 部署 hexo 到远端
> hexo d
> ```

下载 `hexo-script`

```shell
curl -O https://cdn.jsdelivr.net/gh/kjhuanhao/hexo-script@master/install.sh
```



---

```shell
cd	显示当前目录的名称或将其更改
print	打印文本文件
mkdir	创建目录
if	执行批处理程序中的条件性处理
exit	退出 CMD.EXE 程序(命令解释程序)
dir	显示一个目录中的文件和子目录
attrib	显示或更改文件属性
calc	启动计算器
cls	清除屏幕
cmd	打开另一个 Windows 命令解释程序窗口
```