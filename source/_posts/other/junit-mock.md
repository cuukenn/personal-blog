---
title: junit mock随记
date: 2022-07-22 12:00:00
tags: 
  - junit
categories: 
  - program
---

#### 优势：

可以创建一个代理类来代替实际类，并注入到待测试类，可以根据需要设定返回值

#### 相关注解：

```java
@Mock:用于创建一个代理的类，默认不调用实际方法，返回null,可通过Mockito来定义调用函数后的返回值等
```

```
@Spy:用于创建一个代理的类，默认调用类的真实方法，可通过Mockito来定义重新定义返回值，而不执行原函数逻辑
```
<!-- more -->
```
@InjectMocks：用于将上述@Mock或@Spy创建的类注入到@InjectMocks标注的对象，按照类型匹配，当存在多个类型时，按照属性名匹配(实际上需要调用属性对应set方法注入#待考证，注入失败不会抛错，在这种情况可以考虑反射)
```

#### 相关常用类：

##### ArgumentMatchers：

​				用于创建某个Mock类的方法捕获器并返回特定值，

​				如@Mock创建的代理,此示例为mockObject执行 excuteSomeMethod创建一个固定的调用返回值"objectReturn"

```
      Mockito.when(mockObject
          .excuteSomeMethod(ArgumentMatchers.anyString()))
          .thenReturn(objectReturn)
```

##### Mockito:

| 方法名                                                       | 描述                                      |
| ------------------------------------------------------------ | ----------------------------------------- |
| Mockito.mock(classToMock)                                    | 模拟对象                                  |
| Mockito.verify(mock)                                         | 验证行为是否发生                          |
| Mockito.when(methodCall).thenReturn(value1).thenReturn(value2) | 触发时第一次返回value1，第n次都返回value2 |
| Mockito.doThrow(toBeThrown).when(mock).[method]              | 模拟抛出异常。                            |
| Mockito.mock(classToMock,defaultAnswer)                      | 使用默认Answer模拟对象                    |
| Mockito.when(methodCall).thenReturn(value)                   | 参数匹配                                  |
| Mockito.doReturn(toBeReturned).when(mock).[method]           | 参数匹配（直接执行不判断）                |
| Mockito.when(methodCall).thenAnswer(answer))                 | 预期回调接口生成期望值                    |
| Mockito.doAnswer(answer).when(methodCall).[method]           | 预期回调接口生成期望值（直接执行不判断）  |
| Mockito.spy(Object)                                          | 用spy监控真实对象,设置真实对象行为        |
| Mockito.doNothing().when(mock).[method]                      | 不做任何返回                              |
| Mockito.doCallRealMethod().when(mock).[method] //等价于Mockito.when(mock.[method]).thenCallRealMethod(); | 调用真实的方法                            |
| reset(mock)                                                  | 重置mock                                  |

#### 特殊处理：

1，返回值为空时：

```
Mockito.doAnswer((InvocationOnMock invocation) -> {
  Object[] args = invocation.getArguments();
 //doSomething
  return null;
}).when(mockObjct).excuteSomeMethod(ArgumentMatchers.anyString(), ArgumentMatchers.any());
通过以上处理可以捕获返回值为void的mockObjct代理函数，并获取到其参数列表
```

##### 其他：

​	可查看他人blog:https://blog.csdn.net/u012894692/article/details/82599100
