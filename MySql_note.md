# MySql学习总结



## DDL语句(数据定义语句)

创建数据库

```sql
create database User
```

进入数据库

```sql
use User
```

创建表

```Sql
create table user(id int, name varchar(25), age int, article text) 
```

添加一列

```sql
alter table user add avatar varchar(255)
```

删除一列

```sql
alter table user drop avatar
```

修改字段类型

```sql
alter table user modify article varchar(255) 
```

修改表明

```sql
rename table user(原始表明) to person(新表明)
```

查看创建表细节

```sql
show create table user
```

修改表的列名

```sql
alter table user change name(原始列名) phone(新列名) varchare(11)
```

删除表

```sql
drop table user
```



## DML数据操作语句

插入数据

```sql
insert into table user(name,age) values('张三',13)
```

更新数据

```sql
update user set name='李四',age=30 where name='张三'
update user set age=age+1 where name='张三'
```

刷新权限

````sql
flush privileges
````

删除操作

```sql
delete from user where name='张胜'`
```

 

## DQL数据提取

条件查询

```sql
select * from user
select name from user
select name from user where id = 1
select name from user where id < 3 and name != '张三'
select name from user where name = '张三' or id=1
select name from user where id in(1,2,3)  -- id为1 2 3 中的任意都符合要求
select name from user where id is null  -- id为空的数据
select name from user where id is not null  -- id不为空的数据
select name from user where age between 18 and 20  -- 年龄在18到20的数据
```

模糊查询

```sql
select * from user where name like '__'  -- 下划线_ 占位符 表示单个字符
select * from user where name like '张%'  -- % 0到n个字符 以张开头的符合条件
select * from user where name like '%张_'  -- 倒数第二个字符为 张
```

字段控制查询

```sql
select distinct name from user  -- distinct 去重操作
select *,age+score from user   -- 结果多出一列  列名是 age+score 值为表达式的值  注意两个值都必须是数值类型
select *,ifnull(age,0)+score as total from user -- 如果age为null 那么就讲结果变为0  将增加的结果字段 命名为total
select name as rename from user  -- 结果集别名
```

排序

```sql
select * from user order by age  -- 默认是升序
select * from user order by age desc
select * from user order by age,id desc -- 当年龄相同时候 按照id降序排列
```

聚合函数

```sql
select count(*) from user  -- 所有数据的行数
select count(name),count(age) from user  -- name age 不为空的行数分别是多少
select sum(age) from user  -- 所有年龄的求和
select avg(age) from user  -- 平均年龄
select max(age),min(age) from user -- 最大年龄 和最小年龄
```

分组查询

- 执行顺序  select -> from -> where -> group by -> having -> order by -> limit

- having和where的区别
  1. having是在分组后对数据进行过滤  where是分组前对数据进行过滤
  2. having后面可以使用分组函数(统计函数)  where后面不能使用分组函数

```sql
select * from user group by department
select department,group_concat('name') from user group by gender -- 按照部分分类,且查询出每个分类的名字集合
select name,gender from user group by gender,name -- 一般select后面跟的字段, group by后面也会跟这个字段
select department,group_concat(salary),sum(salary) from user group by department -- 先按照部门分组,求和每一分组的所有薪资

-- Having
select department,sum(salary) from user group by department having sum(salary) >= 9000 
-- 查询工资总和大于9000的部门名称      主要用于先分组以后处理的动作使用 having
select department,sum(salary) from user where salary>2000 group by department having sum(salary)>6000 order by sum(salary) desc   
-- 查询工资大于2000,工资总和大于6000的部门名称以及工资和 并且按照工资总和降序排列

-- limit
select * from user limit 0,3    -- 0为开始的位置  3为一共有3条数据, 实际为分页查询

```



## 数据完整性

#### 实体完整性

- 主键约束—-数据不能为空,不能重复

  ```sql
  create table person(id int primary key,name varchar(50))
  create table person(id int, name varchar(50),age int,primary key(id))
  create table person(id bigint,name varchar(50),age int, primary key(id,name))
  -- 联合主键 id 和name 组合起来成为主键
  
  alter table student add constraint primary key(id)  -- 先创建表,后面再设置主键
  ```

- 唯一约束----数据不能重复

  ```sql
  create table person(id int primary key,name varchar(50) unique)     -- unique为不能重复
  ```

- 自动增长列 ——指定的列会自动增长

  ```sql
  create table person(id int primary key auto_increment,name varchar(50) unique) 
  -- auto_increment 自动增长
  ```

#### 域完整性

- 数据类型—创建表时候对应字段的数据类型约束

- 非空约束

  ```sql
  create table person(id int primary key,name varchar(50) unique not null)  -- 非空
  ```

- 默认值约束

  ```sql
  create table person(id int primary key,name varchar(50) unique not null, gender char(1) default '男')  -- 默认值 default
  ```

#### 参照完整性

- 建立表与表的一种关系(外键)

  ```sql
  create table score(id int, score int, p_id int, constraint sc_person foreign key(p_id) refrences person(id) )  
  -- sc_person 为约束的名称(可以省略不写) score表中的p_id依赖于 person中的id
  ```



## 多表查询

#### 简单夺标查询

- 建立多对多的关系表    学生表  老师表  关系表   需要这第三个 关系表来说面 老师和学生的关系

  ```sql
  CREATE TABLE student(sid INT PRIMARY KEY, name VARCHAR(25), age INT);
  CREATE TABLE teacher(tid INT PRIMARY KEY, NAME VARCHAR(25), age INT);
  CREATE TABLE tea_stu_ref(tid INT, sid INT);
  
  ALTER TABLE tea_stu_ref ADD CONSTRAINT FOREIGN KEY(tid) REFERENCES teacher(tid);
  ALTER TABLE tea_stu_ref ADD CONSTRAINT FOREIGN KEY(sid) REFERENCES student(sid);
  ```

-  合并查询结果集 —— 列数相同 切 列的数据结构必须相同

  ```sql
  select * from A union select * from B   -- 拼接查询结果,并且去重
  select * from A union ALL select * form B   -- 直接拼接查询结果
  ```

#### 连接查询

- 内连接

  ```sql
  select * from A,B    -- 笛卡尔积结果
  select * from student std,score sc     -- 查询别名
  --  99查询法
  select * from student std,score sc where std.sid = score.sid    -- 筛选两个表的关系
  -- 内连接 等值连接
  select st.id,st.name from stu st JOIN score sc ON std.sid = score.sid where sc.score>60 and st.gender='男'
  -- 非等值连接
  select st.name,sc.score,c.name from stu st,score sc,course c where st.id = sc.sid and sc.cid = c.cid
  select * from stu st join score sc on st.id = sc.sid join course c on sc.cid = c.id
  ```

- 外链接

  - 左外链接

  - ```sql
    select * from stu st left join score sc on st.id = sc.sid  -- 左边的表全部数据,右边的满足条件才出来
    ```

  - 右外连接

    ```sql
    select * from stu st right join score sc on st.id = sc.sid  -- 右边的表全部数据,左边的满足条件才出来
    ```

  - 自然连接—两个表中必须有字段名相同 且 字段类型相同

    ```sql
    select * from stu natural join score   -- 效果等同于
    select * from stu st,score sc where st.sid = sc.sid
    ```

#### 子查询  放在 where后面 和放在from后面

```sql
-- 将子查询的结果 做为上级查询的条件  (查询跟项羽在同一部门的所有员工)
select ename,deptno from emp where deptno = (select deptno from emp where name='项羽')
select ename,deptno from (select * from emp where deptno=30)
-- 查询工作和工资都跟妲己相同的所有员工信息
select * from emp where (job,salary) in (select job,salary from emp where ename = '妲己')
select * from emp e,(select job,salary from emp where ename = '妲己') r where e.job = r.job and e.salary = r.salary  -- 将结果作为第二张表 联合查询

```

自连接

 ```sql
-- 把自己当做另外一张表做连接
select e1.empno,e2.empno from emp e1,emp e2 where e1.mgr = e2.empno
 ```



## 常用函数

字符串函数

- concat(s1,s2...sn)
- Insert(str,x,y,instr) 原字符串,开始位置,替换的长度,用于替换的字符串
- lower(str) upper(str) 字符串变为 大小写
- left(str,idx)  right(str,idx) 从左边获取几个 右边获取几个
- LPAD(str,len,padstr)   RPADstr,len,padstr()  len 最大值,左边(右边)加上padstr数值 直到达到最大长度
- LTRIM(str) RTRIM(str) 去掉左边右边的空格
- REPEAT('123',4) 拼接前面的'123'  4次
- REPLACE('ABCD','C','FF') 替换 C 为 FF
- substr('ABCDEFG',3,2) 从第三个位子 截取两个字符

数字函数

- ABS(X) 绝对值
- CEIL(x) 向上取整
- FLOOR(x) 向下取整
- MOD(x,y) 取模  MOD (9,2) 9模2 余1
- RAND()   0~1的随机数

日期和时间

- CURDATE() 当前时间,只包含年月日

- CURTIME() 当前时间,只包含时分秒

- NOW()  当前时间,包含年月日 时分秒

- UNIX_TIMESTAMP()  1970年的毫秒数

- FROM_UNIXTIME(unixtime)  时间戳转日期

- WEEK(DATE)

- YEAR(DATE)

- HOUR(TIME)

- MINUTE(TIME)

- DATE_FORMAT(date,fmt)  日期格式化 

  ```sql
  select DATE_FORMAT(now(),'%M,%D,%Y')
  ```

- DATE_ADD(date,interval expr type)

  ```sql
  select DATE_ADD(now(),interval 31 day)
  select DATE_ADD(now(),interval 31 week)
  ```

- DATEDIFF(date1,date2)

流程函数

- IF(value,t,f)  如果value是真,返回t,否则返回f
- IFNULL(value1,value2) 如果value1不为空返回value1的值,否则返回value2的值
- CASE WHEN THEN END

其他常用函数

- DATABASE() 返回当前数据库名称
- VERSION() 返回数据库版本
- USER() 返回当前的用户
- PASSWORD(STR)  对Str进行加密
- MD5(str) 对 str进行MD5



## 事务

mysql默认开启事务, 每个语句都是一个事务

- 原子性
- 一致性
- 隔离性
- 持久性

使用

```sql
select @@globale.tx_isolation,@@tx_isolation  --查看隔离级别
set global transaction isolation level read committed  --设置隔离级别(隔离级别有下面的四种)
start transaction -- 开事务
-- 执行需要的sql(多条sql语句)
commit  --提交事务
rollback --回滚事务  没有commit的事务 回滚到 事务开启之前的状态
```

事务隔离级别

- Read uncommitted  容易造成 脏读

- Read commite 必须等到事务提交以后 别的用户才能读取数据

- Repeatable read (数据库默认情况) 不可重复读 当事务开启 但是没有提交的时候读取数据为事务开启前状态

- Serializable


## 权限

创建用户

````sql
create user 'myxq'@'localhost' identified by 'password' 
-- @之前是用户名 之后是访问主机,如果是'myxq'@'%'则为所有ip都可以访问
````

删除用户

````sql
drop user 'myxq'@'localhost'
````

权限操作

```sql
grant all privileges on *.* to myxq@localhost identified by '1234' with grant option
-- with grant option 意思为该用户是否具有分配给别人对应权限的能力 *.* 表示所有数据库 中的 所有表
flush privileges  -- 刷新权限 立即生效

grant insert,update,select on *.* to myxq@localhost identified by '1234' -- 只分配 增 改 查
show grants for myxq@localhost -- 查看指定用户的权限
revoke insert,update ON *.* from myxq@localhost  --删除指定用户的指定权限
```



## 视图

视图: 一个虚拟表,就是一个查询结果 保存起来 

创建视图

- Merge 替换试创建 —— 修改视图数据,会直接修改真实数据

  执行语句时候 会隐士转换为 对原有的表进行操作的sql语句

- ```
  create view ALGORITHM = MERGE emp_salary_view as (select * from emp where salary > 2000) 
  -- ALGORITHM = MERGE 可以不写 默认就是按照这个方式创建
  create view emp_salary_view as (select * from emp where salary > 2000) with check option
  -- with check option 表示更新视图的时候 条件必须满足,如果不满足 不容许更新
  ```

- Temptable 具化试创建 

  在内存中临时创建了一个新的视图表  操作的时候直接对新的视图操作

  ```
  
  ```

- Undefined

使用视图—— 增,删,改,查

```sql
select * from emp_salary_view where job = '经理'
```

修改视图

```sql
create or replace view emp_salary_view as (select * from emp)
-- 只要视图数据不来源于基表 那么视图更改就不能影响基表数据
```

删除视图

```sql
drop view emp_salary_view
```



##  存储过程

