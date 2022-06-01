spring和springboot，mysql，redis，多线程，Nio，jvm，k8s，docker，网络，zookeeper

算法每天练习



MySQL要了解分库分表，然后了解一下大数据量下的题

redis再看1遍

多线程再看看

springcloud在看看，分布式事务这个问题好好看看



redis都用在了那些地方

那些比较难的事情

动态代理

redis大对象优化

排序

动态规划

链表

双指针

二叉树

并查集

栈

单调栈

f(n-1)f(n-2)



Priorityqueue 默认是小根堆，方法有**poll()**，isEmpty()，add()，size()

HashMap的方法有 map.values()，map.KeySet(), map.containsKey(), map,put(), map.get(), **map.remove(key)**，map.entrySet()--for(Entry<Integer,Integer> entry:map.entrySet())，entry.getKey(),entry.getValue()

ArrayList的方法有add，remove,toArray();翻转ArrayList的方法是Collections.reverse(ans); ans.set(index,value);

StringBuilder的方法有append(),delete()

String的方法有valueOf, toLowerCase,toUpperCase,substring(), deleteCharAt(s.length()-1)

Integer的方法有valueOf，Integer.parseInt(String)

Stack的方法有pop() , push() , peek() , empty()

LinkedList的方法add(),addLast(), addFirst(),pollFirst() ,pollLast();

HashSet的方法add(), size(), remove() , contains()



Arrays.copyOfRange()

## 字典序的比较方法

S1.compareTo(s2r)



39.题中

最后的ans.add()时候需要使用，ans.add(new ArrayList<Integer> path)

List<List<Integer>>  ans = new ArrayList; List<Integer> path = new ArrayList<>();

ans.add(new ArrayList<Integer> path)

回溯过程中要减掉ArrayList<>()的最后一个元素使用

int n = path.size()-1;

Path.remove(n);

的方式



分布式事务

两阶段提交（2PC）

正常的提交流程

整个请求的过程被分为两个阶段，第一阶段，协调者发起请求询问参与者是否可以提交，这时候参与者1首先锁定数据，返回可以提交，同事参与者2锁定数据返回可以提交，第二阶段协调者发起请求，命令参与者提交，参与者1返回提交成功了，参与者2也返回提交成功了，正常流程就结束了。

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220513104633528.png" alt="image-20220513104633528" style="zoom: 50%;" />

异常流程

第一阶段，协调者发起请求询问参与者是否可以提交，参与者1锁定数据，返回可以提交，但是参与者2返回不可以，第二阶段，协调者收到了结果，发出取消请求，参与者1返回取消成功，参与者2返回取消成功。

注意：

1、参与者1一定要要求加锁，否则不加锁的话，在第二阶段提交之前，数据改变了那时候就会出现不一致。

正是因为要加锁所以就导致两阶段提交的一系列问题

最严重的问题就是死锁：如果第1阶段，参与者返回了可以提交，并锁定了数据，这时候协调者挂掉了，将会导致协调者不知道如何操作

，因为这时参与者本身并不知道另一个参与者的存在。当然这里可以通过超时回退的方式避免死锁，但是会导致不一致的情况出现。

第二个不可饶恕的问题就是性能比较差，这里的锁是悲观的，随着参与者数量的增加，性能将会大幅下降，本身在分布式架构中，数据库就是性能瓶颈，通常不会关联太多的表进行查询，宁愿增加调用次数，这里是反其道而行之，因此两阶段提交方案应用并不是很广泛。

TCC(Try-Confirm-Cancel)

TCC事务机制相对于两阶段提交，它的特征是不依赖于数据库协议，数据库不需要锁定数据，事务过程由业务服务来实现，这样也就不会出现死锁的问题，性能也会高很多，而且降低了数据库的压力。

缺点：

1、业务变得很复杂，数据库需要增加字段表示状态

2、需要增加相应的接口实现Confirm和Cancel方法

3、业务要保证每个方法的幂等，因为这里可能涉及到失败重试的问题。

TCC事务机制的具体实现

1、主业务发起事务，并调用从业务Try接口，检测业务数据一致性，预留资源。例如优惠券服务，在数据库增加一列，锁定优惠券，不能被重复使用，这样相当与两阶段提交中的加锁操作，数据不能在Try到Confirm这段时间，发生任何变化，否则将导致Confirm失败。

如果Try返回成功，分别调用从业务Confirm接口，提交数据，这一这里不会再检查业务数据了，只能使用上一步中已经预留的资源，例如优惠券服务，Confirm阶段可以使用Try阶段锁定的优惠券，直接把优惠券扣掉，无需检测数据的有效性。

如果执行Try时，任何业务失败或超时，都会分别调用Cancel接口，将前面预留的资源释放。

用伪代码来简单介绍一下，

第一步，在Try阶段，我们使用@TccStart注解来表示事务开启，无论是转入还是转出，这里都只是检测是都满足条件，这里可以在数据库记录要转出的金额，事先锁定。

![image-20220513112036182](/Users/mac/Library/Application Support/typora-user-images/image-20220513112036182.png)

第二步，在子事务Try方法处，添加@participate的注解，通过这个注解指明它所要对应的confirm和cancel方法，如果你仔细看一定会发现，TCC响应的方法都已经有了，这里缺少协调器，也就是控制整个流程的程序代码，这里就比较有学问了，业界有很多实现方案和框架，大多数都是将这个协调器抽象为一个库，内嵌到业务服务中，也就是说业务进程服务是同一个进程，如果这样实现的话还会出现前面谈到的问题，如果协调器挂了怎么办，有的人可能会说，我可以部署多可业务服务啊，这里一定要注意，多个业务服务之间并不共享事务的状态数据，也许有人会说，那可以把这个数据放到统一的地方存储起来啊，没错这就引出了接下来我们的另一种方案。

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220513112307187.png" alt="image-20220513112307187" style="zoom: 33%;" />

也就是说，将业务协调器独立成一个业务服务，这样一来，业务服务和协调器互不影响，又有着很多的隔离性

，这就是servicecomb-pack里面的一种实现方案，Omega是一个业务服务中内嵌的一个agent，负责对调用请求进行拦截，并向Alpha上报事务事件，Alpha就是我们前面说的协调器，主要负责对事务进行管理和协调，这里面Alpha是可以部署多个的，是无状态的，所以呢数据是存放在DB里。

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220513113315517.png" alt="image-20220513113315517" style="zoom:33%;" />

实际上还有很多问题没有解决，如果失败了到底是重试呢还是直接回滚呢，通常我们会把选择就交给业务放来处理。

另外一个问题是ServiceA实际上承担了两个任务，一个是处理业务，另外一个是发消息，那这里任务怎么保持一致呢，按照前面的例子来说，假设有一个M服务，同时调用了A服务和B服务，A往数据库插入一条数据然后告诉Alpha，分支事务提交成功，如果插入业务的数据成功了，发送分支事务提交成功的时候失败了，Alpha会认为事务提交失败了，然后进行Cancel操作，大实际上这条数据已经插入成功了了，和可能已经被别的服务看到了并且修改了，这里就要做一个选择题了，如果要更低的侵入性和更好的性能，可以忽略这个问题，如果要更高的一致性，那可以通过接下来要将的这个方案来实现。

在业务数据库中增加一张消息表，写业务数据的时候，再本地存储一条消息，表示将要向Alpha发送数据消息，注意写数据和本地存储事件的消息，这两步操作在一个单库事务里完成，要么同时成功，要么同时失败，然后向Alpha发送消息，告诉Alpha提交分支事务成功，然后删除本地消息表中的消息，一旦向Alpha发送消息失败，由于消息表中的数据是持久化的，不会丢失数据，