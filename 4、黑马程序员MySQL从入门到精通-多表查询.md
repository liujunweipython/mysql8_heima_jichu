# 多表查询

# 一、多表关系

## 概述：

项目开发中，在进行数据库表结构设计时，会根据业务需求以及业务模块之间的关系，分析并设计表结构，由于业务之间相互关联，所以各个表结构之间也存在着各种联系，基本上分为三种：

1. 一对多(多对一)
   案例：部门 与 员工 的关系
   关系：一个部门对应多个员工，一个员工对应一个部门
   实现：在多的一方建立外键，指向一的那一方的主键
   ![image-20230113144847789](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/img/image-20230113144847789.png)

2. 多对多
   案例：学生和课程的关系
   关系：一个学生可以选修多门课程，一门课程也可以供多个学生选择
   实现：建立第三张中间表，中间表至少包含两个外键，分别关联两方主键
   ![image-20230113151615514](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/img/image-20230113151615514.png)

3. 一对一
   案例：用户 与 用户详情的关系

   关系：一对一关系，多用于单表拆分，将一张表的基础字段放在一张表中，其他详情字段放在另一张表中，以提升操作效率。

   实现：在任意一方加入外键，关联另一方的主键，并且设置外键为唯一的约束（unique）
   ![image-20230117172140817](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/img/image-20230117172140817.png)

   ```MySQL
   # 一对一操作
   create table tb_user(
       id int primary key auto_increment comment '主键id',
       name varchar(10) comment '姓名',
       age int comment '年龄',
       gender char(1) comment '1:男 ， 2:女',
       phone char(11) comment '手机号'
   ) comment '用户基本信息表';
   
   
   create table tb_user_edu(
       id int primary key auto_increment comment '主键id',
       degree varchar(20) comment '学历',
       major varchar(50) comment '专业',
       primaryschool varchar(50) comment '小学',
       middleschool varchar(50) comment '中学',
       university varchar(50) comment '大学',
       userid int unique comment '用户id',
       constraint fk_userid foreign key (userid) references tb_user(id)
   ) comment '用户教育信息表';
   
   
   #向用户基本信息表中插入数据
   insert into tb_user(id, name, age, gender, phone) values (null,'黄渤',45,'1','18800001111'),
                                                            (null,'冰冰',35,'1','18800002222'),
                                                            (null,'码云',55,'1','18800008888'),
                                                            (null,'李彦宏',50,'1','18800009999');
   
   #向用户教育信息表中插入数据
   insert into tb_user_edu(id, degree, major, primaryschool, middleschool, university, userid)
   VALUES
          (null,'本科','舞蹈','静安区第一小学','静安区第一中学','北京舞蹈学院',1),
          (null,'硕士','表演','朝阳区第一小学','朝阳区第一中学','北京电影学院',2),
          (null,'本科','英语','杭州市第一小学','杭州市第一中学','杭州师范大学',3),
          (null,'本科','应用数学','阳泉第一小学','静阳泉区第一中学','清华大学',4);
   ```

   

# 二、多表查询概述

## 概述：

多表查询：是指从多张表中查询数据
笛卡尔积：笛卡尔乘积是指在教学中，两个集合中，A集合和B集合的所有组合情况（在多表查询中需要消除无效的笛卡尔积）
![image-20230118134111216](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/img/image-20230118134111216.png)

## 多表查询演示

```MySQL
# 数据准备
create table dept(
    id   int auto_increment comment 'ID' primary key,
    name varchar(50) not null comment '部门名称'
)comment '部门表';


create table emp(
    id  int auto_increment comment 'ID' primary key,
    name varchar(50) not null comment '姓名',
    age  int comment '年龄',
    job varchar(20) comment '职位',
    salary int comment '薪资',
    entrydate date comment '入职时间',
    managerid int comment '直属领导ID',
    dept_id int comment '部门ID'
)comment '员工表';


alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id);

insert into dept (id, name)
VALUES
       (null,'研发部'),
       (null,'市场部'),
       (null,'财务部'),
       (null,'销售部'),
       (null,'总经办'),
       (null,'人事部');


insert into emp (id, name, age, job, salary, entrydate, managerid, dept_id)
values
(2, '张无忌', 20, '项目经理',12500, '2005-12-05', 1,1),
            (3, '杨逍', 33, '开发', 8400,'2000-11-03', 2,1),
            (4, '韦一笑', 48, '开发',11000, '2002-02-05', 2,1),
            (5, '常遇春', 43, '开发',10500, '2004-09-07', 3,1),
            (6, '小昭', 19, '程序员鼓励师',6600, '2004-10-12', 2,1),
            (7, '灭绝', 60, '财务总监',8500, '2002-09-12', 1,3),
            (8, '周芷若', 19, '会计',48000, '2006-06-02', 7,3),
            (9, '丁敏君', 23, '出纳',5250, '2009-05-13', 7,3),
            (10, '赵敏', 20, '市场部总监',12500, '2004-10-12', 1,2),
            (11, '鹿杖客', 56, '职员',3750, '2006-10-03', 10,2),
            (12, '鹤笔翁', 19, '职员',3750, '2007-05-09', 10,2),
            (13, '方东白', 19, '职员',5500, '2009-02-12', 10,2),
            (14, '张三丰', 88, '销售总监',14000, '2004-10-12', 1,4),
            (15, '俞莲舟', 38, '销售',4600, '2004-10-12', 14,4),
            (16, '宋远桥', 40, '销售',4600, '2004-10-12', 14,4),
            (17, '陈友谅', 42, null,2000, '2011-10-12', 1,null);
            
            
操作：
select * from emp,dept where dept.id=emp.dept_id;
```

- 多表查询分类：分为**连接查询** 和 **子查询**
- 连接查询又分：内连接、外连接、自连接
- 内连接：相当于查询A、B交集部分的数据
- 外连接又分 **左外连接** 和 **右外连接**：
  - 左外连接：查询左表所有数据，以及两张表交集部分的数据
  - 右外连接：查询有表所有数据，以及两张表交集部分的数据
- 自连接：当前表与自身的连接查询，自连接必须使用表别名
- 子查询：

![image-20230118135637487](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/img/image-20230118135637487.png)

# 三、内连接

## 1.连接查询-内连接

内连接是查询两张表交集的部分

C代表的绿色区域就是两张表的交集数据

![image-20230118140058203](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/img/image-20230118140058203.png)

### 1.1隐式内连接

**①、语法**

```mysql
select 字段列表 from 表名1,表名2 where 条件...;
```

②案例演练

```MySQL
-- 1. 查询每一个员工的姓名 , 及关联的部门的名称 (隐式内连接实现)
-- 表结构: emp , dept
-- 连接条件: emp.dept_id = dept.id
select emp.name,dept.name from emp,dept where emp.dept_id=dept.id;

如果表名很长可以使用别名的方式（#一旦用了别名，就不能使用表名了）：
select e.name,d.name from emp as e,dept as d where e.dept_id=d.id;
```



### 1.2.显式内连接

**①、语法**

```mysql
select 字段列表 from 表名1 inner join 表名2 on 条件...;
```

②案例演练

```MySQL
-- 2. 查询每一个员工的姓名 , 及关联的部门的名称 (显式内连接实现)  --- INNER JOIN ... ON ...
-- 表结构: emp , dept
-- 连接条件: emp.dept_id = dept.id
-- 关键字 inner可以省略
select emp.name,dept.name from emp inner join dept on emp.dept_id = dept.id;

如果表名很长可以使用别名的方式（#一旦用了别名，就不能使用表名了）：
select  e.name,d.name from emp as e inner join dept as d on e.dept_id = d.id;

```

## 2.连接查询-外连接

外连接查询包含 **左外连接** 和 **右外连接**

### 2.1左外连接（常用）

①、左外连接相当于查询表1(左表)的所有数据，包含表1和表2交集部门的数据

```mysql 
#关键字outer可以省略，案例中省略了
select 字段列表 from 表1名 left [outer] join 表2名 on 条件...;	
```

②、案例演练

```mysql
-- 1. 查询emp表的所有数据, 和对应的部门信息(左外连接)
-- 表结构: emp, dept
-- 连接条件: emp.dept_id = dept.id
select e.*,d.name from emp as e left join dept as d on e.dept_id = d.id;
```



### 2.2右外连接（用的少）

①、相当于查询表2(右表)的所有数据，包含表1和表2交集部门的数据

```MySQL
select 字段列表 from 表1名 right [outer] join 表2名 on 条件...;	
```

②、案例演练

```mysql
-- 2. 查询dept表的所有数据, 和对应的员工信息(右外连接)

select d.*, e.* from emp e right outer join dept d on e.dept_id = d.id;

select d.*, e.* from dept d left outer join emp e on e.dept_id = d.id;
```

![image-20230118151855609](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/img/image-20230118151855609.png)

## 3.自连接

**自连接查询，可以是内连接查询，也可以是外连接查询，自连接必须起别名**

### 3.1自连接语法

①、语法

```mysql
select 字段里列表 from 表A as 别名A join 表A as 别名B on 条件...;
```

②、案例演练

```mysql 
-- 自连接
-- 1. 查询员工 及其 所属领导的名字（#分析：领导也是员工，所以将emp表拆成两张表看，领导id(managerid)指向当前表的主键id）
-- 自连接必须起别名
select a.name,b.name from emp as a ,emp as b where a.managerid=b.id;（自连接可以是内连接）


-- 2. 查询所有员工 emp 及其领导的名字 emp , 如果员工没有领导, 也需要查询出来
select a.name as '员工',b.name as '领导' from emp as a left join emp as b on a.managerid=b.id; （自连接可以是外连接）
```

![image-20230118164955140](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/img/image-20230118164955140.png)

![image-20230118170006551](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/img/image-20230118170006551.png)

## 4.联合查询-union，union all

对于union联合查询，就是把多次查询的结果合并起来，形成一个新的查询结果集

### 4.1联合查询语法

**union all是将查询的结果直接合并**

**union是将查询的结果去重后合并**

**对于联合查询的多张表的字段列数必须保持一致，字段类型也需要保持一致。**

```mysql
select 字段列表 from 表A ...
union [all]
select 字段列表 from 表B ...;
```

### 4.2联合查询案例

```MySQL
-- 将薪资低于5000的员工，和 年龄大于50岁的员工全部查询出来
#因为鹿杖客员工同时满足薪资低于5000，年龄大于50岁，所以使用union all是将查询结果直接合并，结果中有两条鹿杖客的数据，因此使用union可以去除重复的数据

select name,salary,age from emp where salary<5000
union
select  name,salary,age from emp where  age>50;
```

# 六、子查询

## 1.DQL-介绍

DML英文全称是Data Query Lanageuage(数据查询语言)，数据查询语言，用来查询数据库中表的记录。

关键字：select

## 2.DQL-查询数据

### 2.1 查询数据语法

```mysql
这是sql语句的编写顺序
select 
		字段列表
from 
		表名列表
where 
		条件列表
group by
		分组字段列表  #分组通常配合聚合函数
having
		分组后条件列表
order by
		排序字段列表
limit
	分页参数;
```

- 基本查询
- 条件查询（where）
- 聚合函数（count,max,min,avg,sum）
- 分组查询（group by）
- 排序查询（order by）
- 分页查询（limit）

### 2.2DQL-基本查询

#### 2.21基本查询语法

```mysql
#查询多个字段
select 字段1,字段2,字段3,... from 表名;

#查询所有字段
select * from 表名;

#设置别名
select 字段1 [as 别名1], 字段2 [as 别名2]... from 表名;

#去除重复的记录
select distinct 字段列表 from 表名;
```

#### 2.22DQL-案例基本查询

```MySQL
数据准备：
创建表emp表：
CREATE TABLE emp (
  id int  COMMENT '编号',
  workno varchar(20)  COMMENT '工号',
  name varchar(10)  COMMENT '姓名',
  gender char(1)  COMMENT '性别',
  age tinyint unsigned  COMMENT '年龄',
  idcard varchar(18)  COMMENT '身份证号',
  workaddress varchar(50)  COMMENT '工作地址',
  entrydate date  COMMENT '入职时间'
) COMMENT='员工表'

为emp表插入数据:
 insert into emp (id, workno, name, gender, age, idcard, workaddress, entrydate)
 values (1,'1','柳岩','女',20,'123456789012345678','北京','2000-01-01'),
        (2, '2', '张无忌', '男', 18, '123456789012345670', '北京', '2005-09-01'),
        (3, '3', '韦一笑', '男', 38, '12345678971235670', '上海', '2005-08-01'),
        (4, '4', '赵敏', '女', 18, '12345675712384567', '北京', '2009-12-01'),
        (5, '5', '杨逍', '男', 28, '12345678931234567X', '北京', '2006-01-01'),
        (6, '6', '范瑶', '女', 40, '123456789212345670', '北京', '2005-05-01'),
        (7, '7', '小昭', '女', 16, '123456769012345678', '上海', '2007-07-01'),
        (8, '8', '黛绮丝', '女', 38, '123456157123645670', '天津', '2015-05-01'),
        (9, '9', '范凉凉', '女', 45, '123156789012345678', '北京', '2010-04-04'),
        (10, '10', '陈友谅', '男', 53, '123456789012345670', '上海', '2011-01-01'),
        (11, '11', '张士诚', '男', 55, '123567897123465670', '江苏', '2015-05-01'),
        (12, '12', '常遇春', '男', 32, '123446757152345670', '北京', '2004-02-01'),
        (13, '13', '张三丰', '男', 88, '123656789012345678', '江苏', '2020-11-01'),
        (14, '14', '灭绝', '女', 65, '123456719012345670', '西安', '2019-05-01'),
        (15, '15', '胡青牛', '男', 70, '12345674971234567X', '西安', '2018-04-01'),
        (16, '16', '周芷若', '女', 18, null, '北京', '2012-06-01');


基本查询实操：
#要求1：查询指定字段name, workno,age 返回
select name,workno,age from emp;

#要求2：查询所有字段返回
法1：select * from emp    #不建议使用*，建议使用法2
法2：select id, workno, name, gender, age, idcard, workaddress, entrydate from emp;

#要求3：查询所有员工的工作地址，起别名，as起别名，但是as可以省略
select workaddress as '工作地址' from emp;

#要求4：查询公司员工的上班地址(去重使用distinct关键字)
select distinct workaddress as '工作地址' from emp;
```

### 2.3DQL-where条件查询

#### 2.31条件查询语法

```mysql
select 字段列表 from 表名 where 条件列表;
#where后面的条件可以是一个也可以是多个
```

| 比较运算符              | 功能                                                         |
| ----------------------- | ------------------------------------------------------------ |
| >                       | 大于                                                         |
| >=                      | 大于等于                                                     |
| <                       | 小于                                                         |
| <=                      | 小于等于                                                     |
| =                       | 等于                                                         |
| <> 或者 !=              | 不等于                                                       |
| between  ... and ...    | 在某个范围之内(含最小、最大值)  在20~50范围：包含20和50      |
| not between ... and ... | 不在某个范围之内（不包含最大、最小值）不在20~50范围：不包含20和50 |
| in(...)                 | 在in之后的列表中的值，多选一                                 |
| like 占位符             | 模糊匹配（ _ 匹配单个字符，% 匹配任意个字符）                |
| is null                 | 判断某个字段是不是 NULL                                      |
| is not null             | 判断某个字段不是null                                         |

| 逻辑运算符 | 功能                         |
| ---------- | ---------------------------- |
| AND 或 &&  | 并且(多个条件同时成立)       |
| OR 或\|\|  | 或者（多个条件任意一个成立） |
| NOT 或 ！  | 非，不是                     |

#### 2.32DQL-案例条件查询

```mysql
#1.查询年龄等于88的员工信息
select * from emp where age=88;

#2.查询年龄小于20的员工信息
select * from emp where age<20;

#3.查询年龄小于等于20的员工信息
select * from emp where age<=20;

#4.查询没有身份证号的员工信息
select * from emp where idcard is null ;

#5.查询有身份证号的员工信息
select * from emp where idcard is  not null ;

#6.查询年龄不等于88 的员工信息
select * from emp where age<>88;
select * from emp where age!=88;

#7.查询年龄在15岁(包含) 到20(包含)之间的员工信息
select * from emp where   age >=15 and age<=20;
select * from emp where age between 15 and 20;
select * from emp where age in (15,16,17,18,19,20);

#8.查询性别为女 且年龄小于25岁的员工信息
select * from emp where gender='女' and age<25;

#9.查询年龄等于18 或者 20 或者 40 的员工信息
select * from emp where age in (18,20,40);  # 推荐
select * from emp where age=18 or age=20 or age=40;


#10.查询姓名为两个字的员工信息
select * from emp where name like "__";

#11.查询身份证号最后一位是x的员工信息
select * from emp where idcard like '%x';
```

### 2.4DQL-聚合函数

#### 2.41聚合函数介绍

**介绍：将一列数据作为一个整体，进行纵向计算。==聚合函数都是作用于表中的某一列==。**

常见的聚合函数：

| 函数  | 功能     |
| ----- | -------- |
| count | 统计数量 |
| max   | 最大值   |
| min   | 最小值   |
| avg   | 平均值   |
| sum   | 求和     |

#### 2.4.2聚合语法

```mysql
select 聚合函数(字段列表) from 表名;
#注意：null值不参与所有聚合函数的运算
```

#### 2.42DQL-案例聚合函数

```MYSQL
统计该企业员工数量
select count(idcard) from emp;

统计该企业员工的平均年龄
select avg(age) from emp;

统计该企业员工的最大年龄
select max(age) from emp;

统计该企业员工的最小年龄
select  min(age) from emp;

统计西安地区员工的年龄之和
select sum(age) from emp where workaddress='西安';
```

### 2.5DQL-group by分组查询

#### 2.51分组语法

```
select 字段列表 from 表名 [where 条件] group by 分组字段名 [having] 分组后过滤条件;
```

==where 与having的区别：==

- 执行时机不同：where是在分组之前进行过滤，不满足where条件，不参与分组；而having是分组之后对结果进行过滤。
- 判断条件不同：where不能对聚合函数进行判断，而having可以。
- 执行顺序：where>聚合函数>having
- 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义

#### 2.52DQL-分组查询案例

```mysql
根据性别分组，统计男性员工和女性员工的数量
select gender,count(id)  from emp group by gender;

根据性别分组，统计男性员工和女性员工的平均年龄
select avg(age) from emp group by gender;


查询年龄小于45的员工，并根据工作地址分组，获取员工数量大于等于3的工作地址
select workaddress,count(workaddress) from emp where age<45 group by workaddress having count(workaddress)>=3;
```

### 2.6DQL-order by排序查询

#### 2.61排序语法

```mysql
select 字段列表 from 表名 order by 字段1 排序方式1，字段2 排序方式2;  #多字段排序规则：如果第一个字段相同，则按照第二个字段排序

排序方式：
asc：升序（默认值）
desc：降序
```

**==注意：如果是多个字段排序，当第一个字段值相同时，才会根据第二个字段进行排序==**

#### 2.62DQL-案例排序查询

```mysql
根据年龄对公司的员工进行升序排序
select * from emp order by age asc;

根据入职时间，对员工进行降序排序
select  * from emp order by entrydate desc;

根据年龄对公司员工进行升序排序，年龄相同，在按照入职时间进行进行降序排序
select name,gender,age,entrydate from emp order by age asc,entrydate desc ;
```

### 2.7limit分页查询

#### 2.71分页语法

```mysql 
select 字段列表 from 表名 limit 起始索引，查询记录数;
```

==注意：==

- **起始索引从0开始，查询页码的起始索引=(查询页码-1)*每页显示记录数**
- 分页查询是数据库的方言，不同的数据库有不同的地方，MySQL中是limit
- 如果查询的是第一页数据，起始索引可以省略，直接简写为limit 10

#### 2.72DQL-案例分页查询

```mysql
查询第一页员工数据，每页展示10条记录
select * from emp limit 10


查询第二页员工数据，每页展示10条记录
select * from emp limit 10,10;
```

### 2.8DQL-案例练习

```mysql
按照要求完成如下DQL语句编写
1.查询年龄为20，21，22，23岁的女性员工信息
select id,workno,name,gender,age,idcard,workaddress,entrydate from emp where gender='女' and age in (20,21,22,23);

2.查询性别为男，并且年龄在20-40岁（含）以内的姓名为三个字的员工。
select * from emp where gender='男' and age between 20 and 40 and name like '___'

3.统计员工表中，年龄小于60岁的，男性员工和女性员工的人数
select gender, count(gender) from emp where age<60 group by gender;

4.查询所有年龄小于等于35岁员工的姓名和年龄，并且对查询结果按年龄升序排序，如果年龄相同按照入职时间降序排序
select  name,age from emp where age<=35 order by age asc ,entrydate desc;


5.查询性别为男，且年龄在20-40（含）以内的前5个员工信息，对查询的结果按照年龄升序排序，年龄相同按照入职时间升序排序 ##前五个员工就是每页5条数据库，查第一页，所以是limit 0 5; (0可以省略)
select * from emp where gender='男' and age between 20 and 40 order by age asc ,entrydate desc limit 5
```

### 2.9DQL语句执行顺序

①：先选择从哪个表查

②：在执行where条件

③：在执行group by 分组 和 having分组后条件

④：在执行select 返回哪些字段

⑤：在执行order by排序

⑥：最后执行limit分页

![image-20220413153508923](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/img/image-20220413153508923.png)

### 2.10DQL语句总结

![1649901212(1)](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/img/1649901212(1).jpg)

# 七、多表查询案例

### 1.DCL-介绍

DCL英文全称是Data Control Lanaguage（数据控制语言），用来管理数据库用户、控制数据的访问权限。

### 2.DCL-管理用户

#### 2.1查询用户

```mysql
use mysql;
select  * from user;
```



#### 2.2创建用户

```mysql 
create user '用户名'@'主机名' identified by '密码';
#创建的账号此时没有任何权限
#用户名表示创建的账号。
#主机名代表这个账号在哪个主机上等访问数据库，localhost表示只能本地访问；%表示任何主机都能访问。
#密码表示给这个用户设置的密码。

案例：创建用户itcast，只能在当前主机localhost访问，密码123456
create user 'itcast'@'localhost' identified by '123456';
创建用户heima，可以在任意主机访问数据库，密码123456
create user 'heima'@'%' identified by '123456';
```



#### 2.3修改用户密码

```mysql
MySQL5.6和MySQL8.0修改密码的语法不同：
MySQL5.6：

方式一：update user set Password=Password('新密码') where user='用户名';
方式二：set password for '用户名'@'主机名' = password('新密码');

MySQL8.0：
alter user '用户名'@'主机名' identified with mysql_native_password by '新密码';

案例：修改用户heima的访问密码1234
MySQL5.6：
update user set Password=Password('123456') where user='heima';
set password for 'heima'@'%' = password('1234');

MySQL8.0：
alter user 'heima'@'%' identified with mysql_native_password by '1234';
```

#### 2.4删除用户

```mysql
drop user '用户名'@'主机名';

案例：删除itcast@localhost 用户
drop  user 'itcast'@'localhost';
```

### 3.DCL-用户权限控制

#### 3.1常用权限

MySQL中定义了很多种权限，常用的有以下几种：

| 权限                 | 说明               |
| -------------------- | ------------------ |
| ALL，ALL  PRIVILEGES | 所有权限           |
| SELECT               | 查询数据           |
| INSERT               | 插入数据           |
| UPDATE               | 修改数据           |
| DELETE               | 删除数据           |
| ALTER                | 修改表             |
| DROP                 | 删除数据库/表/视图 |
| CREATE               | 创建数据库/表      |

#### 3.2查询权限

```MySQL
show grants for '用户名'@'主机名';
```



#### 3.3授予权限

```mysql
grant 权限列表 on 数据库名.表名 to '用户名'@'主机名';
#数据库名.表名 代表某个库的某个表
#数据库名.* 代表某个库的所有表
#*.* 代表s的所有表
```



#### 3.4撤销权限

```mysql
revoke 权限列表 on 数据库名.表名 from '用户名'@'主机名';
```

