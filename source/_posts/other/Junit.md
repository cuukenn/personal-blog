---
title: junit随记
tags:
  - junit
categories:
  - program
abbrlink: c96d5477
date: 2022-07-22 12:00:00
---

#### 1.本次重新回顾，重新熟悉Junit的注解，了解新注解@Rule
        @Rule注解,可以通过实现TestRule来对方法做切面，在@Test方法运行前后加入自定义代码
        Junit也提供了一些默认Rule,如下：
          + TemporaryFolder Rule：创建一些临时目录或者文件
<!-- more -->
          + ExternalResource Rule：ExternalResource 是TemporaryFolder的父类，主要用于在测试之前创建资源，并在测试完成后销毁
          + ErrorCollector Rule：收集多个错误，并在测试执行完后一次过显示出来
          + Verifier Rule：是ErrorCollector的父类，可以在测试执行完成之后做一些校验，以验证测试结果是不是正确
          + TestWatcher Rule：定义了五个触发点，分别是测试成功，测试失败，测试开始，测试完成，测试跳过，能让我们在每个触发点执行自定义的逻辑
          + TestName Rule：能让我们在测试中获取目前测试方法的名字
          + Timeout与ExpectedException Rule：分别用于超时测试与异常测试
     特别的，SpringBoot提供了OutputCapture：用来取出测试方法执行过程的日志
       我写了个自定义rule结合OutputCapture来统计查询语句数量，循环控制及总运行时间
       @After,@AfterClass,@Before,@BeforeClass,@Test,@RunWith简单，只是进行回顾
---
#### 2.使用Junit进行模拟登录后的Web接口测试(更多Mock操作详见mock.md)

1：启动容器进行测试

```kotlin
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:config/context.xml")
@WebAppConfiguration
public class IncotermsRestServiceTest {
    @Autowired
    private WebApplicationContext wac;
    private MockMvc mockMvc;
    @Before
    public void setup() {
        this.mockMvc = MockMvcBuilders.webAppContextSetup(this.wac).build();   //构造MockMvc
    }
    ...
}

注意：
    (1)@WebAppConfiguration：测试环境使用，用来表示测试环境使用的ApplicationContext将是WebApplicationContext类型的；value指定web应用的根；
    (2)通过@Autowired WebApplicationContext wac：注入web环境的ApplicationContext容器；
    (3)然后通过MockMvcBuilders.webAppContextSetup(wac).build()创建一个MockMvc进行测试；
```

2：不启动容器模拟测试（速度快）

```java
public class PricingExportResultsRestServiceTest {
    @InjectMocks
    private PricingExportResultsRestService pricingExportResultsRestService;
    @Mock
    private ExportRateScheduleService exportRateScheduleService;
    @Mock
    private PricingUrlProvider pricingUrlProvider;
    private MockMvc mockMvc;
    @Before
    public void setup() {
        //初始化可换成@RunWith(MockitoJUnitRunner.class)，当需要使用其他Runner时使用以下的initMocks
        MockitoAnnotations.initMocks(this);
        mockMvc = MockMvcBuilders.standaloneSetup(pricingExportResultsRestService).build();  //构造MockMvc
    }
    ...
}

主要是两个步骤：
(1)首先自己创建相应的控制器，注入相应的依赖
(2)通过MockMvcBuilders.standaloneSetup模拟一个Mvc测试环境，通过build得到一个MockMvc
```

---
#### 3.使用Junit进行高并发测试
