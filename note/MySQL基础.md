## SQL

### SQL通用语法

![image-20220225155459443](/Users/mac/Library/Application Support/typora-user-images/image-20220225155459443.png)

### SQL分类

![image-20220225155710227](/Users/mac/Library/Application Support/typora-user-images/image-20220225155710227.png)

#### DDL

DDL-数据库操作

![image-20220228104622841](/Users/mac/Library/Application Support/typora-user-images/image-20220228104622841.png)

DDL-表操作-查询

![image-20220225155916466](/Users/mac/Library/Application Support/typora-user-images/image-20220225155916466.png)

DDL-表操作-创建

![image-20220225160218075](/Users/mac/Library/Application Support/typora-user-images/image-20220225160218075.png)

##### 数据

![image-20220228100245988](/Users/mac/Library/Application Support/typora-user-images/image-20220228100245988.png)

![image-20220228100153421](/Users/mac/Library/Application Support/typora-user-images/image-20220228100153421.png)

![image-20220228100557969](/Users/mac/Library/Application Support/typora-user-images/image-20220228100557969.png)

DDL-表操作-添加字段

![image-20220228101409106](/Users/mac/Library/Application Support/typora-user-images/image-20220228101409106.png)

```sql
alter table emp add nickname varchar(20) comment '昵称';
```

DDL-表操作-修改

![image-20220228101639273](/Users/mac/Library/Application Support/typora-user-images/image-20220228101639273.png)

```sql
alter table emp change nickname username varchar(30) comment '用户名';
```

DDL-表操作-删除

![image-20220228102145418](/Users/mac/Library/Application Support/typora-user-images/image-20220228102145418.png)

```sql
alter table emp drop username;
```

DDL-表操作-修改表名

![image-20220228102523338](/Users/mac/Library/Application Support/typora-user-images/image-20220228102523338.png)

```sql
alter table emp rename to employee;
```

DDL-表操作-删除表

![image-20220228103149514](/Users/mac/Library/Application Support/typora-user-images/image-20220228103149514.png)

```sql
drop table if exists tb_user;
truncate table employee;
```

![image-20220228103812752](/Users/mac/Library/Application Support/typora-user-images/image-20220228103812752.png)

#### DML

![image-20220228105531966](/Users/mac/Library/Application Support/typora-user-images/image-20220228105531966.png)

DML-添加数据

![image-20220228110219950](/Users/mac/Library/Application Support/typora-user-images/image-20220228110219950.png)

```sql
insert into employee(id, workno, name, gender, age, idcard, entrydate) values(1,'1','dage','男',18,'123456789123456789','2022-02-28');

insert into employee values (2,'2','dd','男',19,'123456789123456389','2022-02-28'),(3,'3','bb','男',28,'123456789123356789','2022-02-28');

select * from employee;
```

DML-修改数据

![image-20220228111931639](/Users/mac/Library/Application Support/typora-user-images/image-20220228111931639.png)

```sql
update employee set name = 'di' where id = 2;
update employee set entrydate = '2018-01-01';
```

DML-删除数据

![image-20220228112302223](/Users/mac/Library/Application Support/typora-user-images/image-20220228112302223.png)

```sql
delete from employee where id = 3;
```

#### DQL

![image-20220228113250715](/Users/mac/Library/Application Support/typora-user-images/image-20220228113250715.png)

DQL-基本查询

![image-20220228113409533](/Users/mac/Library/Application Support/typora-user-images/image-20220228113409533.png)

```sql
select * from employee;
select name,workno,age from employee;
# as可以去除掉
select name as '姓名' from employee;
select name '姓名' from employee;
select distinct gender from employee;
```

DQL-条件查询

![image-20220228114201870](/Users/mac/Library/Application Support/typora-user-images/image-20220228114201870.png)

```sql
select * from employee where age = 18;
select * from employee where age < 20;
select * from employee where idcard is null;
select * from employee where idcard is not null;
select * from employee where age != 12;
select * from employee where age <> 12;
select * from employee where age >= 15 and age <= 20;
select * from employee where age between 15 and 20;
select * from employee where age in (18,19,20);
select * from employee where name like '____';
select * from employee where idcard like '%4';
```

DQL-聚合函数

![image-20220228122317284](/Users/mac/Library/Application Support/typora-user-images/image-20220228122317284.png)

```sql
select count(*) from employee;
select avg(age) from employee;
select max(age) from employee;
select min(age) from employee;
select sum(age) from employee;
```

DQL-分组查询

![image-20220228123841420](/Users/mac/Library/Application Support/typora-user-images/image-20220228123841420.png)

```sql
select gender ,count(*) from employee group by gender;
select gender ,avg(age) from employee group by gender;
select workaddress, count(*) from employee where age < 45 group by workaddress having count(*) > 3;
```

DQL-排序查询

![image-20220228162012995](/Users/mac/Library/Application Support/typora-user-images/image-20220228162012995.png)

DQL-分页查询

![image-20220228165514808](/Users/mac/Library/Application Support/typora-user-images/image-20220228165514808.png)

```sql
select * from emp limit 0,10;
select * from emp limit 10;

select * from emp limit 10,10;
```

![image-20220228171447966](/Users/mac/Library/Application Support/typora-user-images/image-20220228171447966.png)

```sql
select * from emp where gender = '女' and age in (18,19);
select * from emp where gender = '男' and age >=19 and age <= 40 and name like '____';
select gender ,count(*) from emp where age < 20 group by gender;
select name, age from emp where age <= 25 order by age, entrydate desc;
select * from emp where gender = '男' and  age >= 19 and age <= 30 order by age, entrydate limit 0,5; 
```

DQL-执行顺序

![image-20220228171626265](/Users/mac/Library/Application Support/typora-user-images/image-20220228171626265.png)

![image-20220228172434038](/Users/mac/Library/Application Support/typora-user-images/image-20220228172434038.png)

#### DCL

DCL-管理用户

![image-20220228174150028](/Users/mac/Library/Application Support/typora-user-images/image-20220228174150028.png)

```sql
use mysql;
select * from user;

create user 'dage'@'localhost' identified by '123456';

create user 'dagege'@'%'  identified by '12345';
alter user 'dagege'@'%' identified with mysql_native_password by '1234';

drop user 'dage'@'localhost';
drop user 'dagege'@'%';
```

DCL-权限控制

![image-20220228193317026](/Users/mac/Library/Application Support/typora-user-images/image-20220228193317026.png)

```sql
-- 查询权限
show grants for 'dagege'@'%';
-- 授予权限
grant all on cloud.* to 'dagege'@'%';
-- 回收权限
revoke all on  cloud.* from 'dagege'@'%';
```

![image-20220228193619241](/Users/mac/Library/Application Support/typora-user-images/image-20220228193619241.png)

## 函数

### 字符串函数

![image-20220228194506099](/Users/mac/Library/Application Support/typora-user-images/image-20220228194506099.png)

```sql
select concat('1','132','12');
select lower('JJJ');
select upper('jjj');
select lpad('01',5,'-');
select rpad('01',5,'-');
select trim('   se  f   ');
select substring('hello',1,5);
```

### 数值函数

![image-20220228200023004](/Users/mac/Library/Application Support/typora-user-images/image-20220228200023004.png)

```sql
select ceil(1.5);
select floor(1.9);
select mod(7,4);
select rand();
select round(2.34324,2);
select floor(rand()*1000000);
```

### 日期函数

![image-20220228231116977](/Users/mac/Library/Application Support/typora-user-images/image-20220228231116977.png)

```sql
liselect curdate();
select curtime();
select now();
select year(now());
select month(now());
select day(now());
select date_add(now(),INTERVAL 90 day);
select date_add(now(),INTERVAL 90 month);
select date_add(now(),INTERVAL 90 year);
select datediff('2022-10-1',now());
select name, datediff(curdate(),entrydate)  entrydays from emp order by entrydays;
```

### 流程函数

![image-20220228231238771](/Users/mac/Library/Application Support/typora-user-images/image-20220228231238771.png)

```sql
select if(true,'Ok','Error');
select ifnull('OK','Default');
select ifnull(null,'Default');
select name ,
       case workaddress when '北京' then '一线城市' when '上海' then '一线城市' else '二线城市' end as '工作城市'
from emp;
show create table emp;

select id,
       name,
       case when Math >=85 then '优秀' when Math>=60 then '及格' else '不及格' end as '数学',
       case when English >=85 then '优秀' when English>=60 then '及格' else '不及格' end as '英语',
       case when Chinese >=85 then '优秀' when Chinese>=60 then '及格' else '不及格' end as '语文'
from score;
```

![image-20220228232718094](/Users/mac/Library/Application Support/typora-user-images/image-20220228232718094.png)

## 约束

![image-20220228233040820](/Users/mac/Library/Application Support/typora-user-images/image-20220228233040820.png)

### 约束演示

```sql
create table user(
    id int primary key auto_increment comment '主键',
    name varchar(10) not null unique comment '姓名',
    age int check(age >0 && age <=120) comment '年龄',
    status char(1) default '1' comment '状态',
    gender char(1) comment '性别'
)comment '用户表' charset utf8mb4;

insert into user(name,age,status,gender) values ('Tom',10,'1','男'),('smith',10,'1','男');
insert into user(name,age,status,gender) values ('maria',5,'1','女'),('helena',10,'1','nv');
```

### 外键约束

![image-20220228235249523](/Users/mac/Library/Application Support/typora-user-images/image-20220228235249523.png)

![image-20220228235401739](/Users/mac/Library/Application Support/typora-user-images/image-20220228235401739.png)

![image-20220228235903725](/Users/mac/Library/Application Support/typora-user-images/image-20220228235903725.png)

```sql
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id);

alter table emp drop foreign key fk_emp_dept_id;
```

![image-20220301095635270](/Users/mac/Library/Application Support/typora-user-images/image-20220301095635270.png)

```sql
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id) on update cascade on delete cascade ;
alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id) on update set null on delete set null;
```

## 多表查询

一对多

![image-20220301100147836](/Users/mac/Library/Application Support/typora-user-images/image-20220301100147836.png)

多对多

![image-20220301100843522](/Users/mac/Library/Application Support/typora-user-images/image-20220301100843522.png)

一对一

![image-20220301100646121](/Users/mac/Library/Application Support/typora-user-images/image-20220301100646121.png)

![image-20220301101411560](/Users/mac/Library/Application Support/typora-user-images/image-20220301101411560.png)

多表查询分类

![image-20220301101540457](/Users/mac/Library/Application Support/typora-user-images/image-20220301101540457.png)

内连接

![image-20220301105936289](/Users/mac/Library/Application Support/typora-user-images/image-20220301105936289.png)

```sql
select emp.name,dept.name from emp ,dept where emp.dept_id = dept.id;
select e.name,d.name from emp e ,dept d where e.dept_id = d.id;

select e.name, d.name from emp e inner join dept d on e.dept_id=d.id;
select e.name, d.name from emp e join dept d on e.dept_id=d.id;
```

外连接

![image-20220301110755038](/Users/mac/Library/Application Support/typora-user-images/image-20220301110755038.png)

```sql
select e.*,d.name from emp e left outer join dept d on e.dept_id = d.id;
select e.*,d.name from emp e left join dept d on e.dept_id = d.id;

select d.*,e.name from emp e right join dept d on e.dept_id = d.id;
```

自连接

![image-20220301111632817](/Users/mac/Library/Application Support/typora-user-images/image-20220301111632817.png)

```sql
select a.name,b.name from emp a,emp b where a.managerid=b.id;
select a.name '员工' b.name '领导' from emp a left join emp b on a.manager.id=b.id;
```

联合查询

![image-20220301111938957](/Users/mac/Library/Application Support/typora-user-images/image-20220301111938957.png)

```sql
select * from emp where salary <5000
union all 
select * from emp where age >50;
```

子查询

![image-20220301112140088](/Users/mac/Library/Application Support/typora-user-images/image-20220301112140088.png)

标量子查询

![image-20220301112436597](/Users/mac/Library/Application Support/typora-user-images/image-20220301112436597.png)

```sql
select * from emp where dept_id = (select id from dept where name = '销售部');
select * from emp where entrydate > (select entrydate from emp where name = '东方白');
```

列子查询

  ![image-20220301112735873](/Users/mac/Library/Application Support/typora-user-images/image-20220301112735873.png)

```sql
select * from emp where dept_id in (select id from dept where name = '市场部' or name = '销售部');

select id from dept where name = '财务部';
select salary from emp where dept_id = (select id from dept where name = '财务部');
select * from emp where salary > all (select salary from emp where dept_id = (select id from dept where name = '财务部'));

select * from emp where salary > any (select salary from emp where dept_id = (select id from dept where name = '财务部'));
```

行子查询

![image-20220301113835334](/Users/mac/Library/Application Support/typora-user-images/image-20220301113835334.png)

```sql
select * from emp where (salary,managerid) = (select salary , managerid from emp where name = '张无忌');
```

表子查询

![image-20220301114711132](/Users/mac/Library/Application Support/typora-user-images/image-20220301114711132.png)

```sql
select * from emp where (job,salary) in (select job,salary from emp where name = '鹿杖客' or name = '宋远桥');

select * from (select * from emp where entrydate > '2006-01-01') e left join dept d on e.dept_id = d.id;
```

![image-20220301123952496](/Users/mac/Library/Application Support/typora-user-images/image-20220301123952496.png)

## 事务

![image-20220301140105823](/Users/mac/Library/Application Support/typora-user-images/image-20220301140105823.png)

![image-20220301140555764](/Users/mac/Library/Application Support/typora-user-images/image-20220301140555764.png)

![image-20220301141432834](/Users/mac/Library/Application Support/typora-user-images/image-20220301141432834.png)

![image-20220301142906208](/Users/mac/Library/Application Support/typora-user-images/image-20220301142906208.png)

 

![image-20220301143240172](/Users/mac/Library/Application Support/typora-user-images/image-20220301143240172.png)

![image-20220301144535997](/Users/mac/Library/Application Support/typora-user-images/image-20220301144535997.png)