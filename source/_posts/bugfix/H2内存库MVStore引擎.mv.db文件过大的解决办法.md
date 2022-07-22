---
title: H2内存库MVStore引擎*.mv.db文件过大的解决办法
description: H2内存库MVStore引擎*.mv.db文件过大的解决办法
date: 2022-07-22 12:00:00
tags: 
  - bugfix
  - database
categories: 
  - program
---

  - 原因(仅个人观点)
  
  - h2的chunk机制：每次commit新建一个chunk存储块，
    
    参考：[H2的存储子系统——MvStore_HappySkaikai的博客-CSDN博客](https://blog.csdn.net/kaixin89/article/details/50738486 "H2的存储子系统——MvStore_HappySkaikai的博客-CSDN博客")
    
    查看*.mv.db文件相关信息
    - `MVStoreTool.main("-info","xxx.mv.db.bak")`
  - 程序异常停止，导致h2的`shutdown compact`或者`shutdown defrag`未完成(如：`KILL -9 PID`)
  
  - 长时间运行、但无定时清理任务
  
  - 代码嵌套事务处于递归函数中

- 解决方案
  
  - 1，升级jar包，VERSION>=1.4.200(不一定有用)：
    
    - VERSION=1.4.200
      
      增加配置'AUTO_COMPACT_FILL_RATE',默认值为90 自定义配置项：`jdbc:h2:tcp://localhost/./dbname;MAX_COMPACT_COUNT=70`
      
      仅对”Chunk fill rate“值小的情况有效、多次COMMIT导致的”Chunk fill rate“值大无法处理
  
  - 2、查看代码、移除递归中的嵌套事务(不一定有用)：
  
  - 3、如果使用到了JPA+HIBERNATE+MapStruct（本人测试有效）
    
    - hibernate-5.2.8.Final
      
      db文件正常
    
    - hibernate版本>5.2.8.Final（本人只测试5.4.27.Final）
      
      大量事务后进行sync，mv.db文件很大
      
      在不回退hibernate版本的情况下、可先定位到哪部分的事务导致的、然后修改对应位置的model到entry的转化逻辑、由MapStruct中对级联对象的new操作改成将外键查询数据库后的实体直接set进去

- 查看当前内存数据库对应配置项的值
  
  - VERSION=1.4.200
    
    `SELECT * FROM INFORMATION_SCHEMA.SETTINGS WHERE NAME='MAX_COMPACT_COUNT'`

- 更多信息、请查看以下类的方法或属性(搜索关键词：autoCompactFillRate)
  
  - 1.org.h2.mvstore.MVStore#writeInBackground
  
  - 2.org.h2.mvstore.db.Store#Store
  
  - 3.org.h2.mvstore.MVStore#MVStore
  
  - 4.org.h2.engine.DbSettings#autoCompactFillRate
    
    参考：[add setting AUTO_COMPACT_FILL_RATE by auntyellow · Pull Request #2262 · h2database/h2database · GitHub](https://github.com/h2database/h2database/pull/2262 "add setting AUTO_COMPACT_FILL_RATE by auntyellow · Pull Request #2262 · h2database/h2database · GitHub")


