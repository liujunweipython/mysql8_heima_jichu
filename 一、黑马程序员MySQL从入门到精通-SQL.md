# 一、SQL通用语法

## SQL通用语法

1. SQL语句可以单行或者多上书写，一分号结尾。
2. SQL语句可以使用空格/缩进来增强语句的可读性。
3. MySQL数据库的SQL语句不区分大小写，关键字建议使用大写。
4. 注释：
   - 单行注释：--注释内容 或 # 注释内容（MySQL特有）
   - 多行注释：/*    内容    */    中间写注释内容

# 二、SQL分类

- SQL语句分类

  | 分类 | 全称                         | 说明                                                   | 关键字                                                       |
  | ---- | ---------------------------- | ------------------------------------------------------ | ------------------------------------------------------------ |
  | DDL  | Data Definition Language     | 数据定义语言，用来定义数据库对象(数据库，表，字段)     | create(创建)，drop（删除） ，truncate（删除表结构，再创一张表），alter（修改） |
  | DML  | Data Manipulation Language   | 数据操作语言，用来对数据库表中的数据进行增删改         | insert（插入），update（更改），delete（删除）               |
  | DQL  | Data Query Language          | 数据查询语言，用来查询数据库中表的记录                 | select                                                       |
  | DCL  | Data Control Language        | 数据控制语言，用来创建数据库用户，控制数据库的访问权限 | grant，revoke,deny                                           |
  | TCL  | Transaction Control Language | 事务控制语言，                                         | begin,savepoint,rollback,commit                              |

# 三、DDl-数据定义语言

## 1.DDL-数据库操作

### 1.1查询所有的数据库

**①、查询所有数据库show databases;**

```mysql
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
3 rows in set (0.00 sec)
```

**②、查询当前在使用哪个数据库 select database();    相当于pwd**

```mysql
mysql> select database();
+------------+
| database() |
+------------+
| mysql      |
+------------+
1 row in set (0.00 sec)
```

### 1.2.创建数据库

**①、create database [if not exists] 数据库名 [default charset 字符集] [collate 排序规则];**   (中括号表示可选，如果不设置则使用默认的字符集和排序规则)

创建数据库时建议使用utf8mb4，因为utf8只能存储三个字节，有些特殊字符占4个字符

```MySQL
语法：create database [if not exists] 数据库名 [default charset 字符集] [collate 排序规则];
方括号中的选项为可选参数
if not exists #表示如果不存在则创建
default charset 字符集 # 创建库时设置库的字符集
collate 排序规则 

实操：
mysql> create database itcast;  #创建itcast数据库
Query OK, 1 row affected (0.01 sec)

mysql> show databases;  #查询是否创建成功
+--------------------+
| Database           |
+--------------------+
| information_schema |
| itcast             |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.00 sec)

mysql> create database itcast; #如果创建的库存在则会报错，此时使用if not exists
ERROR 1007 (HY000): Can't create database 'itcast'; database exists

mysql> create database if not exists itcast;  #if not exists表示如果不存在就创建，存在则不创建
Query OK, 1 row affected, 1 warning (0.00 sec)

mysql> create database itheima default charset utf8mb4; #创建字符集是utf8mb4的itheima库
Query OK, 1 row affected (0.00 sec)
```

### 1.3.删除

```MySQL
语法：drop database [if exists] 数据库名;
方括号中的选项为可选参数
if exists #表示如果存在则删除

实操：
mysql> drop database if exists test;   #如果test库存在则删除，如果不存在不报错
Query OK, 0 rows affected (0.00 sec)

mysql> drop database test; #不加if not exists 参数去删除不存在的库会报错
ERROR 1008 (HY000): Can't drop database 'test'; database doesn't exist
mysql> show databases;   #查看test库已经不存在了
+--------------------+
| Database           |
+--------------------+
| information_schema |
| itcast             |
| itheima            |
| mysql              |
| performance_schema |
+--------------------+
5 rows in set (0.00 sec)
```

### 1.4.使用某个数据库

```mysql
语法：use 数据库名;    相当于cd到某个路径
实操：
mysql> use itcast;  #选择要使用的数据库
Database changed

mysql> select database(); #查看当前所在的库
+------------+
| database() |
+------------+
| itcast     |
+------------+
1 row in set (0.00 sec)
```

## 2.DDL-表操作-查询

### 2.1查询当前数据库所有表

```mysql
语法：show tables; 前提是要先进入到这个库，使用use指令
mysql> use mysql;   先选择库
Database changed

mysql> show tables;   在查看库中所有的表
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| columns_priv              |
| db                        |
| event                     |
| func                      |
| general_log               |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| innodb_index_stats        |
| innodb_table_stats        |
| ndb_binlog_index          |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| servers                   |
| slave_master_info         |
| slave_relay_log_info      |
| slave_worker_info         |
| slow_log                  |
| tables_priv               |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| user                      |
+---------------------------+
28 rows in set (0.00 sec)

```



### 2.2查询表结构

```mysql
语法：desc 表名; 

查询tb_user表的结构
mysql> desc tb_user;  
+--------+-------------+------+-----+---------+-------+
| Field  | Type        | Null | Key | Default | Extra |
+--------+-------------+------+-----+---------+-------+
| id     | int(11)     | YES  |     | NULL    |       |
| name   | varchar(50) | YES  |     | NULL    |       |
| age    | int(11)     | YES  |     | NULL    |       |
| gendet | varchar(1)  | YES  |     | NULL    |       |
+--------+-------------+------+-----+---------+-------+
4 rows in set (0.00 sec)
```



### 2.3查询指定表的创建表语句

```mysql
语法：show create table 表名;

mysql> show create table tb_user;
+---------+--------------------------------------------------------------------------------------------------------+
| Table   | Create Table                                                                 --------------------------+
| tb_user | CREATE TABLE `tb_user` (
  `id` int(11) DEFAULT NULL COMMENT '编号',
  `name` varchar(50) DEFAULT NULL COMMENT '姓名',
  `age` int(11) DEFAULT NULL COMMENT '年龄',
  `gendet` varchar(1) DEFAULT NULL COMMENT '性别'
) ENGINE=InnoDB DEFAULT CHARSET=latin1 COMMENT='用户表'            |
+---------+--------------------------------------------------------------------------------------------------------+
1 row in set (0.01 sec)
```

## 3.DDL-表操作-创建

#### 3.1表创建语法

```mysql
注意：[...]为可选参数，最后一个字段后面没有逗号
create table 表明(
	字段1 字段1类型[comment 字段1注释],
	字段2 字段2类型[comment 字段2注释],
	字段3 字段3类型[comment 字段3注释],
	....
	字段n 字段n类型[comment 字段n注释]
)[comment 表注释];
```

![image-20220225143451655](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/imgimage-20220225143451655.png)

```mysql
实操：
mysql> create table tb_user(
    -> id int comment '编号',
    -> name varchar(50) comment '姓名',
    -> age int comment '年龄',
    -> gendet varchar(1) comment '性别'
    -> ) comment '用户表';
Query OK, 0 rows affected (0.07 sec)

mysql> show tables;
+------------------+
| Tables_in_itcast |
+------------------+
| tb_user          |
+------------------+
1 row in set (0.00 sec)
```

## 4.DDL-表操作-数据类型

### 4.1MySQL的数据类型

MySQL中支持的数据类型有很多，主要分为三类：数值类型、字符串类型、日期时间类型

参考：https://www.runoob.com/mysql/mysql-data-types.html

#### 4.11数值类型

有符号标识允许出现负数，无符号标识不允许出现符号

![image-20220225153552789](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/imgimage-20220225153552789.png)

```mysql
案例：
年龄：age int 或者 age tinyint unsigend   # 表示取值范围在0到255之间
分数：score double(4,1) #100.0这个成绩中4表示最长4位，1表示只允许出现1位小数
```



#### 4.12字符串类型

视频、音频、安装包属于二进制数据，这些是可以存储在MySQL中的，实际上并不建议这样做，会有专门的文件服务器存储

![image-20220225160052475](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/imgimage-20220225160052475.png)

```mysql
char和varchar类型后面都要指定最大长度
cahr(10)  #最多支持10个字符，超出会报错，如果少于10个也会占用10个字符的空间，性能好
varchar(10) #最多支持10个字符，超出会报错，根据实际情况占用相应的空间，性能较差，因为要计算实际使用空间
案例：
用户名 ：username varchar(50)  #
性别：gender char(1)
```

#### 4.13日期时间类型

![image-20220225161515224](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/imgimage-20220225161515224.png)

```mysql
案例：
生日：birthday date
```

### 4.2案例:根据需求创建表(设计合理的数据类型、长度)

设计一张员工表，要求如下：

1. 编号（纯数字）
2. 员工工号（字符串类型，长度不超过10位）
3. 员工姓名（字符串类型，长度不超过100位）
4. 性别（男/女，存储一个汉字）
5. 年龄（正常人年龄，不可存储负数）
6. 身份证号（二代身份证号均为18位，身份证中有X这样的字符）
7. 入职时间（取年月日即可）

```powershell
mysql> create table emp(
id int  comment '编号',
work_num varchar(10)  comment '工号',
name varchar(10)  comment '姓名',
gender char(1)  comment '性别',
age tinyint unsigned  comment '年龄',
id_card char(18) comment '身份证',
entry_time date comment '入职时间'
)comment '员工表';

注意：如果想要修改表中字段的类型或者新增某个字段，见4.3DDL-表操作-修改
```

### 4.3DDL-表操作

#### 4.31表中添加字段

```powershell
语法：alter table 表名 add 字段名 类型(长度) [comment 注释] [约束];  #方括号都是可选参数

案例：为emp表增加一个新的字段"昵称"为nickname，类型为varchar(20)
mysql> alter table emp add  nickname varchar(20) comment '昵称';
```

#### 4.32修改表中字段

```powershell
语法1：修改某一字段的类型
alter table 表名 modify 要修改类型的字段名 字段的新类型(长度);

语法2：修改字段名和字段类型
alter table 表名 change 旧字段名 新字段名 新字段名的类型(长度) [comment '注释'] [约束];

案例：
将emp表的nickname字段修改为username，类型为varchar(30)
mysql> alter table emp change nickname username varchar(30) comment '用户名';
mysql> desc emp; #检查是否修改成功
+------------+---------------------+------+-----+---------+-------+
| Field      | Type                | Null | Key | Default | Extra |
+------------+---------------------+------+-----+---------+-------+
| id         | int(11)             | YES  |     | NULL    |       |
| work_num   | varchar(10)         | YES  |     | NULL    |       |
| name       | varchar(10)         | YES  |     | NULL    |       |
| gender     | char(1)             | YES  |     | NULL    |       |
| age        | tinyint(3) unsigned | YES  |     | NULL    |       |
| id_card    | char(18)            | YES  |     | NULL    |       |
| entry_time | date                | YES  |     | NULL    |       |
| username   | varchar(30)         | YES  |     | NULL    |       |
+------------+---------------------+------+-----+---------+-------+
8 rows in set (0.00 sec)
```

#### 4.33删除表中字段

```powershell
语法：删除字段 # 这一列字段有数据也会一起被删除
alter table 表名 drop 字段名;

案例：将emp表中的username字段删除
mysql> alter table emp drop username;
Query OK, 0 rows affected (0.01 sec)
Records: 0  Duplicates: 0  Warnings: 0

mysql> desc emp; #检查是否删除成功
+------------+---------------------+------+-----+---------+-------+
| Field      | Type                | Null | Key | Default | Extra |
+------------+---------------------+------+-----+---------+-------+
| id         | int(11)             | YES  |     | NULL    |       |
| work_num   | varchar(10)         | YES  |     | NULL    |       |
| name       | varchar(10)         | YES  |     | NULL    |       |
| gender     | char(1)             | YES  |     | NULL    |       |
| age        | tinyint(3) unsigned | YES  |     | NULL    |       |
| id_card    | char(18)            | YES  |     | NULL    |       |
| entry_time | date                | YES  |     | NULL    |       |
+------------+---------------------+------+-----+---------+-------+
7 rows in set (0.00 sec)
```

#### 4.34修改表名

```powershell
语法：修改表名
alter table 旧表名 rename to 新表名;

案例：将emp表的表名修改为employee
mysql> alter table emp rename to employee;
Query OK, 0 rows affected (0.01 sec)

mysql> show tables; #查看表名是否修改成功
+------------------+
| Tables_in_itcast |
+------------------+
| employee         |
| tb_user          |
+------------------+
2 rows in set (0.00 sec)
```

#### 4.35删除表

```powershell
语法1：删除表 ，直接删除表和表中数据
drop table [if exists] 表名;

案例：将tb_user表删除
mysql> drop table tb_user;
Query OK, 0 rows affected (0.01 sec)

语法2：删除指定表，并重新创建该表,该语法会删除表中的数据，然后重新创建该表(了解) #删除表中数据，保留表结构
truncate table 表名;  

注意：在删除表时，表中的数据也会全部被删除
```

## 5.DDL语句总结

### 5.1DDL-数据库操作

```powershell
show databases;   #显示所有的数据库
create database 数据库名;  #创建新的数据库
use 数据库名;  #切换到某个库，要使用这个库
select database(); #查看当前所在的库
drop database 数据库名;  #删除某个数据库
```

### 5.2DDL-表操作

```powershell
show tables; #显示所有表
create table 表名(字段1 字段1类型,字段2 字段2类型); #创建表并定义字段,字段之间逗号分隔，最后一个字段不加逗号
desc 表名; #查看表中的字段
show create table 表名; # 查看创建表示的语句
alter table 表名 add/modify/change/drop/rename to ... ; #更新表的字段
参数：
add 新增字段
modify 修改字段的类型
change 修改某个字段名称和这个字段的类型
drop 删除某个字段
rename to 修改表名

drop table 表名 #删除表
```

# 四、MySQL图形化界面

![image-20220225174737756](C:\Users\liuju\Desktop\黑马-MySQL5.6\tupian\image-20220225174737756.png)

# 五、DML-数据操作语言

## 1.DML-介绍

DML英文全称是Data Manipulation Lanageuage(数据操作语言)，用来对数据库表中的数据记录进行增删改操作（操作表中的数据）。

添加数据（insert）

修改数据（update）

删除数据（delete）

## 2.DML-添加数据-insert

### 2.1 添加数据语法

```mysql
# 给指定字段添加数据，8个字段可以只设置其中的5个字段的数据
insert into 表名 (字段名1,字段名2,字段名3,...) values(值1,值2,值3,...);

#给全部字段添加数据
insert into 表名 values(值1,值2,值3,...);   #值要跟字段一一对应

#批量添加数据
insert into 表名 (字段名1,字段名2,字段名3,...) values(值1,值2,值3,...),(值1,值2,值3,...);
insert into 表名 values(值1,值2,值3,...),(值1,值2,值3,...);   #值要跟字段一一对应

#注意#
插入数据时，指定的字段顺序需要与值的顺序是一一对应的。
字符串和日期型数据应该包含在引号中。
插入的数据大小，应该在字段的规定范围内。
```

### 2.2实操

```mysql
# 给指定字段添加数据
insert into employee(id, wokrno, gender, age, idcard, entrydate, name) values(1,'1','男',10,'370687199408250931','2000-01-01','itcast'); 

#给全部字段添加数据
insert into employee values(2,'2','男',18,'370687198808250931','2005-01-01','张无忌');

#批量添加多条数据
insert into employee values(3,'3','男',38,'370687198808250925','2005-01-01','韦一笑'),(4,'4','女',18,'370687198808251431','2005-01-01','赵敏');
```

## 3.DML-修改数据-update

### 3.1修改数据语法

```mysql
update 表名 set 字段1=值1,字段2=值2,... [where 条件];
#修改语句的条件可以有，也可以没有，如果没有条件，则会修改整张表的所有数据。
```

### 3.2实操

![1649411490(1)](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/img1649411490(1).jpg)

```mysql
要求1：修改id为1的数据，将name修改为itheima
update employee set name='itheima' where id=1;

要求2：修改id为1，将name修改为小昭，gender修改为女
update employee set name='小昭',gender='女' where id=1;

要求3：将所有的员工入职日期修改为2008-01-01
update employee set entrydate='2008-01-01';
```

## 4.DML-删除数据-delete

### 4.1删除数据语法

```mysql
delete from 表名 [where 条件];
注意：
#delete语句的条件可以有，也可以没有，有条件则删除符合条件的数据，没有条件，则会删除整张表的所有数据。
#delete 语句不能删除某一个字段的值（如果要删除某一个字段的值，可以使用update将这个字段的值设为空）。
```

### 4.2实操

![1649411490(1)](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/img/1649411490(1).jpg)

```MySQL
#要求1：删除gender为女的员工
delete from employee where gender='女';

#要求2：删除所有员工(删除表中所有数据，但是不删除表)
方法一：delete from employee;
方法二：truncate table employee;
```

## 5.DML-语句总结

### 5.1添加数据

```MySQL
insert into 表名 (字段1，字段2，...) values (字段1的值，字段2的值，...),(字段1的值，字段2的值，...);
```

### 5.2修改数据

```mysql
update 表名 set 字段1=值1，字段2=值2 [where 条件];
```

### 5.3删除数据

```mysql
delete from 表名 [where 条件];
```

# 六、DQL-数据查询语言

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

#### 2.4.2语法

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

#### 2.51语法

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
学到这里
```

### 2.6DQL-order by排序查询

#### 2.61语法

```mysql
select 字段列表 from 表名 order by 字段1 排序方式1，字段2 排序方式2;
排序方式：
asc：升序（默认值）
desc：降序
```

==注意：如果是多个字段排序，当第一个字段值相同时，才会根据第二个字段进行排序==

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

#### 2.71语法

```mysql 
select 字段列表 from 表名 limit 起始索引，查询记录数;

```

==注意：==

- ==起始索引从0开始，查询页码的起始索引=(查询页码-1)*每页显示记录数==
- 分页查询是数据库额方言，不同的数据库有不同的实现，MySQL中是limit
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
1.查询年龄为20，21，22，23岁的员工信息
select id,workno,name,gender,age,idcard,workaddress,entrydate from emp where gender='女' and age in (20,21,22,23);

2.查询性别为男，并且年龄在20-40岁（含）以内的姓名为三个字的员工。
select * from emp where gender='男' and age between 20 and 40 and name like '___'

3.统计员工表中，年龄小于60岁的，男性员工和女性员工的人数
select gender, count(gender) from emp where age<60 group by gender;

4.查询所有年龄小于等于35岁员工的姓名和年龄，并且对查询结果按年龄升序排序，如果年龄相同按照入职时间降序排序
select  name,age from emp where age<=35 order by age asc ,entrydate desc;


5.查询性别为男，且年龄在20-40（含）以内的前5个员工信息，对查询的结果按照年龄升序排序，年龄相同按照入职时间升序排序
select * from emp where gender='男' and age between 20 and 40 order by age asc ,entrydate desc limit 5
```

### 2.9DQL语句执行顺序

①：先选择从哪个表查

②：在执行where条件

③：在执行group by 分组 和 having分组后条件

④：在执行select 返回哪些字段

⑤：在执行order by排序

⑥：最后执行limit分页

![image-20220413153508923](C:\Users\liuju\Desktop\黑马-MySQL5.6\tupian\image-20220413153508923.png)

### 2.10DQL语句总结

![1649901212(1)](C:\Users\liuju\Desktop\黑马-MySQL5.6\tupian\1649901212(1).jpg)

# 七、DCL-数据控制语言

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
drop 'itcast'@'localhost';
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

