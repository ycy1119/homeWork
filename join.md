## 查出所有广东省的市、县的思路总结
     select x.id, p.cityName, s.cityName, x.cityName
    from s_provinces x
       join s_provinces s on x.parentId = s.id
       join s_provinces p on s.parentId = p.id
    where p.cityName = '广东省'
    union all
    select s.id, p.cityName, s.cityName, null from s_provinces s
                join s_provinces p on s.parentId = p.id
                where p.cityName = '广东省';

- 首先，s_ provinces表中存在了递归关系，所以要在s_provinces指定一个父节点，查询出该父级下面的所有节点。
- -- 查询父节点等于parentId为1000000的下的广东省
    
        select sheng.id,guo.cityName,sheng.cityName from s_provinces guo
                join s_provinces sheng on sheng.parentId =guo.id
                where sheng.cityName='广东省';    
- -- 查询父节点广东省（等于Id为440000的）下的所有市
    
		select sheng.id,sheng.cityName,shi.cityName from s_provinces sheng
                              join s_provinces shi on shi.parentId=sheng.id
                              where sheng.cityName='广东省';
- 查询父节点为（广东省下面所有市）下的所有区县
    
		select x.id, p.cityName, s.cityName, x.cityName
		from s_provinces x
       	join s_provinces s on x.parentId = s.id
       	join s_provinces p on s.parentId = p.id
   		 where p.cityName = '广东省'
