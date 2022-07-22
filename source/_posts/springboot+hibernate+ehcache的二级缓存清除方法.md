---
title: springboot+hibernate+ehcache的二级缓存清除方法
date: 2022-07-22 12:00:00
tags: 
	- bugfix
	- hibernate
	- cache
categories: 
	- program
---

1：注入EntityManager

```html
@PersistenceContext
private EntityManager entityManger;
```

2：获取hibernate缓存对象

```html
CacheImpl cache=（CacheImpl）entityManger.getEntityManagerFactory().getCache();
```

<!--more-->

3:接下来就可以随心所欲过期不需要的缓存，如实体缓存，集合缓存，查询缓存等，具体方法自己去查看

```html
org.hibernate.internal.CacheImpl类，如清除全部类型缓存是cache.evictAllRegions()
```

主要有以下几类：

```html
（1）evictCollectionRegions()：集合缓存相关
（2）evictDefaultQueryRegion();查询缓存
（3）evictEntityRegions();实体缓存
（4）evictQueryRegions();查询缓存
（5）evictNaturalIdRegions();不知道什么缓存
```

ps:有想要了解具体hibernate缓存详情的请查看友链：

- [Hibernate Second-Level Cache | Baeldung](https://www.baeldung.com/hibernate-second-level-cache "https://www.baeldung.com/hibernate-second-level-cache")
- [spring-data-jpa+hibernate 各种缓存的配置演示_czp11210的博客-CSDN博客](https://blog.csdn.net/czp11210/article/details/51996217 "spring-data-jpa+hibernate 各种缓存的配置演示_czp11210的博客-CSDN博客")