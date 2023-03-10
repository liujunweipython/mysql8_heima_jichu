# 约束

## 概述

1. 概念：约束是作用于表中字段上的规则，用于限制存储在表中的数据。

2. 目的：保证数据库中数据的正确、有效性和完整性

3. 分类：

| 约束                       | 描述                                                         | 关键字                            |
| -------------------------- | ------------------------------------------------------------ | --------------------------------- |
| 非空约束                   | 限制该字段的数据不能为null                                   | not null                          |
| 唯一约束                   | 保证该字段的所有数据都是唯一、不重复的（常用于用户名、手机号、身份证号） | unique                            |
| 主键约束                   | 主键是一行数据的唯一标识，要求非空且唯一（必须是非空、唯一的） | primary key（自增auto_increment） |
| 默认约束                   | 保存数据时，如果未指定该字段的值，则采用默认值               | default                           |
| 检查约束(8.0.16版本后支持) | 保证字段值满足某一个条件                                     | check                             |
| 外键约束                   | 用来让两张表的数据之间建立连接，保证数据的一致性和完整性,至少两张表 | foreign key                       |

注意：约束是作用于表中字段上的，可以在创建表、修改表的时候添加约束。

## 约束演示

1. 案例：根据需求，完成表结构的创建

   | 字段名 | 字段含义   | 字段类型    | 约束条件                | 约束关键字                  |
   | ------ | ---------- | ----------- | ----------------------- | --------------------------- |
   | id     | ID唯一标识 | int         | 主键，并且自动增长      | primary key，auto_increment |
   | name   | 姓名       | varchar(10) | 不为空，并且唯一        | not null，unique            |
   | age    | 年龄       | int         | 大于0，并且小于等于120  | check                       |
   | status | 状态       | char(1)     | 如果没有指定该值，默认1 | default                     |
   | gender | 性别       | char(1)     | 无                      |                             |

   ```mysql
   create table user(
       id int primary key auto_increment comment '主键，自增',
       name varchar(10) not null unique comment '姓名，不为空，唯一',
       age int check ( age>0 and age<=120) comment '年龄大于0并且小于等于120',
       status char(1) default '1' comment '状态如果不指定为1',
       gender char(1) comment '性别，无约束'
   )comment '用户表';
   ```
   
   

## 外键约束

1. 概念：外键约束用来让两张表的数据之间建立连接，从而保证数据的一致性和完整性。

![1650609354(1)](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/1650609354(1).jpg)

在主外键的关系中，具有外键的表称为子表，外键所关联的表称为父表

**注意：目前上述的两张表，在数据库层面，并未建立外键关联，所以是无法保证数据的一致性和完整性的。**

准备数据表-部门表和员工表：

```MySQL
-- 创建表部门表
create table dep(
    id int primary key auto_increment comment 'ID',
    name varchar(50) not null  comment '部门表'
) comment '部门表';

-- 向部门表中插入数据
insert into dep (id,name) values (1,'研发部'),(2,'市场部'),(3,'财务部'),(4,'销售部'),(5,'总经办');

-- 创建员工表
create table emp(
    id int primary key auto_increment comment 'id',
    name varchar(50) not null comment '姓名',
    age int check ( age>0 and age<=150 ) comment '年龄',
    job varchar(50) comment '职位',
    salary int comment '薪资',
    entrydate date comment '入职日期',
    managerif int comment '直属领导id',
    dept_id int comment '部门id'
) comment '员工表';

-- 向员工表中插入数据
INSERT INTO emp (name, age, job, salary, entrydate, managerif, dept_id) VALUES
('金庸', 66, '总裁', 20000, '2000-01-01', null, 5),
('张无忌', 20, '项目经理', 12500, '2005-12-05', 1, 1),
('杨逍', 33, '开发', 8400, '2000-11-03', 2, 1),
('韦一笑', 48, '开发', 11000, '2002-02-05', 2, 1),
('常遇春', 43, '开发', 10500, '2004-09-07', 3, 1),
('小昭', 19, '程序员鼓励师', 6600, '2004-10-12', 2, 1)
```

 

2. 添加外键语法

   - 添加语法-创建表的时候添加

     ```mysql
     create table 表名(
         字段名 数据类型,
         ....,
         [constraint] [外键名称] foreign key(外键字段名) references 主表(主表列名),
         [constraint] [外键名称] foreign key(外键字段名) references 主表(主表列名)
     );
     ```

   - 添加语法-表创建好后增加外键

     ```mysql
     alter table 表名 add constraint 外键名称 foreign key (外键字段) references 主表(主表列名);
     # 语法中的外键字段和主表列名的括号不能省略
     #add constraint 添加约束
     #foreign key  外键
     #references 参考，关联的主表
     #外键名称自定义，删除的时候会用到
     ```

   - 实操

     ```mysql
     alter table emp add constraint fk_emp_dept_id foreign key (dept_id) references dept(id);
     ```

     

3. 删除外键语法

   ```mysql
   alter table emp drop foreign key 外键名称;
   ```

   ```mysql
   删除上述添加的外键
   alter table emp drop foreign key fk_emp_dept_id;
   ```

4. 外键删除/更新行为

   | 行为关键字  | 说明                                                         |
   | ----------- | ------------------------------------------------------------ |
   | no action   | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键如果有则不允许删除/更新。是外键约束的默认行为（与restrict一致） |
   | restrict    | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键如果有则不允许删除/更新。（与no action一致） |
   | cascade     | 当在父表中删除/更新对应记录时，首先检查该记录是否有对应外键，如果有，则也删除 /更新外键在子表中的记录 |
   | set null    | 当在父表中删除对应记录时，首先检查该记录是否有对应外键，如果有，则设置子表中该外键值为null（这就要求该外键允许取null） |
   | set default | 父表有更新时，子表将外键列设置成一个默认值(Innodb不支持)     |

   ```mysql
   语法：
   alter table 表名 add constraint 外键名称 foreign key (外键字段) references 主表(主表列名) on update 行为关键字 on delete 行为关键字;  （行为关键字包含：no action、restrict、cascade、set null、set default）
   
   实操：cascade
   alter table emp add constraint  fk_emp_dept_id foreign key (dept_id) references dept(id) on update cascade on delete cascade ;
   
   
   实操：set null
   alter table emp add constraint fk_emp_tept_id foreign key (dept_id) references dept(id) on update set null on delete set null;
   
   ```

   