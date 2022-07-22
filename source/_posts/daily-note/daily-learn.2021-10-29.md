---
title: 2021-10-29 日常杂记
date: 2022-07-22 12:00:00
tags: 
  - daily-note
categories: 
  - program
---

- DDD Repository变更追踪

  - snapshot：缓存快照、然后反射diff(实现简单、反射调用、性能一般、典型框架：Hibernate)
  - proxy：代码编织、set增强记录状态变更(实现复杂、对象复杂容易BUG，典型框架：Entity Framework)

<!--more--> 

- DDD分层

  - XXXDTO ->  Data Assembler转化  -> Entity ->  Data Converter转化  -> XXXDO
  - 转化方式：可以使用mapstruct快速进行类型转化
  - 避免上层过度依赖底层实现
  - XXXDTO可再进行细分、减去不必要请求响应数据
  - XXXEntity可以将多个DO层库表合并到一个中进行宽表处理

- DDD Repository

  - 术语上将是：软件与固件/硬件 实现分离、避免依赖传递
  - 软件设计中、就是业务逻辑与数据持久化操作/数据保存方式(DB等)进行隔离

- DP：领域模型中的值对象

  - Domain Primitive

  - 无状态：有状态的需将有状态的单独抽离

  - 在运行时避免参数传递错误导致的特殊BUG

    ```java
    //方法定义
    public void transfer(String name,String age);
    //错误调用
    xxx.transfer('12','小明');
    ```

  - 内部包含实体属性和无状态方法、部分业务逻辑与实体进行耦合

    - 例如：将随处可见的校验规则耦合进类型的创建之中

      ```java
      //定义
      class PhoneNumber{
      	private final String phoneNumber
      	public PhoneNumber(String phoneNumber){
      	  //进行手机号check
      	  //属性初始化
      	}
      	
      	public String getType(){
      		//经过计算获取手机号区号
      	}
      	
      	public xxx xxx
      }
      //使用
      class XXXEntity{
      	private PhoneNumber phone;
      	private XXX         xxx;
      }
      ```

      