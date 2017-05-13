# Operate Database

相对MyISAM和Memory，InnoDB支持事务，外键，表修复和并发控制，但磁盘和内存占用大，插入速度慢；Memory一般只用于临时表且表的大小受限

## 常用命令用例

```

mysql -h localhost -uroot -p;

create database example;

show databases;

select database();

drop database example;

show engines \G


```

# create, update and drop table

## 常用命令用例

*创建表*

```

create table student (
    num int(10) primary key,
    name varchar(20) not null,
    sex varchar(4),
    birthday datetime,
    address varchar(50)
);

create table grade (
    id int(10) primary key auto_increment,
    course varchar(10) not null,
    s_num int(10) not null,
    grade varchar(4),
    constraint s_sum foreign key(s_num) references student(num)
);

```

*修改表结构*

```

alter table grade modify course varchar(20);

alter table grade modify s_num int(10) after id;

desc grade;

alter table grade change grade score varchar(4);

alter table grade drop foreign key s_sum;

show create table grade \G

alter table grade engine=myisam;

alter table student drop address;

alter table student add phone int(10);

alter table grade rename gradeInfo;

alter table gradeInfo add constraint s_num foreign key(s_num) references student(num);

```

`drop table student;`

# index

索引有两种实现类型：B数和哈希表，InnoDB和MyISAM支持B数，Memory支持哈希表(默认)和B树；索引能提高条件查询，分组，排序和表连接查询的速度；但在插入数据时需要维护索引增加时长

索引类型：

* 普通索引
* 唯一性索引：性能比普通索引要高
* 全文索引：针对字符型的表属性
* 单列索引
* 多列索引
* 空间索引

索引的使用原则：

* 尽量使用唯一索引
* 为经常需要排序，分组，连接或作为查询条件的属性建立索引
* 限制索引的数目
* 尽量使用属性的前段部分为索引而非全文索引
* 删除不再使用的索引

## 常用命令用例

```

create database job;

// 创建表时创建索引

CREATE TABLE user (
    userid int(10) NOT NULL AUTO_INCREMENT,
    username varchar(20) NOT NULL,
    passwd varchar(20) NOT NULL,
    info text,
    PRIMARY KEY (userid),
    unique index index_uid(userid desc),
    index index_user(username, passwd),
    fulltext index index_info(info)
);

CREATE TABLE information (
    id int(10) NOT NULL AUTO_INCREMENT,
    name varchar(20) NOT NULL,
    sex varchar(4) NOT NULL,
    birthday date DEFAULT NULL,
    address varchar(50) DEFAULT NULL,
    tel varchar(20) DEFAULT NULL,
    pic blob,
    PRIMARY KEY (id)
);

create index index_name on information(name(10));

create index index_bir on information(birthday, address);

explain select * from information where birthday='' \G

alter table information add unique index index_id(id asc);

drop index index_user on user;

drop index index_name on information;

```

# view

视图的作用：

* 简化操作
* 增加数据的安全性
* 提高表的逻辑独立性

## 常用命令用例

```

Create Table: CREATE TABLE work_info (
  id int(10) NOT NULL,
  name varchar(20) NOT NULL,
  sex varchar(4) NOT NULL,
  age int(5) DEFAULT NULL,
  address varchar(50) DEFAULT NULL,
  tel varchar(20) DEFAULT NULL,
  PRIMARY KEY (id)
);

insert into work_info values
(1, 'zhang san', M, 18, 'beijing haidian'  , 1234567)
(2, 'li si'    , M, 22, 'beijing changping', 2345678)
(3, 'wang wu'  , F, 17, 'hunan yongzhou'   , 3456789)
(4, 'zhao liu' , F, 25, 'liaoning buxin'   , 4567890);

create algorithm=merge view info_view(id, name, sex, address)
as select id, name, sex, address from work_info where age > 20
with local check option;

select * from info_view;

create or replace algorithm=merge view info_view(id, name, sex, address)
as select id, name, sex, address from work_info where age < 20
with local check option;

update info_view set sex='M' where id=3;

drop view if exists info_view cascade;

```

# Trigger

在表中对update，delete，insert操作进行监听并注册触发器，当监听的操作执行前或后触发触发器

## 常用命令用例

```

create table product (
    id int(10) primary key,
    name varchar(20) not null,
    functiono varchar(50),
    company varchar(20) not null,
    address varchar(50)
);

create table operate (
    op_id int(10) primary key auto_increment,
    op_type varchar(20) not null,
    op_time time not null
);

create trigger product_bf_insert 
before insert on product for each row
insert into operate values(null, 'before insert', current_time());

create trigger product_af_update
after update on product for each row
insert into operate values(null, 'after update', current_time());

create trigger product_af_del
after delete on product for each row
insert into operate values(null, 'after delete', current_time());

delimiter @

create trigger product_bf_del
before delete on product for each row
begin
insert into operate values(null, 'before delete', current_time());
end@

delimiter ;

insert into product
values(1,'p1','f1','c1','a1');

update product set name='p2';

drop trigger product_bf_insert;

drop trigger product_af_update;

drop trigger product_af_del;

show triggers \G

select * from information_schema.triggers \G

```

# Select

## 常用命令用例

```

create table student (
    id int(10) primary key,
    name varchar(20) not null,
    sex varchar(4),
    birth year,
    department varchar(20) not null,
    address varchar(50)
);

create table score (
    id int(10) primary key auto_increment,
    stu_id int(10) not null,
    c_name varchar(10),
    grade int(10)
);

insert into student values(901, 'zhanglaoda', 'male', 1985, 'computer science', 'beijing haidian');
insert into student values(902, 'zhanglaoer', 'male', 1986, 'chinese', 'beijing haidian');
insert into student values(903, 'zhangsan', 'f', 1990, 'chinese', 'hunan yongzhou');
insert into student values(904, 'lisi', 'male', 1990, 'english', 'liaoning buxin');
insert into student values(905, 'wangwu', 'f', 1991, 'english', 'fujian xiamen');
insert into student values(906, 'wangliu', 'male', 1988, 'computer science', 'hunan hengyang');

insert into score values(1, 901, 'computer', 98);
insert into score values(2, 901, 'english', 80);
insert into score values(3, 902, 'computer', 65);
insert into score values(4, 902, 'chinese', 88);
insert into score values(5, 903, 'chinese', 95);
insert into score values(6, 904, 'computer', 70);
insert into score values(7, 904, 'english', 92);
insert into score values(8, 905, 'english', 94);
insert into score values(9, 906, 'computer', 90);
insert into score values(10, 906, 'english', 85);

select * from student;

select * from student limit 1, 3;

select id, name, department from student;

select * from student where department in ('computer science', 'english');

select * from student where year(now()) - birth between 18 and 22;

select department, count(department) from student group by department;

select c_name, max(grade) from score group by c_name;

select c_name, grade from score left join student as stu on stu_id = stu.id where name = 'lisi';

select * from student left join score on student.id = score.stu_id;

select stu_id, sum(grade) from score group by stu_id;

select c_name, avg(grade) from score group by c_name;

select * from student where id in (select stu_id from score where c_name = 'computer' and grade < 95);

select * from student where id in (select stu_id from score where c_name = 'computer' and stu_id in (select stu_id from score where c_name = 'english'));

select grade from score where c_name='computer' order by grade desc;

select id from student union select stu_id from score;

select name, department, c_name, grade from student as stu left join score as s on stu.id=s.stu_id where name regexp '^(zhang|wang)';

select name, year(now()) - birth as age, department, c_name, grade from student as stu left join score as s on stu.id=s.stu_id where address regexp '^hunan';

```

# insert, update and delete

## 常用命令用例

```

create table food (
    id int(10) primary key auto_increment,
    name varchar(20) not null,
    company varchar(30) not null,
    price float,
    produce_time year,
    validity_time int(4),
    address varchar(50)
);

insert into food
values(null, 'AA cookie', 'AA factory', 2.5, 2008, 3, 'beijing'),
(null, 'CC Milk', 'CC factory', 3.5, 2009, 1, 'hebei'),
(null, 'EE jelly', 'EE factory', 1.5, 2007, 2, 'beijing'),
(null, 'FF coffee', 'FF factory', 20, 2002, 5, 'tianjin'),
(null, 'GG sugar', 'GG factory', 14, 2003, 3, 'guangdong');

insert into food(name, company, price, produce_time, validity_time, address)
	select name, company, price, produce_time, validity_time, address from food;

update food
set address='neimenggu', price=3.2
where company='CC factory';

delete from food where year(now()) - produce_time > validity_time;

delete from food where address='beijing';

```

# MySQL functions

[functions手册](https://dev.mysql.com/doc/refman/5.7/en/func-op-summary-ref.html)

## 常用命令用例

*1-100随机整数*

`select round(rand()*100), floor(rand()*100), ceil(rand()*100);`

*圆周率及其正余弦，对数*

`select pi(), sin(pi()), cos(pi(), log(pi()));`

# Store Procedure & Store Function

在数据库中存储一份可执行代码，直接调用就能得出结果，好处包括：

* 不同的客户端能使用一样功能的代码
* 为了保证安全性，保证有足够权限才能访问

## 常用命令用例

*创建food表并插入数据*

```

create table food (
    id int(10) primary key auto_increment,
    name varchar(20) not null,
    company varchar(30) not null,
    price float,
    produce_time year,
    validity_time int(4),
    address varchar(50)
);

insert into food
values(null, 'AA cookie', 'AA factory', 2.5, 2008, 3, 'beijing'),
(null, 'CC Milk', 'CC factory', 3.5, 2009, 1, 'hebei'),
(null, 'EE jelly', 'EE factory', 1.5, 2007, 2, 'beijing'),
(null, 'FF coffee', 'FF factory', 20, 2002, 5, 'tianjin'),
(null, 'GG sugar', 'GG factory', 14, 2003, 3, 'guangdong');

```

*创建存储过程*

```

delimiter @@

-- be careful the ';' at each statement
create procedure food_price_count(in price_info1 float, in price_info2 float, out count int)
	reads sql data
	begin
		# local variable must be declared fist in block
		declare temp float;

		# cursor must be declared after local variable
		declare match_price cursor for select price from food;
		
		# handler must be declared after cursor
		declare exit handler for not found close match_price;
		
		set @sum=0;

		select count(*) into count from food where price>price_info1 and price<price_info2;

		open match_price;

		while count>0 do
			fetch match_price into temp;

			if temp>price_info1 and temp<price_info2 then set @sum=@sum+temp;
			end if;
			
			set count = count - 1;
			
		end while;

		close match_price;
		
		select count(*) into count from food where price>price_info1 and price<price_info2;
		
	end;
@@

delimiter ;

```

*调用存储过程*

`call food_price_count(2,18,@count);`

*查看结果*

`select @count, @sum;`

*删除存储过程*

`drop procedure food_price_count;`

*查看存储过程*

`show create procedure food_price_count \G`

*创建存储函数*

```

delimiter @

create function food_price_count(price_info1 float, price_info2 float)
	returns int
	reads sql data
	begin
		return (select count(*) from food where price>price_info1 and price<price_info2);
	end
@

delimiter ;

```

*调用存储函数*

`select food_price_count(2,18);`

*删除存储函数*

`drop function food_price_count;`

*查看存储函数*

`show create function food_price_count \G`

# User Uanagement

user表包括用户列(Host, User, Password)，权限列，安全列，资源控制列；管理用户的登录和对所有表的权限，ssl，资源使用限制等；

db和host表包括用户列(host, user)，权限列;管理用户在数据库中的权限；

table_priv表包括用户列(host, user)，权限列；管理用户在表中的权限；

columns_priv表包括用户列(host, user)，权限列；管理用户在列中的权限；

pros_priv表用来管理用户对存储过程和函数的权限；

[privilege手册](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html)

## 常用命令用例

*登陆test数据库*

`mysql -h 59.83.17.100 -u root -p test`

*创建用户*

`create user 'test'@'localhost' identified by 'password';`

*删除用户*

`drop user 'test'@'localhost';`

*root修改自己密码*

`mysqladmin -u root -p password 'new_password'`

*root用户修改其他用户密码*

`set password for 'test'@'localhost'=PASSWORD('new_password');`

*普通用户修改自己密码*

`set password=PASSWORD('new_password');`

*授予查询，更新权限，该用户能给其他用户分与权限*

`grant select, update on *.* to 'test'@'localhost' with grant option;`

*回收权限*

`revoke select on *.* from 'test'@'localhost';`

`revoke all privileges, grant option from 'test'@'localhost';`

*查看权限*

`show grants for 'test'@'localhost';`

# Backup & Recovery

mysqldump命令的原理是根据数据库，表和数据生成一个备份文件，在文件中填入创建数据库，表和insert数据的命令

可以通过直接复制数据库目录的方式来备份MyISAM的表，但需要停掉MySQL服务

可以使用mysqlhotcopy工具来热备份MyISAM类型的表

select into outfile导出表数据到文本文件

SELECT [column name] FROM table [where ..] INTO OUTFILE 'file' [OPTION];

mysqldump也能导出数据，使用-X可以导出xml文件

mysqldump -uroot -pPassword -T dirname -X dbname table [option]

可以使用load data infile导入文本数据

LOAD DAATA [LOCAL] INFILE file INTO TABLE table [OPTION]; 

## 常用命令用例

*备份一个数据库中的表*

`mysqldump -uusername -p dbname table1 table2 > backup.sql`

*备份多个数据库*

`mysqldump -uusername -p --databases db1 db2 > backup.sql`

*备份所有数据库*

`mysqldump -uusername -p --all-databases > backup.sql`

*数据恢复*

`mysql -uroot -p < backup.sql`

*导出表中的数据，以"、"分隔，">"开头，字符型数据用双引号括注*

`select * from test.student into outfile '/tmp/data' fields terminated by '\、' optionally enclosed by '\"' lines starting by '\>' terminated by '\r\n';`

*导入表的数据*

`load data infile '/tmp/file' into table student fields terminated by '\、' optionally enclosed by '\"' lines starting by '\>' terminated by '\r\n'`

# Log

MySQL有四种日志，除二进制日志外其他都是文本日志，默认只启动错误日志，因为日志会降低性能：

* 二进制日志：把数据库的操作信息(不包括查询)存储在二进制日志文件中
* 错误日志：记录MySQL的启动，关闭和运行错误信息
* 通用查询日志：记录用户登陆，操作和查询数据库的信息
* 慢查询日志：记录执行超过指定时间的操作信息

二进制日志最好单独存放于其他磁盘，可以用来恢复数据库

## 常用命令用例

*在my.cnf或my.ini中启用二进制日志*

```
[mysqld]
log-bin=/tmp/b_log
```

*查看二进制日志*

`mysqlbinlog /tmp/b_log.000001;`

*删除所有二进制记录*

`reset master;`

*删除某一个二进制日志文件*

`purge master logs to '/tmp/b_log.000001'；`

*删除某个日期前创建的二进制日志*

`purge master logs to '2017-08-01 11:00:00'；`

*用二进制日志恢复数据库*

`mysqlbinlog b_log.000001 | mysql -uroot -p`

*暂停和恢复二进制日志*

`set sql_bin_log = 0`

`set sql_bin_log = 1`

*在my.cnf或my.ini中启用错误日志*

```
[mysqld]
log-error=/tmp/err_log
```

*重新创建错误日志文件，旧的日志文件被改名，可删掉或备份*

`mysqladmin -uroot -p flush-logs`

*在my.cnf或my.ini中启用通用查询日志*

```
[mysqld]
log=/tmp/search_log
```

*重新创建错误日志文件，旧的日志文件被删掉*

`mysqladmin -uroot -p flush-logs`

*在my.cnf或my.ini中启用通用查询日志*

```
[mysqld]
log-slow-queries=/tmp/slow_queries_log
long_query_time=1
```

*重新创建错误日志文件，旧的日志文件被删掉*

`mysqladmin -uroot -p flush-logs`

# Performance

索引经常用来优化条件查询，排序等；使用索引查询记录时，要注意索引的使用情况。like条件的字符串不能以通配符%开头，否则不会启用索引；使用多列索引时，查询条件的要用多列索引中的第一个字段才会启用索引；OR所连接的所有查询条件都要用索引的字段才会启用。

改变数据库的表结构，通过增加冗余来优化数据库的访问：

* 将字段多的表中，把常用字段和不常用字段分成两个表
* 为经常需进行要连接查询的多个表键一个中间表减少连接查询
* 为表增加冗余字段减少表连接
* 插入大量数据时可以先禁用索引和唯一性检查，完成以后再重新启用

## 常用命令用例

*查看InnoDB表的查询记录数和更新记录数，慢查询数，连接数*

`show status like 'Innodb_rows_read' \G`

`show status like 'Innodb_rows_updated' \G`

`show status like 'Slow_queries' \G`

`show status like 'Connections' \G`

*分析查询语句的性能*

`explain select * from score where stu_id=96;`

*分析score表*

`analyze table score;`

*检查score表*

`check table score;`

*优化score表*

`optimize table score;`