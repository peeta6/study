#### MySQL高级

课程链接：https://www.bilibili.com/video/BV1Kr4y1i7ru?p=59

## 一、存储引擎

### 1、MySQL体系结构

![image-20220210163524783](/Users/mac/Library/Application Support/typora-user-images/image-20220210163524783.png)

![image-20220210163545165](/Users/mac/Library/Application Support/typora-user-images/image-20220210163545165.png)

### 2、存储引擎简介

![image-20220210163753900](/Users/mac/Library/Application Support/typora-user-images/image-20220210163753900.png)

```sql
# 查看表的创建语句
show create table account

# 查看当前数据库支持的引擎
show engines

# 创建一个表指定引擎是MyISAM
create table my_myisam(
    id int,
    name varchar(10)
) engine=MyISAM;

# 创建一个表指定引擎是MEMORY
create table my_mymemory(
    id int,
    name varchar(10)
) engine=MEMORY;
```

### 3、存储引擎的特点

#### innoDB

![image-20220213103754113](/Users/mac/Library/Application Support/typora-user-images/image-20220213103754113.png)

查看innodb_file_per_table

```sql
show variables like 'innodb_file_per_table';

# 到文件夹中找到表的存储空间，查看表结构、数据和索引
ibd2sdi [表名.ibd]
```

##### innoDB的逻辑存储结构

 ![image-20220213110014001](/Users/mac/Library/Application Support/typora-user-images/image-20220213110014001.png)

#### MyISAM

![image-20220213110443724](/Users/mac/Library/Application Support/typora-user-images/image-20220213110443724.png)

#### Memory

![image-20220213111317862](/Users/mac/Library/Application Support/typora-user-images/image-20220213111317862.png)



#### 对比

![image-20220213111430705](/Users/mac/Library/Application Support/typora-user-images/image-20220213111430705.png)

存储引擎选择

![image-20220213111636705](/Users/mac/Library/Application Support/typora-user-images/image-20220213111636705.png)

#### 小结

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220213111924162.png" alt="image-20220213111924162" style="zoom: 50%;" />

## 二、索引

```shell
# 在linux下安装mysql之后，查询自动生成的root用户密码
grep 'temporary password' /var/log/mysqld.log

# 修改用户密码
ALTER USER ‘rroot@localhost IDENTIFIED BY ‘root’;
# 失败了是因为默认的密码校验规则是最少8位，可以到官方文档去查找validate_password
# https://dev.mysql.com/doc/refman/8.0/en/validate-password-options-variables.html
# 设置密码复杂度
set global validate_password.policy = 0；
set global validate_password.length = 4；

# 创建用户
# 默认的root用户只能当前节点localhost访问，是无法远程访问的，我们还需要创建一个root账户，用户远程访问
create user 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'root';

# 给root用户分配所有权限
grant all on *.* to 'root'@'%';
```

### 索引概述

![image-20220213115724185](/Users/mac/Library/Application Support/typora-user-images/image-20220213115724185.png)

![image-20220213115923197](/Users/mac/Library/Application Support/typora-user-images/image-20220213115923197.png)

![image-20220213120103212](/Users/mac/Library/Application Support/typora-user-images/image-20220213120103212.png)

### 索引结构

![image-20220213120251639](/Users/mac/Library/Application Support/typora-user-images/image-20220213120251639.png)

![image-20220213120641047](/Users/mac/Library/Application Support/typora-user-images/image-20220213120641047.png)

二叉树

![image-20220213120931997](/Users/mac/Library/Application Support/typora-user-images/image-20220213120931997.png)

B-Tree

![image-20220213175548840](/Users/mac/Library/Application Support/typora-user-images/image-20220213175548840.png)

![image-20220213175901695](/Users/mac/Library/Application Support/typora-user-images/image-20220213175901695.png)

参考网站：https://www.cs.usfca.edu/~galles/visualization/Algorithms.html

B+Tree

![image-20220213180715211](/Users/mac/Library/Application Support/typora-user-images/image-20220213180715211.png)

![image-20220213180818699](/Users/mac/Library/Application Support/typora-user-images/image-20220213180818699.png)

Hash

![image-20220213181037450](/Users/mac/Library/Application Support/typora-user-images/image-20220213181037450.png)

![image-20220213181157322](/Users/mac/Library/Application Support/typora-user-images/image-20220213181157322.png)

为什么InnoDB存储引擎选择B+tree索引结构？

![image-20220213181514955](/Users/mac/Library/Application Support/typora-user-images/image-20220213181514955.png)

### 索引分类

![image-20220213181840447](/Users/mac/Library/Application Support/typora-user-images/image-20220213181840447.png)

![image-20220213182143847](/Users/mac/Library/Application Support/typora-user-images/image-20220213182143847.png)

![image-20220213182505001](/Users/mac/Library/Application Support/typora-user-images/image-20220213182505001.png)

![image-20220213183603957](/Users/mac/Library/Application Support/typora-user-images/image-20220213183603957.png)

### 索引语法

```sql
# 创建索引
CREATE [UNIQUE|FULLTEXT] INDEX index_name ON table_name(index_col_name,...);
# 查看索引
SHOW INDEX FROM table_name;
# 如果用行看不好，可以变成用列看
SHOW INDEX FROM table_name\G;
# 删除索引
DROP INDEX index_name ON table_name;

# 使用哪个数据库
use cloud;
# 查看数据库中的表
show tables;
```

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220213185234507.png" alt="image-20220213185234507" style="zoom:50%;" />

```sql
create index idx_user_name on tb_user(name);

create unique index idx_user_phone on tb_user(phone);

create index idx_user_pro_age_sta on tb_user(profession,age,status);

create index idx_email on tb_user(email);
```

### SQL性能分析

#### SQL执行频率

![image-20220213190109552](/Users/mac/Library/Application Support/typora-user-images/image-20220213190109552.png)

```sql
# 执行以下sql查看sql的执行次数,_有几个就代表填充几个字符,要用7个
show global status like 'Com_______'
```

#### 慢查询日志

![image-20220213190738002](/Users/mac/Library/Application Support/typora-user-images/image-20220213190738002.png)

```sql
# 查看慢查询日志开没开启命令
show variables like 'slow_query_log';
```

#### profile详情

![image-20220213212105900](/Users/mac/Library/Application Support/typora-user-images/image-20220213212105900.png)

```sql
# 查询have_profiling是否支持profile操作
select @@have_profiling;

# 查询profiling是不是已经开启
select @@profiling;

# 开启profiling
set profiling = 1；
```

![image-20220213212805600](/Users/mac/Library/Application Support/typora-user-images/image-20220213212805600.png)

```sql
# 查看每一条SQL的耗时情况
show profiles

# 查看指定query_id的SQL语句各个阶段的耗时情况
show profile for query [query_id];

# 查看指定query_id的SQL语句CPU的使用情况
show profile cpu for query [query_id]
```

#### explain执行计划

![image-20220213214142103](/Users/mac/Library/Application Support/typora-user-images/image-20220213214142103.png)

```shell
# 想要查询语句的执行信息，可以在查询语句前加explain或者desc
explain select * from [table_name] where id =1; 
```

通过主键或唯一索引去查询的话，type就是const，如果使用非唯一性索引就是ref

![image-20220213221816598](/Users/mac/Library/Application Support/typora-user-images/image-20220213221816598.png)

![image-20220213220809184](/Users/mac/Library/Application Support/typora-user-images/image-20220213220809184.png)

重点关注type、possible_keys,key,key_len,Extra这几个字段

### 索引使用原则

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220213221956040.png" alt="image-20220213221956040" style="zoom: 50%;" />

#### 最左前缀法则

这个跟查询语句的顺序没有关系，而是跟联合索引，有没有，有几个，有的那几个的左侧索引有没有的问题，

这个是在建立联合索引时的顺序决定的。

![image-20220213222614137](/Users/mac/Library/Application Support/typora-user-images/image-20220213222614137.png)

#### 范围查询

![image-20220213222937097](/Users/mac/Library/Application Support/typora-user-images/image-20220213222937097.png)

联合索引中在用到范围查询时尽量加上=号，如果只用>,<这样就会导致右侧的索引失效

#### 索引列运算

不要在索引列上进行运算操作，**索引将失效**。

```sql
# 如使用substring
select * from tb_user where substring(phone,10,2)='15';
```

#### 字符串不加引号

![image-20220214000158308](/Users/mac/Library/Application Support/typora-user-images/image-20220214000158308.png)

#### 模糊查询

![image-20220214000426353](/Users/mac/Library/Application Support/typora-user-images/image-20220214000426353.png)

#### or链接的条件

![image-20220214000816591](/Users/mac/Library/Application Support/typora-user-images/image-20220214000816591.png)

#### 数据分布影响

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220214001223046.png" alt="image-20220214001223046" style="zoom:33%;" />

还有比如

```sql
select * from tb_user where profession is null;
select * from tb_user where profession is not null;
```

#### sql提示

如果我要查询的语句，可能会用到两个索引，那么我要求他用那个索引查询，这个就是sql提示

![image-20220214112953291](/Users/mac/Library/Application Support/typora-user-images/image-20220214112953291.png)

```sql
# use index只是建议
explain select * from tb_user use index(idx_user_pro) where profession='软件工程';
# ignore index是不能用
explain select * from tb_user ignore index(idx_user_pro) where profession='软件工程';
# force index是一定要用
explain select * from tb_user force index(idx_user_pro) where profession='软件工程';
```

#### 覆盖索引

R![image-20220214114245397](/Users/mac/Library/Application Support/typora-user-images/image-20220214114245397.png)

![image-20220214120951780](/Users/mac/Library/Application Support/typora-user-images/image-20220214120951780.png)s

#### 前缀索引

![image-20220214121837497](/Users/mac/Library/Application Support/typora-user-images/image-20220214121837497.png)

```sql
create index [idx_xxxx] on [table_name](column(n));
select count(distinct email/count(*)) from tb_user;
select count(distinct substring(email,1,5)/count(*)) from tb_user;
```

![image-20220214122310677](/Users/mac/Library/Application Support/typora-user-images/image-20220214122310677.png)

单列索引和联合索引

![image-20220214123215016](/Users/mac/Library/Application Support/typora-user-images/image-20220214123215016.png)

![image-20220214123424481](/Users/mac/Library/Application Support/typora-user-images/image-20220214123424481.png)

### 索引设计原则

![image-20220214123926937](/Users/mac/Library/Application Support/typora-user-images/image-20220214123926937.png)

## 三、SQL优化

### 插入数据

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220214150207294.png" alt="image-20220214150207294" style="zoom:50%;" />

#### 大批量插入数据

![image-20220214153139305](/Users/mac/Library/Application Support/typora-user-images/image-20220214153139305.png)

```sql
mysql --local-infile -u root -p;
set global local_infile=1;
load data local infile '/root/sql1.log' into table 'tb_user' fields terminated by ',' lines terminated by '\n';
```

### 主键优化

#### 数据组织方式

![image-20220214153451390](/Users/mac/Library/Application Support/typora-user-images/image-20220214153451390.png)

#### 页分裂

![image-20220214153838071](/Users/mac/Library/Application Support/typora-user-images/image-20220214153838071.png)

![image-20220214153752987](/Users/mac/Library/Application Support/typora-user-images/image-20220214153752987.png)

#### 页合并

![image-20220214154110012](/Users/mac/Library/Application Support/typora-user-images/image-20220214154110012.png) 

#### 主键的设计原则

![image-20220214154338715](/Users/mac/Library/Application Support/typora-user-images/image-20220214154338715.png)

### order by优化

![image-20220214161237028](/Users/mac/Library/Application Support/typora-user-images/image-20220214161237028.png)

![image-20220214161344089](/Users/mac/Library/Application Support/typora-user-images/image-20220214161344089.png)

![image-20220214161635373](/Users/mac/Library/Application Support/typora-user-images/image-20220214161635373.png)

![image-20220214161758469](/Users/mac/Library/Application Support/typora-user-images/image-20220214161758469.png)

### group by优化

![image-20220214161834747](/Users/mac/Library/Application Support/typora-user-images/image-20220214161834747.png)

在分组操作时，可以通过索引来提高效率。

分组操作时，索引的使用也需要满足最左前缀法则。

### limit优化

![image-20220214162710350](/Users/mac/Library/Application Support/typora-user-images/image-20220214162710350.png)

### count优化

![image-20220214163052939](/Users/mac/Library/Application Support/typora-user-images/image-20220214163052939.png)

![image-20220214163348837](/Users/mac/Library/Application Support/typora-user-images/image-20220214163348837.png)

![image-20220214163708856](/Users/mac/Library/Application Support/typora-user-images/image-20220214163708856.png)

### update优化

执行update语句是一定要根据索引字段去更新，如果不使用索引字段，行锁就会升级为表锁。

![image-20220214164322169](/Users/mac/Library/Application Support/typora-user-images/image-20220214164322169.png)

### 总结

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220214165009110.png" alt="image-20220214165009110" style="zoom:50%;" />

Sql优化的手段

1、查询语句中尽量不要出现select *

2、尽量减少子查询，使用关联查询（left join ，right join , inner join ）去代替

3、减少使用IN或者NOT IN，使用exists，not exists或者关联查询语句替代

4、or查询尽量使用UNION或者union all代替(在确认没有重复数据或者不用剔除重复数据时，union all更好)

5、应尽量避免在where子句中使用!=或<>操作符，可能会放弃使用索引而进行全表扫描

6、尽量避免在where子句中对字段进行null判断，可能会放弃使用索引而进行全表扫描，可以将要设置成null的设置默认值0；

## 四、视图

### 介绍及基本语法

![image-20220214165238587](/Users/mac/Library/Application Support/typora-user-images/image-20220214165238587.png)

#### 语法

![image-20220214171316156](/Users/mac/Library/Application Support/typora-user-images/image-20220214171316156.png)

```sql
create [or replace] view 视图名称[(列名列表)] as select语句 [with[cascaded | local] check option]

# 创建视图
create or replace view stu_v_1 as select id,name from student where id <=10;

# 查询视图
# 查询创建视图的语句
show create view stu_v_1;
# 查看视图数据
select * from stu_v_1 where id <10;

# 修改视图
create or replace view stu_v_1 as select id from student where id <=10;
alter view stu_v_1 as select id,name from student where id <=10;

# 删除视图
drop view if exits stu_v_1
```

### 检查选项

![image-20220214220738021](/Users/mac/Library/Application Support/typora-user-images/image-20220214220738021.png)

```sql
create or replace view stu_v_1 as select id,serial from payment where id >10 ;
insert into stu_v_1(serial) values('dagegege');
insert into stu_v_1(serial) values('我嫩爹');

create or replace view stu_v_2 as select id,serial from stu_v_1 where id < 20  with cascaded check option ;
insert into stu_v_2(id,serial) values(20,'你爸爸');
insert into stu_v_2(id,serial) values(28,'我恁爹');

create or replace view stu_v_3 as select id,serial from stu_v_2 where id <= 15;
insert into stu_v_2(id,serial) values(12,'你爸爸');
insert into stu_v_2(id,serial) values(17,'我恁爹');
insert into stu_v_2(id,serial) values(27,'我恁爹');
```

local

![image-20220214221737130](/Users/mac/Library/Application Support/typora-user-images/image-20220214221737130.png)

local方式不会对依赖的v1视图添加检查选项，但是如果v1自己也有with local check option 的话，那就需要检查v1。上图是v1没有with local check option,所以插入大于15的数也没事。

### 视图的更新

![image-20220214222241934](/Users/mac/Library/Application Support/typora-user-images/image-20220214222241934.png)

```sql
# 举个例子
create or replace view v1 as select count(*) from payment;
# 因为时使用聚合函数创建的视图，所以下面的sql执行不了，不能更新
insert into v1 values(10);
```

### 视图的作用

   ![image-20220214222716156](/Users/mac/Library/Application Support/typora-user-images/image-20220214222716156.png)

### 案例

![image-20220214223246287](/Users/mac/Library/Application Support/typora-user-images/image-20220214223246287.png)

```sql
# 1
create or replace view tb_user_view as select id,name,profession,age,gender,status,createtime from tb_user;
# 2
create or replace view tb_stu_course_view select s.name student_name, s.no student_no, c.name course_name from student s, student_course sc, course c where s.id = sc.studentid and sc.courseid = c.id;
```

## 五、存储过程

### 介绍及基本语法

![image-20220214224407686](/Users/mac/Library/Application Support/typora-user-images/image-20220214224407686.png) 

![image-20220214224450705](/Users/mac/Library/Application Support/typora-user-images/image-20220214224450705.png)

#### 语法

 ![image-20220214224555901](/Users/mac/Library/Application Support/typora-user-images/image-20220214224555901.png)

```sql
# 创建
create procedure p1()
begin
	select count(*) from student;
end;

# 调用
call p1();
```

![image-20220214224847848](/Users/mac/Library/Application Support/typora-user-images/image-20220214224847848.png)

![image-20220214225504882](/Users/mac/Library/Application Support/typora-user-images/image-20220214225504882.png)

```sql
# 在命令行中，先设置结束符为$$，然后执行创建存储过程的命令，之后再把结束符换回来
delimiter $

create procedure p1()
begin
	select count(*) from student;
end$$

delimiter ;
```

```sql
# 查看
select * from information_schema.ROUTINES where ROUTINE_SCHEMA = 'cloud';
# 查看某个存储过程的定义
show create procedure p1;

# 删除
drop procedure if exists p1;
```

### 语法结构

#### 变量

##### 系统变量

![image-20220214231615280](/Users/mac/Library/Application Support/typora-user-images/image-20220214231615280.png)

```sql
# 变量：系统变量
# 查看系统变量没加global或者session默认是session级别的
show variables;

# 模糊查询
show session variables like 'auto%';
show global variables like 'auto%';

# 精确查询
select @@global.autocommit;

# 设置
set session autocommit = 0;
insert into payment(serial) values('你爸爸');
commit;# 因为设置了非自动提交，所以就需要commit。
```

##### 用户自定义变量

![image-20220214232638788](/Users/mac/Library/Application Support/typora-user-images/image-20220214232638788.png)

```sql
# 赋值
set @myname = 'dage';
set @myage := 10;
set @mygender :='男',@myhobby := 'java';

select @mycolor := 'red';
select count(*) into @mycount from payment;

# 使用
select @myname,@myage,@mygender,@myhobby;
select @mycolor,@mycount;
```

##### 局部变量

![image-20220214233403618](/Users/mac/Library/Application Support/typora-user-images/image-20220214233403618.png)

```sql
create procedure p2()
begin
	declare stu_count int default 0
	set stu_count := 100;
	select stu_count;
	select count(*) into stu_count from payment;
	select stu_count;
end;
```

#### if

![image-20220214234830271](/Users/mac/Library/Application Support/typora-user-images/image-20220214234830271.png)

```sql
create procedure p3()
begin
	declare score int default 58;
	declare result varchar(10);
	
	if score >= 85 then
		set result := '优秀';
	elseif score >= 60 then
		set result := '及格';
	else
  	set result := '不及格';
  end if;
  select result;
end;

call p3();
```

##### 参数

![image-20220214235013511](/Users/mac/Library/Application Support/typora-user-images/image-20220214235013511.png)

```sql
create procedure p4(in score int,out result varchar(10))
begin

    if score >= 85 then
        set result := '优秀';
    elseif score >= 60 then
        set result := '及格';
    else
        set result := '不及格';
    end if;
end;

call p4(12,@result);
select @result;

# 将传入的200分制的分数进行换算，换算成百分制，然后返回
create procedure p5(inout score double)
begin
    set score := score * 0.5;
end;

set @result := 120;
call p5(@result);
select @result;
```

#### case

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220215000638223.png" alt="image-20220215000638223" style="zoom: 50%;" />

<img src="/Users/mac/Library/Application Support/typora-user-images/image-20220215000737804.png" alt="image-20220215000737804" style="zoom:50%;" />

```sql
create procedure p6(in month int)
begin
	declare result varchar(!0);
	
	case
		when month >= 1 and month <= 3 then
			set result := '第一季度';
		when month >= 4 and month <= 6 then
			set result := '第二季度';
		when month >= 7 and month <= 9 then
			set result := '第三季度';
		when month >= 10 and month <= 12 then
			set result := '第四季度';
		else
			set result := '非法参数';
	end case;
	select concat('您输入的月份为:',month,',所属的季度为:',result);
end;
```

#### while

![image-20220215095844263](/Users/mac/Library/Application Support/typora-user-images/image-20220215095844263.png)

```sql
# 累计计算从1加到n
create procedure p7(in n int)
begin
    declare total int default 0;
    while n > 0 do
        set total := total + n;
        set n := n - 1;
    end while;
    select total;
end;

call p7(50);
```

#### repeat

![image-20220215102133840](/Users/mac/Library/Application Support/typora-user-images/image-20220215102133840.png)

```sql
# 计算从1加到n
create procedure p8(in n int)
begin
    declare total int default 0;
    repeat
        set total := total+n;
        set n := n-1;
    until  n<=0
    end repeat;
    select total;
end;

call p8(100);
```

#### loop

![image-20220215103915521](/Users/mac/Library/Application Support/typora-user-images/image-20220215103915521.png)

```sql
# 实现累加
create procedure p9(in n int)
begin
    declare total int default 0;

    sum:loop
        if n<=0 then
            leave sum;
        end if;

        set total := total +n ;
        set n := n-1;
    end loop sum;
    select total;
end;

call p9(100);

# 实现偶数累加
create procedure p10(in n int)
begin
    declare total int default 0;

    sum:loop
        if n<=0 then
            leave sum;
        end if;

        if n % 2 =1 then
            set n := n-1;
            iterate sum;
        end if;

        set total := total +n ;
        set n := n-1;
    end loop sum;
    select total;
end;

call p10(100);
```

#### 游标

![image-20220215105103607](/Users/mac/Library/Application Support/typora-user-images/image-20220215105103607.png)

#### 条件处理程序

![image-20220215112700118](/Users/mac/Library/Application Support/typora-user-images/image-20220215112700118.png)

```sql
create procedure p11(in n int)
begin
    declare udes varchar(10);
    declare u_cursor cursor for select serial from payment where id <= n;
    # 这句写成 declare exit handler for not found close u_cursor;也可以
    declare exit handler for Sqlstate '02000' close u_cursor;

    drop table if exists tb_user_pro;
    create table if not exists tb_user_pro(
        id int primary key auto_increment,
        des varchar(10)
    );

    open u_cursor;
    while true do
        fetch u_cursor into udes;
        insert into tb_user_pro values (null,udes);
    end while;
    close u_cursor;
end;

call p11(3);
```

#### 存储函数

![image-20220215113642485](/Users/mac/Library/Application Support/typora-user-images/image-20220215113642485.png)

```sql
create function fun1(n int)
returns int deterministic
begin
    declare total int default 0;

    while n >0 do
        set total := total +n;
        set n := n-1;
    end while;
    return total;
end;

select fun1(100);
```

## 六、触发器

### 介绍

![image-20220215114953574](/Users/mac/Library/Application Support/typora-user-images/image-20220215114953574.png)

### 语法

![image-20220215123514127](/Users/mac/Library/Application Support/typora-user-images/image-20220215123514127.png)

![image-20220215123701724](/Users/mac/Library/Application Support/typora-user-images/image-20220215123701724.png)

```sql
# 先建表
create table user_logs(
		id int not null primary key auto_increment,
    operation varchar(20) not null comment '操作类型，insert/update/delete',
    operate_time datetime not null comment '操作时间',
    operate_id int(11) not null comment '操作ID',
    operate_params varchar(500) comment '操作参数'
)engine=InnoDB default charset=utf8;

# 插入数据的触发器
create trigger pay_insert_trigger
    after insert on payment for each row
begin
    insert into user_logs(id,operation,operate_time,operate_id,operate_params) values
        (null,'insert',now(),new.id,concat('插入的数据内容为：id=',new.id,',serial=',new.serial));

end;
# 查看触发器
show triggers;
# 插入数据，去看use_log表中是否记录数据
insert into payment(serial) values('insert');
```

```sql
# 修改数据的触发器
create trigger pay_update_trigger
    after update on payment for each row
begin
    insert into user_logs(id,operation,operate_time,operate_id,operate_params) values
        (null,'update',now(),new.id,concat('更新的之前的数据内容为：id=',old.id,',serial=',old.serial,
            '|更新后的数据内容为：id=',new.id,',serial=',new.serial));

end;

update payment set serial='tiantian' where id = 4;
```

```sql
# 删除数据的触发器
create trigger pay_delete_trigger
    after delete on payment for each row
begin
    insert into user_logs(id,operation,operate_time,operate_id,operate_params) values
        (null,'delete',now(),old.id,concat('删除的之前的数据内容为：id=',old.id,',serial=',old.serial));
end;

delete from payment where id = 30;
```

