首选，我们要查询出广东省所有的市和区
```
select s.id,r.cityName,p.cityName,s.cityName from s_provinces s,s_provinces p,s_provinces r where s.parentId = p.id and p.parentId = r.id and r.cityName = '广东省';
```
然后，我们要查出广东省所有的市
```
select p.id,r.cityName,p.cityName,null
from s_provinces s,s_provinces p,s_provinces r 
where p.parentId = r.id and r.cityName = '广东省';
```
最后，我们用union将两张表的数据连接起来
```
select s.id,r.cityName,p.cityName,s.cityName 
from s_provinces s,s_provinces p,s_provinces r 
where s.parentId = p.id and p.parentId = r.id and r.cityName = '广东省'
union
select p.id,r.cityName,p.cityName,null
from s_provinces s,s_provinces p,s_provinces r 
where p.parentId = r.id and r.cityName = '广东省'；
```