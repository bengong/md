---
title: MySQL数据库
date: 2017-05-24 19:26:44
tags:
---

#MySQL数据库

## 安装

指定安装的版本 比如：5.6

	sudo apt-get install mysql-server-<version>

设置默认密码为：root

	/usr/sbin/mysqld

## 查看安装后的信息

	netstat -lntp | grep 3306

## 启动

终端启动MySQL：

	/etc/init.d/mysql start；（stop ，restart）

## 修改默认密码

Linux系统下，使用mysqld_safe来启动MySQL服务。

	mysqld_safe --skip-grant-tables user=mysql

也可以使用/etc/init.d/mysql命令来启动MySQL服务。

	/etc/init.d/mysql start-mysqld --skip-grant-tables

修改默认密码

	use mysql;
	update user set password=password('root') where user='root';
	flush privileges;

## 不区分大小写

MySQL在Linux下数据库名、表名、列名、别名大小写规则是这样的：

　　1、数据库名与表名是严格区分大小写的；

　　2、表的别名是严格区分大小写的；

　　3、列名与列的别名在所有的情况下均是忽略大小写的；

　　4、变量名也是严格区分大小写的；

 

　　MySQL在Windows下都不区分大小写。

　　所以在不同操作系统中为了能使程序和数据库都能正常运行，最好的办法是在设计的时候都转为小写，但是如果在设计的时候已经规范化大小写了，那么在Windows环境下只要对数据库的配置做下改动就行了，具体操作如下：

　　在MySQL的配置文件中my.ini [mysqld] 中增加一行

　　lower_case_table_names = 1

　　参数解释：

　　0：区分大小写

　　1：不区分大小写

在 MySQL 中，数据库和表对就于那些目录下的目录和文件。因而，操作系统的敏感性决定数据库和表命名的大小写敏感。这就意味着数据库和表名在 Windows 中是大小写不敏感的，而在大多数类型的 Unix 系统中是大小写敏感的。
奇怪的是列名与列的别名在所有的情况下均是忽略大小写的，而表的别名又是区分大小写的。
要避免这个问题，你最好在定义数据库命名规则的时候就全部采用小写字母加下划线的组合，而不使用任何的大写字母。
或者也可以强制以 -O lower_case_table_names=1 参数启动 mysqld（如果使用 --defaults-file=...\my.cnf 参数来读取指定的配置文件启动 mysqld 的话，你需要在配置文件的 [mysqld] 区段下增加一行 lower_case_table_names=1）。这样MySQL 将在创建与查找时将所有的表名自动转换为小写字符（这个选项缺省地在 Windows 中为 1 ，在 Unix 中为 0。从 MySQL 4.0.2 开始，这个选项同样适用于数据库名）。
当你更改这个选项时，你必须在启动 mysqld 前首先将老的表名转换为小写字母。
换句话说，如果你希望在数据库里面创建表的时候保留大小写字符状态，则应该把这个参数置0： lower_case_table_names=1 。否则的话你会发现同样的sqldump脚本在不同的操作系统下最终导入的结果不一样（在Windows下所有的大写字符都变成小写了）。

 

在linux环境下，表的名称是区分大小写的，而在winodws环境下，表名则不区分大小写。因而如果设置不当，windows下的应用程序连接linux下的MySQL数据库常常提示table xxx doesn't exits的情况。

一、MySQL中关于大小写有两个变量： 
                         
	lower_case_file_system
                        
该变量说明是否数据目录所在的文件系统对文件名的大小写敏感。ON说明对文件名的大小写不敏感，OFF表示敏感。
                        
	lower_case_table_names                        
如果设置为1,表名用小写保存到硬盘上，并且表名比较时不对大小写敏感。如果设置为2，按照指定的保存表名，但按照小写来比较。该选项还适合数据库名和表的别名。
如果你正使用InnoDB表，你应在所有平台上将该变量设置为1，强制将名字转换为小写。           
如果运行MySQL的系统对文件名的大小写不敏感(例如Windows或Mac OS X)，你不应将该变量设置为0。如果启动时没有设置该变量，并且数据目录所在文件系统对文件名的大小写不敏感，MySQL自动将lower_case_table_names设置为2。


二、为了消除linux环境下大小写敏感问题，笔者的设置如下：
1.修改my.cnf配置文件

	shell>vi /etc/my.cnf
	[mysqld]
	lower_case_table_names = 1

## 连接

登录MySQL：

(用root账户登录),然后输入密码；

    mysql -uroot -p 
    

## 查看数据库和表

查看所有的数据库名：

    show databases;

选择一个数据库操作：

    use database_name;

查看当前数据库下所有的表名：

    show tables;

查看表结构：

    show columns from table_name;

查看表结构：

    describe table_name;(快捷方式)


MySQL 中有非常多的日期函数，但是使用到比较多的就是 DATE_FORMAT(), FROM_UNIXTIME() 和 UNIX_TIMESTAMP() 这三个，DATE_FORMAT() 把日期进行格式化，FROM_UNIXTIME() 把时间戳格式化成一个日期，UNIX_TIMESTAMP() 正好想法，把日期格式化成时间戳。下面就介绍下他们之间详细的使用过程：

DATE_FORMAT()

DATE_FORMAT() 函数用于以不同的格式显示日期/时间数据，其语法是：DATE_FORMAT(date,format)。

其中 date 参数是合法的日期，format 参数则规定日期/时间的输出格式，可以使用的格式有：

| 格式 | 描述 | 
| ---- | ---- | 
| %a   |  缩写星期名    |
| %b   |  缩写月名    |
| %c   |  月，数值    |
| %D  |  	带有英文前缀的月中的天 |
| %d  |  	月的天，数值(00-31) |
| %e  |  	月的天，数值(0-31) |
| %f  |  	微秒 |
| %H  |  	小时 (00-23) |
| %h  |  	小时 (01-12) |
| %I  |  	小时 (01-12) |
| %i  |  	分钟，数值(00-59) |
| %j  |  	年的天 (001-366) |
| %k  |  	小时 (0-23) |
| %l  |  	小时 (1-12) |
| %M  |  	月名 |
| %m  |  	月，数值(00-12) |
| %p  |  	AM 或 PM |
| %r  |  	时间，12-小时（hh:mm:ss AM 或 PM） |
| %S  |  	秒(00-59) |
| %s  |  	秒(00-59) |
| %T  |  	时间, 24-小时 (hh:mm:ss) |
| %U  |  	周 (00-53) 星期日是一周的第一天 |
| %u  |  	周 (00-53) 星期一是一周的第一天 |
| %V  |  	周 (01-53) 星期日是一周的第一天，与 %X 使用 |
| %v  |  	周 (01-53) 星期一是一周的第一天，与 %x 使用 |
| %W  |  	星期名 |
| %w  |  	周的天 （0=星期日, 6=星期六） |
| %X  |  	年，其中的星期日是周的第一天，4 位，与 %V 使用 |
| %x  |  	年，其中的星期一是周的第一天，4 位，与 %v 使用 |
| %Y  |  	年，4 位 |
| %y  |  	年，2 位 |


FROM_UNIXTIME()

FROM_UNIXTIME() 函数将 MySQL 中以 INT 存储的时间戳以 “YYYY-MM-DD” 格式来显示的字符，其语法是 FROM_UNIXTIME(unix_timestamp ，format) 。

其中 unix_timestamp 参数为要转换的时间戳，format 参数则规定日期/时间的输出格式，他可以使用的格式和 DATE_FORMAT() 函数基本一致，这里不再列出。

UNIX_TIMESTAMP()

UNIX_TIMESTAMP() 函数将 MySQL 中存储为日期的数据转换成时间戳，其语法是 UNIX_TIMESTAMP(date ) 。它只有一个参数，date 为合法的日期。