---
title: Linux系统管理及系统信息
categories: Linux
date: 2018-03-19 23:20:12
tags:
- 系统信息查询
- 进程管理
---

# 系统信息查询

## 系统时间查询

1. `cla`命令

   显示系统当前月日历，参数：

   -y 查询某一年的日历

2. `date`命令

   显示系统当前时间，参数：

   ​

## 磁盘、进程信息查询

1. `df`命令

   列出系统磁盘使用情况。

2. `du`命令

   查看某一个目录的磁盘使用情况。

3. `ps`命令

   查看进程的详细情况，参数：

   au： 显示终端上所有进程的详细信息。

   x ： 显示非终端的进程。

4. `top`命令

   动态的显示进程信息，并排序。

5. `kill`命令

   终止某一进程。`kill 进程PID  -9 选项，强制终止` 。

## 网络信息查看和设置

1. 查看ip地址和暂时设置ip地址

   `ifconfig`命令。

   无参数，列出当前网卡信息，ip地址。

   指定ip，`ifconfig eth0 145.255.11.36`。

2. 查看网络联通情况

   `ping `命令。

   用于检测与主机的连通性。

   `ping 主机名称或IP地址`。

3. `netstat`命令

   显示网络相关信息。包括连接方式，端口号等。


## 用户信息查看

1. `whoami`命令。

   查看当前正在登陆的用户。

2. `last`命令。

   查看以往登陆过的用户。

   ​