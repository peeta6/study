# JVM

 ![img](https://img-blog.csdnimg.cn/20190216114129109.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzM3NTI0NjYx,size_16,color_FFFFFF,t_70)

## 面试常见问题

●请你谈谈你对JVM的理解? java8虚拟机和之前的变化更新? ●什么是OOM，什么是栈溢出StackOverFlowError? 怎么分析? ●JVM的常用调优参数有哪些? ●内存快照如何抓取，怎么分析Dump文件？ ●谈谈JVM中，类加载器你的认识

## 1、JVM的位置

<img src="https://img-blog.csdnimg.cn/20200714083435768.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L29rRm9ycmVzdDI3,size_16,color_FFFFFF,t_70#pic_center" alt="img" style="zoom: 67%;" />

## 2、JVM的体系结构

![image-20211206110932952](/Users/mac/Library/Application Support/typora-user-images/image-20211206110932952.png)

百分之99的JVM调优都是在堆中调优。Java栈、本地方法栈、程序计数器是不会有垃圾存在的。

## 3、类加载器

作用：加载class文件

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20211206112735034.png" alt="image-20211206112735034" style="zoom:67%;" />

图片解释：new一个对象的过程，反射机制

类加载器分级 ：

1、虚拟机自带的加载器

2、启动类（跟）加载器

3、扩展类加载器

4、应用程序加载器  

![image-20211206113356634](/Users/mac/Library/Application Support/typora-user-images/image-20211206113356634.png)

### 双亲委派机制

博客链接链接:https://blog.csdn.net/codeyanbao/article/details/82875064

<img src="https://img-blog.csdnimg.cn/2020121722082798.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGV5YW5iYW8=,size_16,color_FFFFFF,t_70" alt="img" style="zoom: 67%;" />

### 沙箱安全机制


​		Java安全模型的核心就是Java沙箱（sandbox），什么是沙箱？沙箱是一个限制程序运行的环境。沙箱机制就是将 Java 代码限定在虚拟机(JVM)特定的运行范围中，并且严格限制代码对本地系统资源访问，通过这样的措施来保证对代码的有效隔离，防止对本地系统造成破坏。**沙箱主要限制系统资源访问**，那系统资源包括什么？——CPU、内存、文件系统、网络。不同级别的沙箱对这些资源访问的限制也可以不一样。

​		所有的Java程序运行都可以指定沙箱，可以定制安全策略。

​		对于沙箱演变的历史了解就行，java1.0:引入**沙箱机制**=>java1.1:增加**受信任**的安全策略=>java1.2:增加**代码签名**=>最新的是引入**域(domin)**的概念

当前最新的安全机制实现，则引入了域 (Domain) 的概念。虚拟机会把所有代码加载到不同的**系统域**和**应用域**，系统域部分专门负责与关键资源进行交互，而各个应用域部分则通过系统域的部分代理来对各种需要的资源进行访问。虚拟机中不同的受保护域 (Protected Domain)，对应不一样的权限 (Permission)。存在于不同域中的类文件就具有了当前域的全部权限，如下图所示最新的安全机制（JDK1.6):

![4](https://img-blog.csdnimg.cn/2020110322142264.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JhaWR1XzM4MTI2MzA2,size_16,color_FFFFFF,t_70#pic_center)

组成沙箱的基本组件：



- **字节码校验器**（bytecode verifier）:确保Java类文件遵循Java语言规范。这样可以帮助Java程序实现内存保护。但并不是所有的类文件都会经过字节码校验，比如核心类。

- **类装载器**（class loader）:其中类装载器在3个方面堆Java沙箱起作用：
  - 防止恶意代码去干涉善意的代码；//**双亲委派**机制
  - 守护了被信任的类库边界；//**双亲委派**机制
  - 将代码归入保护域，确定了代码可以进行哪些操作。//**沙箱安全**机制

- **存取控制器**（access controller）：存取控制器可以控制核心API堆操作系统的存取权限，而这个控制的策略设定，可以由用户指定。

- **安全管理器**（security manager）: 是核心API和操作系统之间的主要接口。实现权限控制，比存取控制器优先级高。

- **安全软件包**（security package）：java.security下的类和扩展包下的类。允许用户为自己的应用增加新的安全特性，包括：
  - 安全提供者
  - 消息摘要
  - 数字签名 keytools
  - 加密

### Native关键字

因为历史的原因，Java为了融合不同的编程语言为自己所用，想要调用别的语言，就会加native关键字，想要运行就在运行时数据区中开辟一个本地方法栈通过JNI- 本地方法接口调用本地方法库实现方法。

### PC寄存器

程序计数器:Program Counter Register

每个线程都有一个程序计数器，是线程私有的，可以简单理解为一个指针指向方法区中的方法字节码（指向即将要执行的指令代码），是一个非常小的内存空间，由执行引擎读取下一条指令。若执行的是一个native方法，则程序计数器是空的。程序计数器主要用来完成分支、循环、跳转以及异常处理等基础功能。

### 方法区

详解链接：https://blog.csdn.net/ym15229994318ym/article/details/106554661

Method Area 方法区

方法区是被所有线程共享，**所有字段和方法字节码**，以及一些特殊方法如构造函数，接口代码也在此定义。简单说，所有定义的方法的信息都保存在该区域，此区域属于共享区间。**静态变量+常量+类信息+运行时常量池**存在方法区中，实例变量存在堆内存中。
1.又叫静态区，跟堆一样，**被所有的线程共享**。方法区包含所有的class和static变量。 
2.方法区中包含的都是在**整个程序中永远唯一的元素**，如class，static变量。 

**静态变量，常量，类信息（构造方法，接口定义），运行时的常量池存在方法区中，但是实例变量存在堆内存中，和方法区无关**

方法区存储：static，final，Class模板，运行时的常量池

java8之后叫元空间，跟内存绑定，并不在堆内存里





![在这里插入图片描述](https://img-blog.csdnimg.cn/20200825115137659.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RzdW5hZ3h5,size_16,color_FFFFFF,t_70#pic_center)

### 栈：数据结构

栈也叫栈内存，主管Java程序的运行，是在线程创建时创建，它的生命期是跟随线程的生命期，线程结束栈内存也就释放，对于栈来说不存在垃圾回收问题，只要线程一结束该栈就Over，生命周期和线程一致，是线程私有的。**基本类型的变量和对象的引用变量都是在函数的栈内存中分配**。 （）栈中的数据都是以栈帧（Stack Frame）的格式存在，栈帧是一个内存区块，是一个数据集，是一个有关方法和运行期数据的数据集，当一个方法A被调用时就产生了一个栈帧F1，并被压入到栈中，A方法又调用了B方法，于是产生栈帧F2也被压入栈，B方法又调用了C方法，于是产生栈帧F3也被压入栈…… 依次执行完毕后，先弹出后进......F3栈帧，再弹出F2栈帧，再弹出F1栈帧。

栈存储：**基本类型的变量**和**对象的引用变量**都是在函数的栈内存中分配

注意：实例是在堆中的

栈满了抛异常：StackOverflowError

栈帧：

![image-20211206143319779](/Users/mac/Library/Application Support/typora-user-images/image-20211206143319779.png)

对象实例化的过程链接：https://blog.csdn.net/qq36846776/article/details/110263526

对象实例化过程分为类加载初始化和对象初始化，先类加载初始化再对象初始化，子类初始化需要先父类初始化

类初始化的过程：

先加载class文件，在使用字节码校验器：校验符不符合当前JVM规范，准备：为所有的**类变量**赋初值，final修饰的赋值声明值，把常量池中的符号引用全部替换成直接引用，然后初始化，执行**类构造器**（不是对象构造器），为类变量赋值，执行静态代码块，jvm会保证子类的<client>执行之前，父类的<client>先执行完毕

对象实例化过程：

对象实例化过程其实就是执行类构造函数对应在字节码文件中的<init>()方法(称之为实例构造器)；<init>()方法由 非静态变量、非静态代码块以及对应的构造器组成
<init>()方法可以重载多个，类有几个构造器就有几个<init>()方法
<init>()方法中的代码执行顺序为：父类变量初始化，父类代码块，父类构造器，子类变量初始化，子类代码块，子类构造器。
静态变量，静态代码块，普通变量，普通代码块，构造器的执行顺序

### JVM调参数

#### 改变堆内存大小

 -Xms 1024m -Xms 1024m -XX:+printGCDetails

#### dump文件调优

首先idea安装Jprofiler插件，然后下载Jprofiler客户端，官网是：https://www.ej-technologies.com/download/jprofiler/files，然后idea中的tools中配置Jprofiler客户端路径。

编辑器加上vm参数：

-Xms 1m  -Xms  8m -XX:+HeapDumpOnOutOfMemoryError

Xms 设置初始化内存分配大小,默认1/64

Xmx 设置最大分配内存，默认1/4

## 4、JMM

JMM：Java Memory Model ，Java内存模型

作用缓存一致性协议，用于定义数据读写的规则

volivate关键字：

保证可见性，不保证原子性，保证指令不重排

