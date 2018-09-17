##数据类型
###大致分为6类
1. 整数：bit/bool/tinyint/smallint/mediumint/int/bitint
2. 浮点数据：float/double/decimal
3. 字符串：char/varchar/tinytext/test/mediumtext/longtext
4. BLOB(Binary Large Object):tinyblob/blob/mediumblob/longblob
5. 日期：date(年-月-日)/time(时-分-秒)/datetime(年-月-日 时-分-秒)/timestamp
6. 其他: binary / varbinary / enum / set / geometry / point / LineString …
###问题：
1. char 跟 varchar 有什么区别？
	1. char(n)和varchar(n)中间的n代表字符的个数，不是字节的个数
	2. char是固定宽度，如果数据长度不满足n，那么将会在右边用空格补齐；varchar是变长宽度；
	3. 如果数据超过n的限制，那么数据将会被截断
	4. 
2. varchar 最多存储的长度是多少？
3. varchar 跟 text 类型有什么区别？
4. set/enum 该怎么去使用
5. 用命令行将sqlite数据导入到mysql
6. 用jdbc的方式将sqlite的书数据导入到mysql