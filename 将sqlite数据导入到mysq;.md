##sqlite数据导入到mysql
---
首先，从sqlite中导出：
1. sqlite3 lagou.db
2. .output lagou.sql
3. .dump
4. .exit
然后，修改lagou.sql文件，让其兼容Mysql数据库，之后就可以连接Mysql进行导入了
1. drop table if exists lagou;
2. set names utf8;
3. set autocommit=0;
4. source lagou.sql的地址
5. commit;
6. select count(*) from lagou;

##sqlite数据导入到mysql可能遇到的问题
---------
向 mysql 插入汉字时报错 Incorrect string value: '\xE6\x9B\xB9\xE5\x86\xAC...' for col....
Incorrect string value: '\xE6\x9B\xB9\xE5\x86\xAC...' for column 'realname' at row 1 该情况一般是由数据库设计的编码错误导致的。
###show variables like 'character%' 查看数据库编码，为latin1.

##如果项目正在测试当中，数据不重要的话，一劳永逸的解决办法是，使用
####alter database 数据库名 character set utf8 更改数据库的编码格式，再重新建表。

注意：在 Hibernate中，指定hibernate.hbm2ddl.auto=create，重新部署项目，hibernate会自动重新建表。

##如果项目中的数据是重要的话，使用
####alter table address convert to character set utf8 转换需要插入汉字的数据表编码为utf8即可，（此例中的数据表是address)