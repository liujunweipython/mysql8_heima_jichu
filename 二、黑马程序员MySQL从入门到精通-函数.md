# 函数-讲解

**函数**是指一段可以直接被另一段程序调用的程序或代码。MySQL中内置了许多函数，我们直接调用即可。

## 一、字符串函数

### 1.1字符串函数介绍

MySQL中内置了很多字符串函数，常用的几个如下：

| 函数                     | 功能                                                         |
| ------------------------ | ------------------------------------------------------------ |
| concat(S1,S2,...Sn)      | 字符串拼接，将S1,S2,...Sn拼接成一个字符串                    |
| lower(str)               | 将字符串str全部转为小写                                      |
| upper(srt)               | 将字符串str全部转为大写                                      |
| lpad(str,n,pad)          | 左填充，用字符串pad对str的左边进行填充，达到n个字符串长度    |
| rpad(str,n,pad)          | 右填充，用字符串pad对str的右边进行填充，达到n个字符串长度    |
| trim(str)                | 去掉字符串头部和尾部的空格                                   |
| substring(str,start,len) | 返回从字符串str从start位置起的len个长度的字符串==(索引是从1开始)== |

```mysql
select 函数(参数);
```

### 1.2字符串函数案例

```MySQL
select concat('hello',' mysql');   # 拼接hello和mysql两个字符串

select lower('HellO');   #将字符串HellO转换成小写

select upper('Hello');    #将字符串HellO转换成大写

select lpad('1',5,'0');  # 在字符串的左边进行填充，长度为5，填充为0的字符

select rpad('1',5,'0');   # 在字符串的右边进行填充，长度为5，填充为0的字符

select trim('   hello word ');   #去掉字符串   hello word 头部和尾部的空格

select substring('hello word',1,5)    # 截取字符串的hello word，从1索引开始截，长度为5

实操：由于业务需求的变更，企业员工的工号，统一为5位数，目前不足五位数的全部在前面补0 比如：一号员工的工号应该为00001;
update emp set workno=lpad(workno,5,'0') ;
```



## 二、数值函数

### 2.1数值函数介绍

常见的数值函数如下：

| 函数       | 功能                               |
| ---------- | ---------------------------------- |
| ceil(x)    | 向上取整                           |
| floor(x)   | 向下取整                           |
| mod(x,y)   | 取x/y的模（余数）                  |
| rand()     | 返回0~1内的随机数                  |
| round(x,y) | 求参数x的四舍五入的值，保留y位小数 |

### 2.2数值函数案例

```mysql 
select ceil(1.1)   #向上取整，结果是2

select floor(2.2)   #向下取整，结果是2

select mod(7,3)   #取模  7/3的余数是1，结果1

select rand()  # 返回一个0~1之间的随机数

select round(6.224,3)  #将6.224四舍五入，保留3位小数，结果是 6.224


实操：通过数据库函数，生成一个六位数的随机验证码。
法一：select substring(floor(rand()*1000000),1,6);
法二：select lpad(round(rand()*1000000),6,'0');
```

## 三、日期函数

### 3.1日期函数介绍

| 函数                              | 功能                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| curdate()                         | 返回当前日期                                                 |
| curtime()                         | 返回当前时间                                                 |
| now()                             | 返回当前日期和时间                                           |
| year(date)                        | 获取指定date的年份                                           |
| month(date)                       | 获取指定date的月份                                           |
| dat(date)                         | 获取指定date的日期                                           |
| date_add(date,interval expr type) | 返回一个日期/时间值加上一个时间间隔expr   后的时间值(date时间值，interval固定写法，expr是时间间隔，type时间类型：year，month，day) |
| datediff(date1,date2)             | 返回起始时间date1和结束时间date2之间的天数(date1-date2)      |

### 3.2日期函数案例

```mysql
select curdate();  #显示当前日期的年月日 结果2020-04-19

select curtime();  #显示当前日期的时分秒 结果17:54:47

select now();   #显示当前日期的年月日时分秒 结果2022-04-19 17:55:25

select year(curdate());     #显示当前日期的年份 结果 2022

select month(curdate());  #显示当前日期的月份 结果 4

select day(now());   #显示当前日期的日，结果19

select date_add(now(),interval 30 day );   # 在当前时间往后推30天，结果2022-03-20 17:57:47

select datediff('2022-12-12','2022-11-12');  #获取两个日期相差的天数，2022-12-12减2022-11-12

实操：查询所有员工的入职天数，并根据入职天数倒序排序
select name as '姓名',datediff(curdate(),entrydate) as 'enjoy_day' from emp order by enjoy_day desc ;
```

## 四、流程函数

### 4.1流程控制函数介绍

流程函数也是很常用的一类函数，可以在sql语句中实现条件筛选，从而提高语句的效率

| 函数                                                     | 功能                                                     |
| -------------------------------------------------------- | -------------------------------------------------------- |
| if(value,t,f)                                            | 如果value为true，则返回t，否则返回f                      |
| ifnull(value1,value2)                                    | 如果value不为空，返回value1，否则返回value2              |
| case when [val1] then [res1]...else [default] end        | 如果val1为true，返回res1，...否则返回default默认值       |
| case [expr] when [val1] then [res1]...else [default] end | 如果expr的值等于val1，返回res1，...否则返回default默认值 |

### 4.2流程控制函数案例

```mysql
实操：查询emp表的员工姓名和工作地址（北京/上海显示一线城市，其他地区显示二线城市）
select name,case workaddress when '北京' then '一线城市' when '上海' then '一线城市' else '二线城市' end as '工作地址' from emp;
```

## 五、练习

### 5.1根据需求完成以下SQL编写

```mysql
案例：统计班级各个学员的成绩，展示的规则如下：
>=85,优秀
>=60,及格
否则，不及格

select id,
       name,
       case  when math>=85 then '优秀' when math>=60 then '及格' else '不及格' end as 'math',
       case  when english>=85 then '优秀' when english>=60 then '及格' else '不及格' end as 'english',
       case  when chinese>=85 then '优秀' when chinese>=60 then '及格' else '不及格' end as 'chinese'
from score;
```

## 六、函数总结

### 6.1字符串函数

```mysql
concat,lower,upper,lpad,rpad,trim,substring
```

### 6.2数值函数

```
ceil,floor,mod,rand,round
```

### 6.3日期函数

```
curdate,curtime,now,year,month,day,date_add,dateiff
```

### 6.4流程函数

```mysql
if,ifnull,case [...] when ... then ...else ... end
```