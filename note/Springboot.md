# Springboot

## 配置bean

![image-20211228233536485](/Users/mac/Library/Application Support/typora-user-images/image-20211228233536485.png)

### @Configuration结合@Bean

![image-20211228234436962](/Users/mac/Library/Application Support/typora-user-images/image-20211228234436962.png)

![image-20211228234403431](/Users/mac/Library/Application Support/typora-user-images/image-20211228234403431.png)

SpringBoot启动过程

1.bootstrappers

2.applicationcontextInit

3.applicationlistener

运行springappplication

stop watch

记录应用启动时间

创建引导上下文

进入headless模式

获取所有的runnerlistener

遍历 **SpringApplicationRunListener 调用 starting 方法**

保存命令行参数

准备环境

创建ioc容器

准备ioc容器的基本信息

刷新ioc容器

遍历所有的runners调用run

如果有异常就调用listener.failed