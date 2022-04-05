# SQL server

创建表：

```sql
drop table student    --删除表student
create table student  --创建表student
(sno char(4),
sname char(8),
sage int,
ssex char(2),
sdept char(20)
)

drop table course    --删除表course
create table course  --创建表course
(cno char(4),
cname char(8),
cpno char(4),
ccredit int
)

drop table sc    --删除表sc
create table sc  --创建表sc
(sno char(4),
cno char(4),
grade int
)
```

