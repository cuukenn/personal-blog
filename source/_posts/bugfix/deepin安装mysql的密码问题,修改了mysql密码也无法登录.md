---
title: 'deepin安装mysql的密码问题,修改了mysql密码也无法登录'
description: 'deepin安装mysql的密码问题,修改了mysql密码也无法登录'
tags:
  - bugfix
  - linux
categories:
  - program
abbrlink: 968058fb
date: 2022-07-22 12:00:00
---


每次安装mysql都会出现这个问题,真的挺无语的,上次是centos7,它在安装的时候会自动生成一个密码,在log日志里

查看命令大概是这样的:

```bash
cat /var/log/mysql/mysql.log | grep "password";
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

这次我要说的是deepin下的Mysql密码初始化,修改问题

问题描述:

执行

```bash
sudo apt-get mysql-server  mysql-client
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

后按网上的说法是会出现设置mysql root账户密码的弹框,但实际上并不出现

它会创建一个特殊用户,如下

![](https://img-blog.csdn.net/20180922144716963?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzU5MDkx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

此时mysql -uroot -p无法登录

可以使用

```sql
sudo mysql -uroot -p 
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

无密码登录

登录完成后使用改密码命令,例如:(新版本password字段改成了authentication_string)

```sql
set authentication_string=password("密码") ;
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

或

```sql
update user setset authentication_string=password("密码") where user="用户名";
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

然后如果发现重新退出登录还是无效可以看看下面的命令输出的plugin是不是和我的一样

输入如下命令可以看到

![](https://img-blog.csdn.net/20180922144923412?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzU5MDkx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")编辑

一样就只需要将plugin改为下面几个用户的就行,命令如下

```sql
update user set plugin="mysql_native_password" where user="用户名";
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

然后在正常执行改密码方法后重新登录就可以了

结果大概如下(password_expired要为N才能使用对应账户)

![](https://img-blog.csdn.net/20180922145654972?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM2NzU5MDkx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")编辑