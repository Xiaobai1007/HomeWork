首先，我们从主表s_provinces中分离出省、市、县三个字段
```
select r.id as dId, s.cityName as province, p.cityName as city, r.cityName as district from s_provinces s 
join s_provinces p on s.id = p.parentId
join s_provinces r on p.id = r.parentId
where s.depth = 1 and p.depth = 2 and r.depth = 3
union
select p.id as cId, s.cityName as province, p.cityName as city,null from s_provinces s
join s_provinces p on s.id = p.parentId
where s.depth = 1 and p.depth = 2;
```
再从主表lagou_position中分离出公司的各信息,并创建company表
```
create table company as select company_id, company_short_name, company_full_name, company_size, financestage from lagou_backups;
```
然后我们把之前分好的省、市、县放在同一个表里
```
create table lagou_city as 
select r.id as cId, s.cityName as province, p.cityName as city, r.cityName as district from s_provinces s 
join s_provinces p on s.id = p.parentId
join s_provinces r on p.id = r.parentId
where s.depth = 1 and p.depth = 2 and r.depth = 3;
```
因为我们创建表格时各个字段是默认为不能为空的，所有我们要修改字段的默认属性
![修改字段属性](.\primaryKey.png)
之后我们再把数据录入完整
```
insert into lagou_city 
select p.id as cId, s.cityName as province, p.cityName as city,null from s_provinces s
join s_provinces p on s.id = p.parentId
where s.depth = 1 and p.depth = 2;
```
先查询出主表lagou_backups_bc中区县为空的值，外联lagou_city表条件该表中city的值是否和lagou_backups_bc中city字段相等，并且区县为空，然后查询出职位的所需字段
```
select pid, cid as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at from (select * from lagou_backups_bc where district is null) p
join lagou_city c on c.city like concat(p.city, '%') and c.district is null;
```
之后查询出主表lagou_backups_bc中区县不为空的值，外联lagou_city表条件该表中city的值是否和lagou_backups_bc中city字段相等，并且区县不为空，然后查询出职位的所需字段
```
select pid, cid as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at from (select * from lagou_backups_bc where district is not null) p
join lagou_city c on c.city like concat(p.city, '%') and c.district like concat(p.district, '%');
```
最后将查询出的数据添加到lagou_position表中
```
create table lagou_position as
select pid, cid as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at from (select * from lagou_backups_bc where district is null) p
join lagou_city c on c.city like concat(p.city, '%') and c.district is null;

insert into lagou_position
select pid, cid as city, company_id as company, position, field, salary_min, salary_max, workyear, education, ptype, pnature, advantage, published_at, updated_at from (select * from lagou_backups_bc where district is not null) p
join lagou_city c on c.city like concat(p.city, '%') and c.district like concat(p.district, '%');
```

最后:总结一下做题的思路，写作业的时候不要忙于写代码，而要先分析需求，构造好思路，不要一直写代码，然后又改，要理清思路。





