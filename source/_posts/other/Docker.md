---
title: Docker随记
tags:
  - docker
categories:
  - program
abbrlink: cf5597ac
date: 2022-07-22 12:00:00
---

###### ps(总是用完就把命令给忘记了，记录一下)
#### 1.安装（略）
#### 2.拉取容器（略）
#### 3.创建容器
    docker run -itd -v 主机路径：容器路径 -p 主机端口：容器端口 --name 命名  tag(Images的tag)
    其中，-i表示保持容器标准输入一直活动如果未断开,-t代表开启一个tty,-d表示容器后台运行
#### 4.重新进入容器
    docker exec -itd container /bin/sh
