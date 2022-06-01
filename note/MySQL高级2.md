## 七、锁

介绍

![image-20220215223734817](/Users/mac/Library/Application Support/typora-user-images/image-20220215223734817.png)

分类

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220215223812882.png" alt="image-20220215223812882" style="zoom:50%;" />

### 全局锁

#### 介绍

![image-20220215224235249](/Users/mac/Library/Application Support/typora-user-images/image-20220215224235249.png)

#### 演示流程

![image-20220215224428285](/Users/mac/Library/Application Support/typora-user-images/image-20220215224428285.png)

```sql
#  首先登录到数据库中，然后使用全局锁
flush tables with read lock;

# 操作完成了释放锁
unlock tables;
```

```shell
# 使用了全局锁之后就用命令行输入，注意不是在数据库命令行，而是电脑的命令行
mysqldump -h 8.141.145.31 -P 3310 -uroot -proot cloud > /Users/mac/bjhl/cloud.sql
```

#### 特点

![image-20220215230315634](/Users/mac/Library/Application Support/typora-user-images/image-20220215230315634.png)

```shell
# 加上--single-transaction就不用加全局锁了，这是innoDB引擎的快照
mysqldump --single-transaction -h 8.141.145.31 -P 3310 -uroot -proot cloud > /Users/mac/bjhl/cloud.sql 
```

### 表级锁

#### 介绍及分类

![image-20220215230653209](/Users/mac/Library/Application Support/typora-user-images/image-20220215230653209.png)

 ![image-20220215230737378](/Users/mac/Library/Application Support/typora-user-images/image-20220215230737378.png)

#### 表锁

![image-20220215230809679](/Users/mac/Library/Application Support/typora-user-images/image-20220215230809679.png)

![image-20220215230850333](/Users/mac/Library/Application Support/typora-user-images/image-20220215230850333.png)

![image-20220215231544002](/Users/mac/Library/Application Support/typora-user-images/image-20220215231544002.png)

![image-20220215231944911](/Users/mac/Library/Application Support/typora-user-images/image-20220215231944911.png)

```sql
# 读锁，所有人只能读，不能写
lock tables payment read;
# 写锁,当前客户端既能读也能写，其他客户端啥都不能干
lock tables payment write;
```

#### 元数据锁

这里面的shared_read和shared_write是读锁，是共享锁，但是读锁（就是这两个锁）和exclusive是互斥的

![image-20220215233042046](/Users/mac/Library/Application Support/typora-user-images/image-20220215233042046.png)

```sql
select object_type,object_schema,object_name,lock_type,lock_duration from performance_schema.metadata_locks;
```

### 意向锁

![image-20220215233812829](/Users/mac/Library/Application Support/typora-user-images/image-20220215233812829.png)

   ![image-20220428145430580](/Users/mac/Library/Application Support/typora-user-images/image-20220428145430580.png)

意向锁是行锁添加的

![image-20220215234040629](/Users/mac/Library/Application Support/typora-user-images/image-20220215234040629.png)

#### 兼容情况

![image-20220215234203821](/Users/mac/Library/Application Support/typora-user-images/image-20220215234203821.png)

![image-20220215234303859](/Users/mac/Library/Application Support/typora-user-images/image-20220215234303859.png)

```sql
select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from performance_schema.data_locks;

# 查询数据加上表锁
select * from payment where id = 1 lock in share mode;
```

### 行级锁

#### 介绍  

![image-20220215235904043](/Users/mac/Library/Application Support/typora-user-images/image-20220215235904043.png)

#### 行锁

![image-20220215235915663](/Users/mac/Library/Application Support/typora-user-images/image-20220215235915663.png)

![image-20220216000610912](/Users/mac/Library/Application Support/typora-user-images/image-20220216000610912.png)

![image-20220216000757783](/Users/mac/Library/Application Support/typora-user-images/image-20220216000757783.png)

![image-20220216001429378](/Users/mac/Library/Application Support/typora-user-images/image-20220216001429378.png) 

![image-20220216001441096](/Users/mac/Library/Application Support/typora-user-images/image-20220216001441096.png)

```sql
select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from performance_schema.data_locks;
```

#### 间隙锁/临建锁 

![image-20220216002705020](/Users/mac/Library/Application Support/typora-user-images/image-20220216002705020.png)

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220216003219423.png" alt="image-20220216003219423" style="zoom:50%;" />

## 八、InnoDB引擎

### 逻辑存储结构

![image-20220216150140674](/Users/mac/Library/Application Support/typora-user-images/image-20220216150140674.png)

### 架构

![image-20220216150352621](/Users/mac/Library/Application Support/typora-user-images/image-20220216150352621.png) 

#### 内存架构

![image-20220216150727827](/Users/mac/Library/Application Support/typora-user-images/image-20220216150727827.png)

![image-20220216151005053](/Users/mac/Library/Application Support/typora-user-images/image-20220216151005053.png)

![image-20220216151121833](/Users/mac/Library/Application Support/typora-user-images/image-20220216151121833.png)

![image-20220216151719515](/Users/mac/Library/Application Support/typora-user-images/image-20220216151719515.png)

#### 磁盘结构

![image-20220216155411296](/Users/mac/Library/Application Support/typora-user-images/image-20220216155411296.png)

![image-20220216160457080](/Users/mac/Library/Application Support/typora-user-images/image-20220216160457080.png)

```sql
# 创建表空间
create tablespace ts_dage add datafile 'payment.idb' engine=innodb;
# 绑定表空间
create table a(id int primary key auto_increment,name varchar(10)) engine=innodb tablespace ts_dage;
```

![image-20220216161324196](/Users/mac/Library/Application Support/typora-user-images/image-20220216161324196.png)

#### 后台线程

![image-20220216164212940](/Users/mac/Library/Application Support/typora-user-images/image-20220216164212940.png)

### 事务

![image-20220216164927288](/Users/mac/Library/Application Support/typora-user-images/image-20220216164927288.png)

#### 事务原理

![image-20220216165107023](/Users/mac/Library/Application Support/typora-user-images/image-20220216165107023.png)

![image-20220216165934752](/Users/mac/Library/Application Support/typora-user-images/image-20220216165934752.png)

![image-20220216170347683](/Users/mac/Library/Application Support/typora-user-images/image-20220216170347683.png)

### MVCC

#### 基本概念

![image-20220216170922783](/Users/mac/Library/Application Support/typora-user-images/image-20220216170922783.png)

![image-20220216172014804](/Users/mac/Library/Application Support/typora-user-images/image-20220216172014804.png)

![image-20220216172156711](/Users/mac/Library/Application Support/typora-user-images/image-20220216172156711.png)

![image-20220216173521742](/Users/mac/Library/Application Support/typora-user-images/image-20220216173521742.png)

![image-20220216174515882](/Users/mac/Library/Application Support/typora-user-images/image-20220216174515882.png)

![image-20220216174436312](/Users/mac/Library/Application Support/typora-user-images/image-20220216174436312.png)

读已提交隔离级别（RC）下，把具体的数值带入就行了

![image-20220216231718789](/Users/mac/Library/Application Support/typora-user-images/image-20220216231718789.png)

可重复读隔离级别下(RR)，仅在事务中第一次执行快照读时生成ReadView，后续复用该Readview

![image-20220216232022433](/Users/mac/Library/Application Support/typora-user-images/image-20220216232022433.png)

![image-20220216232441490](/Users/mac/Library/Application Support/typora-user-images/image-20220216232441490.png)

总结

![image-20220216232819931](/Users/mac/Library/Application Support/typora-user-images/image-20220216232819931.png)

## 九、MySQL管理

### 系统数据库

![image-20220216233413249](/Users/mac/Library/Application Support/typora-user-images/image-20220216233413249.png)

### 常用工具

#### mysql

![image-20220216233529828](/Users/mac/Library/Application Support/typora-user-images/image-20220216233529828.png)

```shell
mysql -h192.168.200.202 -P3306 -uroot -proot cloud -e 'select * from payment'
```

#### mysqladmin

![image-20220216234404766](/Users/mac/Library/Application Support/typora-user-images/image-20220216234404766.png)

```shell
mysqladmin -uroot -proot create db02
mysqladmin -uroot -proot drop db02
```

#### mysqlbinlog

![image-20220216235729988](/Users/mac/Library/Application Support/typora-user-images/image-20220216235729988.png)

#### mysqlshow

![image-20220217000247985](/Users/mac/Library/Application Support/typora-user-images/image-20220217000247985.png)

```shell
mysqlshow -uroot -proot --count
# 只想看db01这个数据库
mysqlshow -uroot -proot db01 --count
 
# 查看某个字段的话
# 只看db01这个数据库中course这个表下，id字段的详细信息
mysqlshow -uroot -proot db01 course id -i
```

#### mysqldump

![image-20220217000643358](/Users/mac/Library/Application Support/typora-user-images/image-20220217000643358.png)

```shell
mysqldump -uroot -proot db01 > db01.sql 
# 加上-T，.txt文件指定的目录要是mysql认为安全的，通过一个sql查
show variables like '%secure_file_priv%'
# 查到之后要把备份文件放到这个目录下
mysqldump -uroot -proot -T/varl/ib/mysql-files/ db01 score
```

#### Mysqlimport/source

![image-20220217001718124](/Users/mac/Library/Application Support/typora-user-images/image-20220217001718124.png)

```shell
mysqlimport -uroot -proot db01 /var/lib/mysql-files/score.txt
```

```sql
source /root/db01.sql
```

![image-20220217002145317](/Users/mac/Library/Application Support/typora-user-images/image-20220217002145317.png)

![image-20220523205158323](/Users/mac/Library/Application Support/typora-user-images/image-20220523205158323.png)

![image-20220523205331615](/Users/mac/Library/Application Support/typora-user-images/image-20220523205331615.png)
