---
title: 2021-10-13 日常杂记
date: 2022-07-22 12:00:00
tags: 
  - daily-note
categories: 
  - program
---

- java shutdownhook
<!--more--> 
  - JNI DestroyJavaVM 在不存在非守护线程后调用Shutdown#shutdown
  - 默认使用了三个插槽(默认大小为10,Runable类型)
    - 0：console
    - 1：ApplicationShutdownHooks(程序使用、Runtime#getRuntime#addShutdownHook)
    - 2：DeleteOnExitHook(文件删除)
  - kill -0 PID：检查指定PID是否存在
- @Contented
  - 缓存行：cpu读取缓存有固定长度，当一个单元内放科多个缓存对象时会发生竞争
  - 使用该注解会自动填充、使数据占用一整行缓存

