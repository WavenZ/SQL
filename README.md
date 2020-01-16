# SQL
1. 创建数据库:`create database baseName`。
```sql
create database db
```
2. 创建表：`create table tableName(attributes)`。

```sql
create table persons(
    Id int not null unique, -- int类型，非空且唯一
    lastName varchar(255), -- 可变长字符串，最长255字节
    firstName varchar(255),
    Address varchar(255),
    City varchar(255)
);
```
3. 修改表名：`alter table tableName rename newName`
```sql
alter table persons rename animals;
```
4. 修改属性名：`alter table tableName rename column sourceName to targetName`
```sql
alter table persons rename column Id to P_Id;
```
5. 修改属性类型：`alter table tableName modify column attrName typeName`
```sql
alter table persons modify column Id smallint; 
```
6. 增加属性：`alter table tableName add attrName typeName`
```sql
alter table persons add birthday date;
```
7. 删除属性：`alter table tableName drop <column> attrName`
```sql
alter table persons drop birthday;
alter table persons drop column birthday;
```
8. 删除表：`drop table tableName`
```sql
drop table persons;
```
9. 向表中插入数据：`insert into tableName(attrNames) values(attrs)`或`insert into tableName values(attrs)`
```sql
insert into 
persons(Id, LastName, FirstName, Address, City) 
values(1, 'Adams', 'John', 'Oxford Street', 'London');
```
```sql
insert into persons values(2, 'Bush', 'George', 'Fifth Avenue', 'New York');
```
10. 基本选择：`select [attrNames] from tableName`
```sql
select * from persons;
select LastName, firstName from persons;
```
11. 去除重复项：`select distinct [attrNames] from tableName`
```sql
select distinct * from persons;
```
12. 条件选择：`select [attrNames] from tableName where condition`
```sql
select * from persons where City = 'Beijing'; -- 选择City等于Beijing的数据
select lastName from persons where firstName City != 'Beijing'; -- 选择City不等于Beijing的lastName项数据
```
可用的操作符：`=`、`!=`、`<`、`>`、`<=`、`>=`、`in`、`between`、`like`
```sql
select * from persons where City in ('Beijing', 'London'); -- City为Beijing或London
select * from persons where City not in('Beijing', 'London'); -- City不为Beijing或London
select * from persons where Id between 10 and 20; -- Id为10到20之间(闭区间)
select * from persons where lastName like 'N%' -- lastName以字母N开头
select * from persons where lastName not like 'N%' -- lastName不以字母N开头
```
13. 多条件选择：`select [attrNames] from tableName where condition1 and/or condition2`
```sql
select * from persons where lastName = 'Adams' and firstName = 'John';
```
14. 选择n条数据：`select top n|percentage [attrNames] from tableName` `select [attrNames] from tableName limit n` `select [attrNames] from tableName where ROWNUM <= N`
```sql
-- SQL server
select top 10 * from persons;
select top 50 percentage * from persons;
-- MySQL
select * from persons limit 10;
-- Oracle
select * from persons where ROWNUM <= 10;
```
15. 更新数据：`update tableName set attrName = val where condition`
```sql
-- MySQL中可能需要用set sql_safe_updates = 0来关闭安全模式。
update persons set lastName = 'Mick' where lastName = 'Adams';
update persons set Address = 'Oxford Street', City = 'London' where City = 'New York';
```
16. 删除数据：`delete from tableName where condition`
```sql
delete from persons where City = 'London'; -- 删除所有city=London的数据
delete from persons; -- 删除所有数据，但是表仍然在
```
17. sql通配符：`select [attrNames] from tableName where attrName like pattern`
其中,`pattern`可以由`%`、`_`、`[charlist]`或`[!charlist]`构成。`%`匹配一个或多个字符，`_`匹配单个字符。
```sql
select * from persons where lastName like 'N%'; -- 以N开头
select * from persons where lastName like '%O'; -- 以N结尾
select * from persons where lastName like 'ABC_'; -- ABC0、ABC1、ABC2、ABCD等
select * from persons where lastName like '[ABC]%'; -- 以A或B或C开头,MySQL中不可用
```
通配符中如果要对`%`和`_`进行转移，需要用到`escape`：
```sql
select * from persons where lastName like '%_%'; -- 可以匹配到很多不用的数据;
select * from persons where lastName like '%/_%' escape '/'; -- 匹配带下划线的lastName;
```
18. 别名：sql中可以对表或者列取别名。
```sql
select lastName as family, firstName as Name from persons;
select p.lastName, p.firstName, o.orderId from persons as p, orders as o;
```
19. 排序：`select [attrNames] from tableName order by attrName;`
```sql
select company, orderNumber from orders order by company;
select company, orderNumber from orders order by company, orderNumber;
```
20. join: `select table1.attrs, table2.attrs from table1, table2 where condition`
 - `join`: 如果表中至少有一个匹配，则返回行
 - `left join`: 即使右表没有匹配，也从左表中返回所有行
 - `right join`: 即使左表没有匹配，也从右表中返回所有行
 - `full join`: 只要其中一个表存在匹配，就返回行
```sql
select persons.lastName, persons.firstName, order.orderNo
from persons
inner join orders
on persons.Id = orders.Id
order by persons.lastName;
```
21. 合并结果集：
```sql
select [attrNames] from tableName1
union
select [attrNames] from tableName2;
```
22. 创建备份：
```sql
select * into backupTableName from tableName; -- mySQL不支持
create table backupTableName(select * from tableName);
```
23. 创建表时设置约束：
 - `not null`：非空 
 - `unique`：唯一，(主键默认唯一)
 - `primary key`：设置主键
 - `foreign key`：设置外键
 - `check`：检查合法性
 - `default`：默认值
```sql
create table persons(
    Id int not null, -- not null
    lastName varchar(255) unique, -- unique
    firstName varchar(255),
    Sex varchar(10) default 'male', -- default
    Age int check(Age > 9),
    Id_P int,
    primary key(Id), -- primary key
    foreign key(Id_P) references order(Id_P) -- foreign key
);
```
可为约束命名或为多个属性同时设置约束：
```sql
create table persons(
    Id int not null,
    lastName varchar(255),
    firstName varchar(255),
    Age int,
    constraint c_unique unique(Id, lastName),
    constraint c_check check(Id > 0 and Age > 0)
);
```
其中，`c_unique`和`c_check`是约束的名字。

24. 增加约束：`alter table tableName add constraint ...`
```sql
create table persons(
    Id int not null,
    lastName varchar(255),
    firstName varchar(255),
    Address varchar(255),
    Age int,
    Id_P int,
);
alter table persons add unique(Id, lastName);
alter table persons add constraint c_unique unique(Id, lastName);

alter table persons add primary key(Id);
alter table persons add constraint c_primary primary key(Id);

alter table persons add foreign key(Id_P) references order(Id_P);
alter table persons add constraint c_foreign foreign key(Id_P) references prder(Id_P);

alter table persons add check(Id_P > 0);
alter table persons add constraint c_check check(Id_P > 0);

alter table persons alter Address set default 'Beijing'; -- MySQL
alter table persons alter column city set default 'Beijing'; -- SQL server / oracle / MS access / MySQL
```
25. 撤销约束：`alter table tableName alter attrName drop constraintName`
```sql
alter table persons drop index c_unique; -- MySQL
alter table persons drop constraint c_unique -- SQL server/oracle/MS access

alter table persons drop primary key; -- MySQL
alter table persons drop constraint c_primary; -- SQL server/oracle/MS access

alter table persons drop foreign key; -- MySQL
alter table persons drop constraint c_foreign; -- SQL server/oracle/MS access

alter table persons drop check c_check; -- MySQL
alter table persons drop constraint c_check; -- SQL server/oracle/MS access

alter table persons alter city drop default; -- MySQL
alter table persons alter column city drop default; -- SQL server/oracle/MS access
```
26. 创建索引：`create index indexName on tableName(attrName)`
```sql
create index personIndex on persons(lastName);
create unique index personsIndex on persons(lastName);
create index personIndex on persons(lastName DESC);
create index personIndex on persons(lastName, firstName);
```
27. 自动递增：
```sql
-- MySQL
create table persons(
    Id int not null auto_increment,
    lastName varchar(255),
    firstName varchar(255),
    ...
);
alter table persons auto_increment = 100; -- 从100开始

-- SQL server
create table persons(
    Id int not null identity,
    -- Id int not null identity(100, 10), -- 从100开始，每次递增10
    lastName varchar(255),
    firstName varchar(255),
    ...
);

-- MS Access
create table persons(
    Id int not null autoincrement,
    -- Id int not null autoincrement(100, 10), -- 从100开始，每次递增10
    lastName varchar(255),
    firstName varchar(255),
    ...
);

 -- Oracle
 create table persons(
     Id int not null,
     lastName varchar(255),
     firstName varchar(255),
     ...
 );
create sequence seq minvalue 1 start with 1 increment by 1 cache 10
```
28. 创建视图：`create view viewName as select ...`
```sql
create view myView as select * from persons where lastName not like 'A%'; 
```
29. 空值处理：
```sql
-- 可将null当作0处理

-- MySQL
select unitPrice * (unitsInStock + ifnull(unitsOnOrder, 0)) from products;

-- SQL server/MS access
select unitPrice * (unitsInStock + isnull(unitsOnOrder, 0)) from products;

-- Oracle
select unitPrice * (unitsInStock + nvl(unitsOnOrder, 0)) from products;
```


