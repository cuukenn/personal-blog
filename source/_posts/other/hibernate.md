---
title: hibernate随记
date: 2022-07-22 12:00:00
tags: 
  - hibernate
categories: 
  - program
---

###### 1.普通字段无法懒加载
可以使用hibernate-enhance-maven-plugin字节码增强器。
<!-- more -->
注：
- 1.自己单纯的实现PersistentAttributeInterceptable会对集合类的关联关系的懒加载产生影响，如没写好会无法保存多对多
- 2.使用字节码增强器后会导致json序列化失败，就算使用了Hibernate5Module也没用
- 3.针对这种情况，需要做特殊处理，我的方法是直接更改类，在对应set方法中使用PersistentAttributeInterceptor强转LazyAttributeLoadingInterceptor获取到- session判断session是否已经存在
###### 2.hinernate缓存相关
- 1SharedCacheMode，AvailableSettings为hinernate相关配置描述
- 2.直接开启hinernate缓存无效，包括查询缓存和二级缓存，除了开启之外，需要加上@cacheAble或指定策略javax.persistence.sharedCache.mode
- 3.查询缓存hibernate.cache.use_query_cache配合@QueryHint
- 4.集合缓存@Cache(usage = CacheConcurrencyStrategy.NONSTRICT_READ_WRITE) ，加在实体的集合属性上
- 5.使用Jcache且开启二级缓存后需要配置"default-query-results-region"和"default-update-timestamps-region",
        只是写在echach.xml可能无效，会报warn日志并自动创建默认缓存策略，需要添加"hibernate.javax.cache.uri"指定配置文件，
        详细配置见org.hibernate.cache.jcache.ConfigSettings。
- 详见：https://blog.csdn.net/czp11210/article/details/51996217
      相关配置查看请查看SharedCacheMode.java和AvailableSettings.java
