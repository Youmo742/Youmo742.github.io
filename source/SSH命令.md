---
title: SSH命令
categories: Linux
date: 2018-03-17 12:50:34
tags:
- SSH
- 远程管理
---

# SSH

Source Shell(ssh)是一种网络协议，用于加密传输信息等作用，最常用的就是实现服务器的远程管理，用ssh客服端，以命令行信息的形式管理服务器。是一种安全的传输协议。

# SSH基本命令

1. 连接命令

   `ssh [-p port]user@hostname`

   * 各个词的含义：`user 代表在远程服务器的用户，hostname 代表远程服务器的ip地址（ip地址唯一标识了一台计算机），port代表服务器ssh服务的端口号`,Linux服务器ssh默认的端口号是22，所以，-p这个 选项可以省略，但ssh服务的端口号要不是22，就得手动输入。

2. 连接问题

   * 先确定服务器是否装配了ssh服务`ps -e | grep ssh`,通常是会安装，但也有例外。安装命令`sudo apt-get install openssh-server`.
   * 然后再检查服务是否开启。

3. 断开连接（exit）

**这些都是建立再Linux基础上的，在Windows上，需要安装ssh服务客户端来启动ssh服务，登陆方式一样**

# SSH高级命令

1. 免密登陆

   * 使用密码登录，每次都必须输入密码，非常麻烦。SSH提供了公钥登录，可以省去输入密码的步骤。

   所谓"公钥登录"，就是用户将自己的公钥储存在远程主机上。登录的时候，远程主机会向用户发送一段随机字符串，用户用自己的私钥加密后，再发回来。远程主机用事先储存的公钥进行解密，如果成功，就证明用户是可信的，直接允许登录shell，不再要求密码。

   * 设置步骤

     1. 生成SSH公钥

        `ssh-keygen`

     2. 上传公钥到服务器

        `ssh-copy-id [-p port] user@hostname`

2. 设置中的问题

   * 此处的免密方法只适用mac和类Unix系统，在Windows下，配置的方法不同。

   * windows下的方法，见[Xshell5免密登陆](http://blog.51cto.com/molewan/1942173).()。

     ​