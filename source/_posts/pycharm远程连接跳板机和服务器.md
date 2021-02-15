---
title: pycharm远程连接跳板机和服务器
date: 2021-02-11 15:38:32
categories: 
    - 技术
tags: 
    - pycharm
    - 服务器
    - 环境配置
    - jupyter notebook
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
## 本地跳板机转发jupyter notebook
思路上和使用pycharm远程连接解释器类似：由于Jupyter_notebook是通过浏览器端口访问的，所以只需要通过跳板机将内网服务器的对应端口转发到本地端口即可。
### 服务器端的准备工作
- 1、安装jupyter_notebook（如果是anaconda的话，直接在需要安装的虚拟环境下输入conda install jupyter notebook即可）。
- 2、由于Jupyter_notebook默认设置是只允许在jupyter_notebook所安装的PC上运行而拒绝跨PC访问的，因此需要首先设置一下Jupyter_notebook的config文件。[【参考博客】](https://blog.csdn.net/simple_the_best/article/details/77005400)
```bash
#生成config文件
jupyter notebook --generate-config

#生成密码
In [1]: from notebook.auth import passwd
In [2]: passwd()
Enter password:
Verify password:
Out[2]: 'sha1:67c9e60bb8b6:9ffede08*******71089e11aed'

#修改配置文件
vim ~/.jupyter/jupyter_notebook_config.py
```
作如图所示修改，并记得去除对应的注释：
![jupyter配置](/images/jupyter修改配置.PNG)
这之后在服务器端运行jupyter notebook，注意查看其运行所在的端口。该端口即是需要进行隧道转发的jupyter访问端口。（笔者的是运行在8888端口）

### 本地的准备工作
本地需要做的就是转发服务器8888端口到本地端口（笔者选择本地也转发到8888端口）。通过Xshell或者直接命令行都可以配置，笔者采用Xshell进行SSH隧道设置，设置方法和上文转发跳板机的方法类似。
![jupyter隧道设置](/images/jupyter隧道设置.PNG)

### 浏览器访问localhost:8888（自己设置的端口号）
![jupyter远程访问成功](/images/jupyter成功画面.PNG)

即可愉快地使用远程服务器上的Jupyter notebook啦~
