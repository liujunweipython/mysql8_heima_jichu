# MySQL管理

## 常用工具

### 1.mysql

该mysql不是指mysql服务，而是指mysql的客户端工具

```mysql
语法：
mysql [option] [database]

选项：
-u,--user=name              #指定用户名
-p,--password[=name]        #指定密码
-h,--host=name              #指定服务器ip或域名
-P,--port=port              #指定连接数据库的端口(大写)
-e,--execute=name           #指定SQL语句并退出
```

**-e选项可以在mysql客户端执行sql语句，而不用连接到MySQL数据库在执行，对于一些批处理脚本，这种方式尤其方便**

```mysql
实例：
mysql -uroot -p123456 db01 -e "select * from stu";
# db01指定查询的数据库
```

### 2.mysqladmin

mysqladmin 是一个执行管理操作的客户端程序。可以用它来检查服务器的配置和当前状态、创建并删除数据库等。

```mysql
通过帮助文档查看选项
mysqladmin --help
  create databasename	Create a new database
  debug			Instruct server to write debug information to log
  drop databasename	Delete a database and all its tables
  extended-status       Gives an extended status message from the server
  flush-hosts           Flush all cached hosts
  flush-logs            Flush all logs
  flush-status		Clear status variables
  flush-tables          Flush all tables
  flush-threads         Flush the thread cache
  flush-privileges      Reload grant tables (same as reload)
  kill id,id,...	Kill mysql threads
  password [new-password] Change old password to new-password in current format
  old-password [new-password] Change old password to new-password in old format
  ping			Check if mysqld is alive
  processlist		Show list of active threads in server
  reload		Reload grant tables
  refresh		Flush all tables and close and open logfiles
  shutdown		Take server down
  status		Gives a short status message from the server
  start-slave		Start slave
  stop-slave		Stop slave
  variables             Prints variables available
  version		Get version info from server
  
  示例：删除db02数据库
  [root@mysql-5 ~]# mysqladmin -uroot -pasdqwe123@ drop db02
Warning: Using a password on the command line interface can be insecure.
Dropping the database is potentially a very bad thing to do.
Any data stored in the database will be destroyed.

Do you really want to drop the 'db02' database [y/N] y
Database "db02" dropped
```

### 3.mysqlbinog

由于服务器生成的二进制日志文件是以二进制格式保存，所以如果想要检查这种格式的文本文件，就睡使用到mysqlbinlog日志管理工具。

```mysql
语法：
mysqlbinlog [option] log-files1 log-files2 ...  （在服务器种执行，不需要进入mysql命令行中）

选项
  -d, --database=name         #指定数据名称，只列出指定的数据库相关操作
  -o, --offset=#              #忽略掉日志中的前n行命令
  -s, --short-form            #将输出的文本格式日志输出到指定的文件
  --start-datetime=name =date1 --stop-datetime=date2    #指定日期间隔内的所有日志
  --stop-position=pos1 --stop-position=pos2             #指定位置间隔内的所有日志
```

### 4.mysqlshow

mysqlshow客户端对象查找工具，用来很快地查找存在哪些数据库、数据库表中表，表中的列或者索引。

```mysql
语法：
mysqlshow [option] [db_name [table_name[col_name]]]

选项：
--count  #显示数据以及表的统计信息（数据库、表均可以不指定）
-i       #显示指定数据库或者指定表的状态信息


示例：
#查询每个数据库的表的数量以表中记录的数据，Databases显示的是存在的数据库，Tables表示每个库中表的总个数，Total Rows表示表中包含多少条数据，例如：building4.0库共有50张表，50张表中共有118035条数据
[root@mysql-5 mysql]# mysqlshow -uroot -pasdqwe123@ --count
Warning: Using a password on the command line interface can be insecure.
+------------------------+--------+--------------+
|       Databases        | Tables |  Total Rows  |
+------------------------+--------+--------------+
| information_schema     |     59 |        28713 |
|  demo                  |      1 |            3 |
| Labour4.0              |     17 |          542 |
| Training1.0            |     25 |          691 |
| WZ_SupervisoryPlatform |      0 |            0 |
| building4.0            |     50 |       118035 |
| building4.1            |     70 |       274918 |
| building4.2            |     69 |       274924 |
| dx_labour4.0           |     16 |         9143 |
| itcast                 |      1 |            0 |
| labour4.1              |     18 |         1287 |
| mysql                  |     28 |         3759 |
| performance_schema     |     52 |        29504 |
+------------------------+--------+--------------+
13 rows in set.






#查询某个库中每个表中的字段数，以及行数
#指定某个数据库进行统计，building4.2指定统计的库名
 Tables 表示 building4.2的表
 Columns 表示 这张表中有几个字段
 Total Rows 表示这张表中有几条数据
 
[root@mysql-5 mysql]# mysqlshow -uroot -pasdqwe123@ building4.2 --count
Warning: Using a password on the command line interface can be insecure.
Database: building4.2
+--------------------------------+----------+------------+
|             Tables             | Columns  | Total Rows |
+--------------------------------+----------+------------+
| Apply                          |        8 |          0 |
| CapitalRecycle                 |        6 |          4 |
| ChargingPlatformDeviceInfo     |       10 |          1 |
| ChargingPlatformOperatingData  |       12 |          6 |
| ComponentModels                |        8 |     273864 |
| CraneBasicInfo                 |       15 |          1 |

+--------------------------------+----------+------------+
69 rows in set.
```

**#查询building4.2库种CapitalRecycle表的详细情况**

#Field代表表中有的字段

#Type代表字段的类型

#Collation代表排序规则

#Null代表能否为空，NO不能为空，yes可以为空

#Privileges代表权限

#Comment代表备注

![image-20230203172815719](https://raw.githubusercontent.com/liujunweipython/tuchuang/main/img/image-20230203172815719.png)

