## 首先，我们要有个数据源，
    -- 源数据
    select * from china_city limit 4;
    select * from lagou_position_bk limit 2; -- lagou_position_bk (工作信息，地区信息，分类信息，公司信息)
## 其次我们要清楚在数据源中分割出想要的数据，例如：
    -- 要求，创建三张表
    -- lagou_city (cityid, province, city, district)  # 全国的省市县信息，需要从 china_city 表中提取出来
    -- lagou_company (cid, name, short_name, size, financestage) # 所有的公司表，从 lagou_position_bk 中分离出来
    -- lagou_position (pid, cityid, cid, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at)

## 分割过程
> #### --创建lagou_city表

    -- 分步创建
    create table lagou_city01 as
    select d.id, p.cityName as province, c.cityName as city, d.cityName as district from
      (select * from china_city where depth=3) d
        join china_city c on d.parentId = c.id and c.depth=2
        join china_city p on c.parentId = p.id and p.depth=1;
    
    insert into lagou_city01
    select c.id, p.cityName as province, c.cityName as city, null as district from (select * from china_city where depth=2) c
      join china_city p on c.parentId = p.id and p.depth = 1;
    
    select count(*) from lagou_city01;
    
    -- 或者使用 union 语句
    --Union:
    --作用：把2次或多次查询结果合并起来
    --要求：两次查询的列数一致
    --推荐：查询的每一列，相对应的列类型也一样
    
    create table lagou_city as
    select d.id, p.cityName as province, c.cityName as city, d.cityName as district from
      (select * from china_city where depth=3) d
        join china_city c on d.parentId = c.id and c.depth=2
        join china_city p on c.parentId = p.id and p.depth=1
    union
    select c.id, p.cityName as province, c.cityName as city, null as district from (select * from china_city where depth=2) c
    join china_city p on c.parentId = p.id and p.depth = 1;

## 将公司、城市信息从主表分离出去:

	create table lagou_position
	as
	select pid, cid as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at from
	(
	  -- position 表中 district 为空的数据
	  select p.*, c.cid from (select * from lagou_position_bk where district is null) p
	     join lagou_city c on c.city like concat(p.city, '%') and c.district is null
	
	  union all
	
	  -- position 表中 district 不为空的数据
	  select p.*, c.cid from (select * from lagou_position_bk where district is not null) p
	    join lagou_city c on c.city like concat(p.city, '%') and c.district like concat(p.district, '%')
	
	) as ppp;
	
	
	-- 也可以分步进行。使用 union 语句虽然会简化语句，但效率会比较低
	create table lagou_position as
	select pid, cid as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at
	from (select * from lagou_position_bk where district is null) p
	  join lagou_city c on c.city like concat(p.city, '%') and c.district is null;
	
	insert into lagou_position
	select pid, cid as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at
	from (select * from lagou_position_bk where district is not null) p
	  join lagou_city c on c.city like concat(p.city, '%') and c.district like concat(p.district, '%');
	 
> ## 注意:
>
>分表过程中、分完的表，需要添加主键或索引，否则关联查询会特别特别慢
>使用 create as 语句分表会比较简单，但这个过程存在数据的复制，会比较占用硬盘存储
>除了 create as 语句，也可以在基表上进行数据操作（删改）。但是注意数据的备份，当心不慎操作导致的数据永久丢失
>备份迁移需要在系统停机的情况下进行
