---
title: hibernate普通字段懒加载集合关联无法保存的解决方法，及hibernate字节增强插件的使用
tags:
  - bugfix
  - database
categories:
  - program
abbrlink: ece0d388
date: 2022-07-22 12:00:00
---

首先请查看[Hibernate 5 & JPA 2.1 延迟加载大字段属性_windsigirl123的博客-CSDN博客](https://blog.csdn.net/windsigirl123/article/details/60957632 "Hibernate 5 & JPA 2.1 延迟加载大字段属性_windsigirl123的博客-CSDN博客")，通过模仿这篇文章确实可以产生效果，但是出现ManyToMany的集合关联时，即存在集合关联懒加载时，不能单纯的模仿博文提到的普通字段的修改方式。

### 该情况下会产生两个问题：

1.多对多懒加载关联无法保存数据，其他集合懒加载关联类似

<!-- more -->

2.如果编写代码失误会导致懒加载的字段无法序列化，即便使用了Hibernate5Module(该情况在使用hibernate字节增强插件时尤其明显，下面讲)。

### 解决方法：

使用hibernate-enhance-maven-plugin字节增强插件，该插件会在编译时会修改实体到目标值，可以查看反序列化后的效果。

### 使用方法：

maven:

```html
 <plugin>
        <groupId>org.hibernate.orm.tooling</groupId>
        <artifactId>hibernate-enhance-maven-plugin</artifactId>
        <version>4.3.5.Final</version>
        <configuration>
               <failOnError>true</failOnError>
               <enableLazyInitialization>true</enableLazyInitialization>//必须要
               <enableDirtyTracking>true</enableDirtyTracking>
               <enableAssociationManagement>true</enableAssociationManagement>//存在懒加载集合关联需要加上这个配置
        </configuration>
        <executions>
            <execution>
                <goals>
                    <goal>enhance</goal>
                </goals>
            </execution>
        </executions>
    </plugin>
```

然后运行

```bash
mvn install -Dmaven.test.skip=true
```

可反编译编译的class文件进行查看确认。

问题：

使用字节增强会导致序列化问题，如果有更好的方法可以分享下。

我几番尝试，无奈，只能将反汇编的替换掉原来的类，再在需要懒加载字段的get方法中加上了判断session是否有效的语句，如下：

(注:this.$$_hibernate_getInterceptor())是使用字节增强会在class文件中加上去的)
```html
if (this.$$_hibernate_getInterceptor() instanceof LazyAttributeLoadingInterceptor
  &&
  (
    ((LazyAttributeLoadingInterceptor) this.$$_hibernate_getInterceptor()).getLinkedSession() == null)
  || ((LazyAttributeLoadingInterceptor) this.$$_hibernate_getInterceptor()).getLinkedSession().isClosed()
) {
  return this.filed;
}
```