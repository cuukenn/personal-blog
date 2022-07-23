---
title: >-
  windows运行正常但是linux下程序在浏览器控制台console报websocket is closed before the connection
  is established
description: >-
  windows运行正常但是linux下程序在浏览器控制台console报websocket is closed before the connection
  is established
tags:
  - bugfix
categories:
  - program
abbrlink: 1b86e9b0
date: 2022-07-22 12:00:00
---

**原因：**

1. 连接socket时需要通过IP获取到对应主机的域名来进行连接，此过程会相当耗时
2. 前端有一个超时时间，导致无法在规定时间内收到服务端消息而触发超时操作

**表现：**

1. windows及linux下可不同时出现，与DNS解析的配置有关
2. 浏览器console界面会打印大量websocket is closed 错误消息
3. 可能伴随着刷新很慢之类的相关异常现象

**解决方法：**

1. 在hosts文件中添加对应IP的反解析，如：示例IP IP ,其中IP为服务机器的内网地址，无效则自己通过堆栈在某个地方debug代码来确认
2. 重写SocketWrapperBase类的getLocalName方法，直接返回其私有属性localAddr。此处不做赘述，存在多个类可用于重写代码，根据需要自己合理选择
3. 修改DNS相关配置(可能还存在其他相关配置，个人没有找到方法1外的相关配置，故无法给出实际例子)

**推荐：**

      卡顿的时候可以使用JDK自带工具进行分析

       获取程序进程号

```bash
JPS
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

       获取当前程序的堆栈

```bash
JSTACK PID > stack.log
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

**前台报错**

```
WebSocket connection to 'ws://127.0.0.1:8080/pms/websocket/181/vledvxj1/websocket' failed: WebSocket is closed before the connection is established.
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

**后台报错**

```
Caused by: java.io.IOException: Broken pipe
    at sun.nio.ch.FileDispatcherImpl.write0(Native Method)
    at sun.nio.ch.SocketDispatcher.write(SocketDispatcher.java:47)
    at sun.nio.ch.IOUtil.writeFromNativeBuffer(IOUtil.java:93)
    at sun.nio.ch.IOUtil.write(IOUtil.java:65)
    at sun.nio.ch.SocketChannelImpl.write(SocketChannelImpl.java:487)
    at org.apache.tomcat.util.net.NioChannel.write(NioChannel.java:124)
    at org.apache.tomcat.util.net.NioBlockingSelector.write(NioBlockingSelector.java:101)
    at org.apache.tomcat.util.net.NioSelectorPool.write(NioSelectorPool.java:172)
    at org.apache.coyote.http11.InternalNioOutputBuffer.writeToSocket(InternalNioOutputBuffer.java:139)
    at org.apache.coyote.http11.InternalNioOutputBuffer.addToBB(InternalNioOutputBuffer.java:197)
    at org.apache.coyote.http11.InternalNioOutputBuffer.access$000(InternalNioOutputBuffer.java:41)
    at org.apache.coyote.http11.InternalNioOutputBuffer$SocketOutputBuffer.doWrite(InternalNioOutputBuffer.java:320)
    at org.apache.coyote.http11.filters.ChunkedOutputFilter.doWrite(ChunkedOutputFilter.java:118)
    at org.apache.coyote.http11.AbstractOutputBuffer.doWrite(AbstractOutputBuffer.java:256)
    at org.apache.coyote.Response.doWrite(Response.java:491)
    at org.apache.catalina.connector.OutputBuffer.realWriteBytes(OutputBuffer.java:391)
    ... 21 more
```

![](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw== "点击并拖拽以移动")

**调用堆栈：**

>    at java.net.Inet6AddressImpl.getHostByAddr(Native Method)  
>     at java.net.InetAddress$2.getHostByAddr(InetAddress.java:932)  
>     at java.net.InetAddress.getHostFromNameService(InetAddress.java:617)  
>     at java.net.InetAddress.getHostName(InetAddress.java:559)  
>     at java.net.InetAddress.getHostName(InetAddress.java:531)  
>     at org.apache.tomcat.util.net.NioEndpoint$NioSocketWrapper.populateLocalName(NioEndpoint.java:1304)  
>     at org.apache.tomcat.util.net.SocketWrapperBase.getLocalName(SocketWrapperBase.java:223)  
>     at org.apache.coyote.AbstractProcessor.action(AbstractProcessor.java:464)  
>     at org.apache.coyote.Request.action(Request.java:432)  
>     at org.apache.catalina.connector.Request.getLocalName(Request.java:1307)  
>     at org.apache.catalina.connector.RequestFacade.getLocalName(RequestFacade.java:1002)  
>     at javax.servlet.ServletRequestWrapper.getLocalName(ServletRequestWrapper.java:336)  
>     at javax.servlet.ServletRequestWrapper.getLocalName(ServletRequestWrapper.java:336)  
>     at javax.servlet.ServletRequestWrapper.getLocalName(ServletRequestWrapper.java:336)  
>     at org.springframework.http.server.ServletServerHttpRequest.getLocalAddress(ServletServerHttpRequest.java:197)

​
