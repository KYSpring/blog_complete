---
title: pycharm远程连接跳板机和服务器
date: 2021-02-11 15:38:32
categories: 
    - 技术
tags: 
    - pycharm
    - 服务器
    - 环境配置
---
## 问题
>实验室服务器仅限内网访问，同时实验室也提供了一个跳板机，可以先ssh到跳板机再从跳板机ssh到内网服务器。然而这种方式不方便传输文件，也没法用pycharm进行自动同步代码或者远程调试。本篇文章给出了一个该类问题的解决方案，即通过ssh隧道的方式，用Pycharm通过跳板机连接内网服务器。

## 注意
pycharm专业版可以连接远程解释器，而社区版则不含有此功能。服务器端的账号配置此处不做处理。
## 设置SSH隧道
- 这一步实际上也可以通过xshell直接可视化设置
- 端口号可以自行选择
``` bash
ssh -N -f -L 6001:<内网服务器ip>:22 -p <跳板机端口> username@<跳板机ip> -o TCPKeepAlive=yes
```
``` bash
$ ssh -N -f -L 6001:wangshaochun@<内网服务器ip>:22 -p 2444 law@<跳板机ip> -o TCPKeepAlive=yes
```
[参考1](https://blog.csdn.net/witnessai1/article/details/89217498) | [参考2](https://blog.csdn.net/qq_33039859/article/details/89503464) | [整理稿](https://blog.csdn.net/qq_33039859/article/details/89503464) | [SSH教程]( https://wangdoc.com/ssh/key.html )

## 使用Xshell提升连接效率(可选，需要自行安装Xshell)
整体步骤分为两步：<br/>
- 1、构建连接跳板机的会话；
- 2、基于构建的连接跳板机的会话连接内网服务器；<br/>
具体配置上可参考该[博客连接](https://www.cnblogs.com/wzh19820101/p/11907114.html)<br/>

### 配置后的最终结果如图：
- 1、跳板机连接（需要保持跳板机成功连接状态之后才能连接内网服务器）
![xshell连接跳板机](/images/xshell跳板机.png)
![隧道设置](/images/xshell跳板机隧道设置.png)

- 2、之后便可直接访问登录6001接口，连接内网服务器。
![内网服务器连接设置](/images/内网服务器.PNG)

## 使用Pycharm专业版连接远程解释器
- 1.点击tool -> deployment -> configuration进行配置。
<br/>Connection用于配置主机连接；Mapping用于将本地项目文件夹映射到服务器对应位置（会自动实时上传当前的代码）
![pycharm连接远程解释器](/images/pycharm连接远程解释器.png)
- 2.修改项目解释器为远程解释器。
<br/>点击file -> setting -> (project下的)python interpreter选择第一步中已经配置的远程服务器即可。
![解释器修改](/images/pycharm解释器修改.PNG)
默认解释器如图，也可自行在服务器上安装其他版本Python后食用。
![解释器连接](/images/默认的远程解释器.PNG)

选择确定之后该项目便会自动使用远程服务器进行运行啦~
