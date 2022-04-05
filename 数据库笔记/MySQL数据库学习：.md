# MySQL数据库学习：

## 进阶一：基础查询

```mysql
/*select last_name,job_id,salary as sal
from employees;*/
# select employee_id ,last_name,salary * 12 'sal_year' from employees;
# select employee_id ,last_name,salary * 12 'annual salary' from employees;
# 显示表的结构
/*desc departments;
show columns from departments;*/
# select distinct job_id from employees;
# 拼接的时候，如果出现null，该列全为null
select concat(
    employee_id,
    first_name,
    last_name,
    email,
    salary
    )as 'out' from employees;
# ifnull(表达式一，表达式二)
#  表达式一：可能为null的字段或表达式
#  表达式二：如果表达式一为null，则最终结果显示的值
# 功能：如果表达式一为null，则显示表达式二，否则显示表达式1
# 该函数使用规则，如果传入的列值不为null，显示原本的值，否则显示 空 。
select commission_pct,ifnull(commission_pct,'空') from employees;
```

## 进阶二：条件查询

```MySQL
/*
语法：
	select 查询列表
	from 表名
	where 筛选条件;
	
	执行顺序：
	1，from子句
	2，where子句
	3，select子句
*/

select last_name,first_name from employees where salary > 20000;

/*
	特点：
		1，按关系表达式筛选
			关系表达式： >	 < 	>= 	<= 	= 	!= 	<> 
			不建议使用 != 
		2，逻辑表达式：
			and 	or 		not
			不建议使用：&& 	|| 	！
		3，模糊查询：
			like 	in    between and 	is null
*/
# 案例一：查询部门编号不是100的员工信息
select *
from employees
where department_id<>100;
# 案例一：查询工资<15000的姓名，工资
select concat(last_name,first_name),salary
from employees
where salary < 15000;
/*
二：按逻辑表达式筛选
*/
# 案例一： 查询部门编号不是50到100之间的员工姓名，部门编号，邮箱
select concat(last_name,first_name),department_id,email
from employees
where not (department_id>=50 and department_id<=100);
#或者：
select concat(last_name,first_name),department_id,email
from employees
where department_id < 50 or department_id > 100;
# 案例二： 查询奖金率>0.03 或者 员工编号在60-110之间的员工信息
select *
from employees
where commission_pct>0.03 or (employee_id>=60 and employee_id<=100);
/*
三：模糊查询：
	1，like子句
		一般和通配符搭配使用，对字符型数据进行部分匹配查询
		常见的通配符：
			_  代表任意单个字符
			%  代表任意个字符(0-多)
*/
# 案例一： 查询姓名中包含字符a的员工信息
select * from employees where last_name like'%a%';
# 案例二： 查询姓名中最后一个字符为 e 的员工信息
select * from employees where last_name like '%e';
# 案例三： 查询姓名中第一个字符为 a 的员工信息
select * from employees where last_name like 'a%';
# 案例四： 查询姓名中第三个字符为 e 的员工信息
select * from employees where last_name like '__e%';
# 案例五： 查询姓名中第二个字符为 _ 的员工信息
select * from employees where last_name like '_\_%';
# 或者 escape表示用后面的那个字符代表转义字符
select * from employees where last_name like '_$_%' escape '$';
/*
	2，in关键字
		功能：用于查询某字段的值是否属于指定的列表之内
		 a in (常量值1，常量值2，常量值3,....)
		 a not in (常量值1，常量值2，常量值3,....)
		 in not/in
*/
# 案例一：查询部门编号是30/50/90 的员工名，部门编号
select last_name,department_id 
from employees
where department_id in(30,50,90);
# 或者
select last_name,department_id 
from employees
where department_id = 30
		or department_id = 50
		or department_id = 90;
# 案例二：查询工种编号不是ST_CLERK或IT_PROG的员工信息
select *
from employees
where job_id not in('ST_CLERK','IT_PROG');
# 或者
select *
from employees
where job_id <> 'ST_CLERK' and job_id <> 'IT_PROG';
# between and 判断某个字段是否介于两者之间
# between and / not between and
# 案例一：查询部门编号是30-90之间的部门编号，员工姓名
select department_id,last_name
from employees
where department_id between 30  and 90;
# 方式二：
select department_id,last_name
from employees
where department_id>=30 and department_id<=90;
# 案例二：查询年薪不是100000-200000之间的员工姓名，工资，年薪
select last_name,salary*12*(1+ifnull(commission_pct,0)) as yearsal
from employees
where salary*12*(1+ifnull(commission_pct,0)) not between 100000 and 200000;
# is null / is not null
# = 只能判断普通的数值
#  is 只能判断null值
# <=> 安全等于，既能判断普通内容，又能判断null值

# 案例一：查询没有奖金的员工信息
select * from employees where commission_pct is null;
# 查询有奖金的员工信息
select * from employees where commission_pct is not null;

```

```mySQL
#作业：
# 1，查询工资大于 12000 的员工姓名和工资
select last_name,salary
from employees
where salary>12000;
# 2，查询员工号为 176 的员工的姓名和部门号和年薪
select last_name,department_id,salary*12*(1+ifnull(commission_pct,0)) as '年薪'
from employees
where employee_id = 176;
# 3，选择工资不在 5000 到 12000 的员工姓名和工资
select last_name,salary
from employees
where salary not between 5000 and 12000;
# 4，选择在20 或 50 号部门工作的员工姓名和部门号
select last_name,department_id
from employees
where department_id in (20,50);
# 5，选择公司中没有管理者的员工姓名和部门号
select last_name,department_id
from employees
where manager_id is null;
# 6，选择公司中有奖金的员工姓名，工资，和奖金级别
select last_name,salary,commission_pct
from employees
where commission_pct is not null;
# 7，选择员工姓名的第三个字母是 a 的员工姓名
select last_name
from employees
where last_name like '__a%';
# 8，选择姓名中有字母 a 和 e 的员工姓名
select last_name
from employees
where last_name like '%a%' and last_name like '%e%';
# 9，显示出表 employees 表中 first_name 以 e 结尾的员工信息
select *
from employees
where first_name like '%e';
# 10，显示出表employees 部门编号在80-100 之间的姓名，职位
select last_name,job_id,department_id
from employees
where department_id between 80 and 100;
# 11，显示出表 employees 的manager_id 是100,101,110 的员工姓名，职位
select last_name,job_id
from employees
where manager_id in (100,101,110);
```

## 进阶三：排序查询

```MySQL
/*
语法：
	select 查询列表
	from 表名
	[where 筛选条件]
	order by 排序列表
	
	执行顺序：
	1，from 子句
	2，where 子句
	3，select 子句
	4，order by 子句
	
	举例：
	select last_name,salary
	from employees
	where salary > 10000
	order by salary;
	
	特点：
	1，排序列表可以是单个字段，多个字段，表达式，函数，列表，以及以上组合
	2，升序：通过asc ，默认行为
	3，降序：通过desc
*/
# 一，按单个字段排序
	# 案例一：将员工编号大于120的员工信息进行工资的升序
	select * 
	from employees
	where employee_id > 120
	order by salary asc;
	# 案例二：将员工编号大于120的员工信息进行工资的降序
	select * 
	from employees
	where employee_id > 120
	order by salary desc;
# 二，按表达式排序
	#案例一：对有奖金的员工，按年薪降序
	select *,salary*12*(1+ifnull(commission_pct,0)) as '年薪'
	from employees
	where commission_pct is not null
	order by salary*12*(1+ifnull(commission_pct,0)) desc;
	# 方式二：因为ifnull也会筛选奖金率不为null的值，但是where那个条件已经筛选了奖金率不为null的情况，所以不需要使用ifnull这个函数进行二次筛选了(先执行的是where子句)
	select *,salary*12*(1+commission_pct) as '年薪'
	from employees
	where commission_pct is not null
	order by salary*12*(1+ifnull(commission_pct,0)) desc;
#三，按别名排序
	#案例一：对有奖金的员工，按年薪降序
        # 方式三：
        select *,salary*12*(1+ifnull(commission_pct,0)) as '年薪'
        from employees
        where commission_pct is not null
        order by '年薪' desc;
#四，按函数的结果排序
	#案例一：按姓名的字数长度进行升序排序
	# length() 函数，获取字符串的长度
	select length(last_name),last_name
	from employees
	order by length(last_name) asc;
#五，按多个字段排序
	#案例一：查询员工的姓名，工资，部门编号，先按工资升序，再按部门编号降序
	select last_name,salary,department_id
	from employees
	order by salary asc,department_id desc;
#六，补充选学：按照列数排序
select *
from employees
order by 2 desc;

select * 
from employees
order by first_name asc;
```

### 作业：

```mysql
# 作业：
# 1，查询员工的姓名和部门和年薪，按年薪降序，按姓名升序
select last_name,department_id,salary*12*(ifnull(commission_pct,0)+1) '年薪'
from employees
order by '年薪' desc,last_name;
# 2,选择工资不在8000 到 17000 的员工的姓名和工资，按工资降序
select last_name,salary
from employees
where salary not between 8000 and 17000
order by salary desc;
# 3,查询邮箱中包含 e 的员工信息，并先按邮箱的字节数降序，再按部门号升序
select *,email,department_id
from employees
where email like '%e%'
order by length(email) desc ,department_id;
```

## 进阶四：常见函数

```mysql

函数：类似于Java中学过的'方法'，为了解决某个问题，编写一系列的命令集合封装在一起，对位仅仅暴露方法名，供外部调用。
	1，自定义方法（函数）
	2，调用方法（函数）
		叫什么：函数名
		干什么：函数功能

常见函数：
	1，字符函数：
		1, cancat(s1,s2,s3,....) 拼接，连接字符串
			select cancat('hello',last_name,first_name) from employees;
		2, length() 获取字符串的字节长度
			select length('hello')
		3, char_length() 获取字符串的字符长度
			select char_length('毛毛');
		4, substring(str,起始索引,截取的字符长度) 截取子串
			select substring('张三丰爱上了郭襄',1,3); 第一个参数是字符串，第二个参数是起始位置(MySQL的起始位置是1)，第三个参数是截取的字符个数.
		5, substring(str,起始索引) 截取起始索引以后的全部的字符串
		6, instr() 获取字符串第一次出现的索引
		select instr('三打白骨精aaa白骨精bb白骨精','白骨精');
		7, trim() 去前后指定的字符，默认是去掉空格
		select trim(' 虚 竹  ') as a;
		select trim('x' from 'xxxxxx虚xxxx竹xxxxxxxxx') as a;
		8, lpad() / rpad() 左填充/右填充 第二个参数是填充后该字符串要占的宽度，最后一个参数是要填充的字符。如果第二个参数小于原字符串的长度，则原字符串的后面的值将会消失。
		select lpad('木婉清',10,'a');
		select rpad('木婉清',1,'a');
	案例： 查询员工表的姓名，要求格式： 姓首字母大写，其他字符小写，名所有字符大写，且姓和名之间用 _ 分割，最后起别名为 'output'
	提示：大写 upper() 小写(lower)
		select concat(upper(subString(last_name,1,1)),
		substring(last_name,2),'_',lower(first_name)) 
		from employees;
		
		9, strcmp 比较两个字符的大小，返回 1 0 -1 
		10，left/right 截取子串
		select left('hha',1);
	2，数学函数：
		1， abs() 求绝对值
			abs(-1)
		2, ceil() 向上取整，返回>=该参数的最小整数
			ceil(1.222) #2
		3,  floor() 向下取整
		
		4， round() 四舍五入,第二个参数是保留小数点后几位
			round(1.2345)
			round(1.2334444,2)
		5,  truncate() 截断，第二个参数表示小数点后保留几位
			truncate(123,9933,1) #123.9
		6,  mod() 取余
			mod(10,3);
		
	3，日期函数：
		1, now(); 当前时间
		
		2，curdate() 当前日期
		
		3，curtime() 当前时间
		
		4，datediff('1999-08-26','2020-10-12') 两个日期之间差的天数，第一个日期减去第二个日期。 
		5，date_format(datetime,fmt) 将日期转换为指定格式的字符串
		
		6，str_to_date() 按照指定格式解析字符串为日期类型
		
	4，流程控制函数：
		1，if函数
		select if(100>9,'好','坏')
		#如果有奖金，则显示最终奖金，如果没有，则显示0
		select if(commission_pct is null,0,salary*12*commission_pct) 奖金,commission_pct from employees;
		2,case 函数
		情况一：类似于switch语句，可以实现等值判断
		case 表达式
		when 值1 then 结果1
		when 值2 then 结果2
		when 值3 then 结果3
		...
		else 结果n
		end
		
		
		/*
		案例：
		部门编号是30，工资显示为2倍
		部门编号是50，工资显示为3倍
		部门编号是60，工资显示为4倍
		否则不变
		显示 部门编号，新工资，旧工资
		*/
                  select department_id,
               case department_id
                   when 30 then salary * 2
                   when 50 then salary * 3
                   when 60 then salary * 4
                   else salary end as newsalary,
               salary
        from employees;
        /*
        情况二：类似于多重if语句，实现区间判断
        case
        when 条件一 then 结果一
        when 条件二 then 结果二
        ...
        else 结果n
        end
        
        案例：
        如果工资>20000,显示级别A
        工资>15000,显示B
        工资>10000,显示级别c
        否则显示为D
        */
                select salary,
               case
                   when salary > 20000 then 'A'
                   when salary > 15000 then 'B'
                   when salary > 10000 then 'C'
                   else 'D'
                   end as 级别
        from employees;
```

## 进阶五：分组函数

```mysql 
#分组函数：
	说明：分组函数王五用于实现将一组数据进行统计计算，最终得到一个值
	又称为聚合函数或者统计函数
	分组函数清单：
	sum(字段名)：求和
	avg(字段名)：求平均数
	max(字段名): 求最小值
	count(字段名): 计算非空字段值的个数
	# 案例一：查询员工信息表中，所有员工的工资和，工资平均值
# ，最高工资，最低工资，有工资的个数
select sum(salary),avg(salary),max(salary),min(salary),count(salary)
from employees;
# 当需要纵向统计时可以使用COUNT()。
# 	查询emp表中记录数：
select count(employee_id) from employees;
# 	查询emp表中有佣金的人数：
select count(salary) from employees;
# 注意，因为count()函数中给出的是comm列，那么只统计comm列非NULL的行数。

# 	查询emp表中月薪大于2500的人数：
select count(salary) from employees where salary>2500;
# 	统计月薪与佣金之和大于2500元的人数：

# 	查询有佣金的人数，以及有领导的人数：
select count(manager_id) from employees;

# count()的补充介绍
# count(*) = count(1)
select count(*)
from employees;
# 搭配distinct实现去重的操作
select count(distinct department_id)  from employees;
# 思考：每个部门的总工资，平均工资
# 分组查询实现：
select sum(salary),avg(salary),department_id
from employees
group by department_id;


```

## 进阶六：分组查询

```mysql
/*
语法：
	select 查询列表
	from 表名
	where 条件
	group by 分组列表;
*/
#特点：
# 查询列表往往是 分组函数和被分组的字段
# 分组查询中的筛选分为两类
					筛选的基表     	使用的关键词 				位置
	分组前筛选         原始表		   where				group by 的前面
	分组后筛选		 分组后的结果集    having				 group by 的后面
	where --->   group by   ----> having

问题：分组函数做条件只可能放在having后面！！！

#案例1，查询每个工种的员工平均工资
select avg(salary),job_id from employees group by job_id;
#案例二：查询每个领导的手下人数
select count(*),manager_id 
from employees 
where manager_id is not null
group by manager_id;
#案例三：查询那个部门的员工个数>5
select count(1) 员工个数,department_id
from employees
group by department_id
having count(*)>5;
# 案例四：每个工种没有奖金的员工的最高工资>12000的工种编号和最高工资
select job_id,max(salary)
from employees
where commission_pct is null
group by job_id
having max(salary)>12000
order by max(salary) asc;
按多个字段分组
查询每个工种每个部门的最低工资，并按最低工资降序
提示：工资和部门都一样，才是一组
select min(salary) 最低工资,job_id,department_id
from employees
group by job_id,deparment_id;

执行顺序：
1，from 子句
2，where 子句
3，group by 子句
4，having 子句
5，select 子句
6，order by 子句

```

## 进阶七：子查询

```MySQL
#也叫多表查询，当查询的字段来自多个表时，就会用到连接查询
1，内连接
			----------------------SQL92语法-------------------
	1，等值连接
		语法：
			select 查询列表
			from 表名1，表名2，....
			where 等值连接的连接条件
		特点：
			1，为了解决多表中字段重名问题，往往为表名起别名，提高语义性
		案例：
		# 查询员工名和部门名
            select last_name,department_name
            from employees e,departments d
            where e.department_id = d.department_id;
         # 查询部门编号大于100的部门名和所在城市名
            select department_name,city
            from locations l,departments d
            where l.location_id = d.location_id
            and d.department_id>100;	
           # 查询每个城市的部门数
                select l.city, count(*) 部门个数
                from departments d,
                     locations l
                where d.location_id = l.location_id
                group by l.city;
           # 查询哪个部门的员工个数大于5，并按员工个数进行降序
                select department_name,count(*)
                from departments d,employees e
                where d.department_id = e.department_id
                group by e.department_id
                having count(*)>5
                order by count(*) desc;
	------------------------SQL99语法----------------------
	内连接：
        语法：
                select 查询列表
                from 表名1 别名
                [inner] join 表名2 别名2
                on 连接条件
                where 筛选条件
                group by 分组后筛选
                order by 排序列表;
 SQL92和SQL99的区别：
 		SQL99 ，使用join关键字代替了之前的逗号，并且将连接条件和筛选条件进行的		分离，提高了阅读性。
 外连接：
 	左连接：左边为主表
    右连接：右边为主表
    语法：
    select 查询列表
    from 表1 别名
    left|right outer join 表2 别名
    on 连接条件
    where 筛选条件;
    
    子查询：
    当一个查询语句中又嵌套了另外一个完整的select语句，则被嵌套的select语句被称为子查询或者内查询外面的select语句又称为主查询或外查询
    
    分类：
    按子查询出现的位置进行分类：
    1，select后面
    	要求：子查询的结果为单行单列（标量子查询）
 	2，from后面
 		要求：子查询的结果可以为多行多列
 	3，where或者having后面   ！！！！！！！！！！！！！！！！！
 		要求：子查询的结果必须为单列
 			单行子查询
 			多行子查询
 	4，exists后面
 		要求：子查询结果必须为单列（相关子查询）
 	
 	特点：
 		1，子查询放在条件中，必须放在条件的右侧
 		2，子查询一般放在小括号中
 		3，子查询的执行优先于主查询
 		4，单行子查询对应了  单行操作符： > < >= <= = <>
 			多行子查询对应了多行操作符：any/some all in
 			
 		多行子查询：in 判断某字段是否在指定列表内
 		
 		#进阶7：子查询
 		/*
含义：
出现在其他语句中的select语句，称为子查询或内查询
外部的查询语句，称为主查询或外查询

分类：
按子查询出现的位置：
	select后面：
		仅仅支持标量子查询
	
	from后面：
		支持表子查询
	where或having后面：★
		标量子查询（单行） √
		列子查询  （多行） √
		
		行子查询
		
	exists后面（相关子查询）
		表子查询
按结果集的行列数不同：
	标量子查询（结果集只有一行一列）
	列子查询（结果集只有一列多行）
	行子查询（结果集有一行多列）
	表子查询（结果集一般为多行多列）

*/
#一、where或having后面
/*
1、标量子查询（单行子查询）
2、列子查询（多行子查询）

3、行子查询（多列多行）

特点：
①子查询放在小括号内
②子查询一般放在条件的右侧
③标量子查询，一般搭配着单行操作符使用
> < >= <= = <>

列子查询，一般搭配着多行操作符使用
in、any/some、all

④子查询的执行优先于主查询执行，主查询的条件用到了子查询的结果

*/
#1.标量子查询★

#案例1：谁的工资比 Abel 高?

#①查询Abel的工资
SELECT salary
FROM employees
WHERE last_name = 'Abel'

#②查询员工的信息，满足 salary>①结果
SELECT *
FROM employees
WHERE salary>(

	SELECT salary
	FROM employees
	WHERE last_name = 'Abel'

);

#案例2：返回job_id与141号员工相同，salary比143号员工多的员工 姓名，job_id 和工资

#①查询141号员工的job_id
SELECT job_id
FROM employees
WHERE employee_id = 141

#②查询143号员工的salary
SELECT salary
FROM employees
WHERE employee_id = 143

#③查询员工的姓名，job_id 和工资，要求job_id=①并且salary>②

SELECT last_name,job_id,salary
FROM employees
WHERE job_id = (
	SELECT job_id
	FROM employees
	WHERE employee_id = 141
) AND salary>(
	SELECT salary
	FROM employees
	WHERE employee_id = 143

);


#案例3：返回公司工资最少的员工的last_name,job_id和salary

#①查询公司的 最低工资
SELECT MIN(salary)
FROM employees

#②查询last_name,job_id和salary，要求salary=①
SELECT last_name,job_id,salary
FROM employees
WHERE salary=(
	SELECT MIN(salary)
	FROM employees
);


#案例4：查询最低工资大于50号部门最低工资的部门id和其最低工资

#①查询50号部门的最低工资
SELECT  MIN(salary)
FROM employees
WHERE department_id = 50

#②查询每个部门的最低工资

SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id

#③ 在②基础上筛选，满足min(salary)>①
SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
HAVING MIN(salary)>(
	SELECT  MIN(salary)
	FROM employees
	WHERE department_id = 50


);

#非法使用标量子查询

SELECT MIN(salary),department_id
FROM employees
GROUP BY department_id
HAVING MIN(salary)>(
	SELECT  salary
	FROM employees
	WHERE department_id = 250


);



#2.列子查询（多行子查询）★
#案例1：返回location_id是1400或1700的部门中的所有员工姓名

#①查询location_id是1400或1700的部门编号
SELECT DISTINCT department_id
FROM departments
WHERE location_id IN(1400,1700)

#②查询员工姓名，要求部门号是①列表中的某一个

SELECT last_name
FROM employees
WHERE department_id  <>ALL(
	SELECT DISTINCT department_id
	FROM departments
	WHERE location_id IN(1400,1700)


);


#案例2：返回其它工种中比job_id为‘IT_PROG’工种任一工资低的员工的员工号、姓名、job_id 以及salary

#①查询job_id为‘IT_PROG’部门任一工资

SELECT DISTINCT salary
FROM employees
WHERE job_id = 'IT_PROG'

#②查询员工号、姓名、job_id 以及salary，salary<(①)的任意一个
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<ANY(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';

#或
SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<(
	SELECT MAX(salary)
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';


#案例3：返回其它部门中比job_id为‘IT_PROG’部门所有工资都低的员工   的员工号、姓名、job_id 以及salary

SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<ALL(
	SELECT DISTINCT salary
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';

#或

SELECT last_name,employee_id,job_id,salary
FROM employees
WHERE salary<(
	SELECT MIN( salary)
	FROM employees
	WHERE job_id = 'IT_PROG'

) AND job_id<>'IT_PROG';



#3、行子查询（结果集一行多列或多行多列）

#案例：查询员工编号最小并且工资最高的员工信息



SELECT * 
FROM employees
WHERE (employee_id,salary)=(
	SELECT MIN(employee_id),MAX(salary)
	FROM employees
);

#①查询最小的员工编号
SELECT MIN(employee_id)
FROM employees


#②查询最高工资
SELECT MAX(salary)
FROM employees


#③查询员工信息
SELECT *
FROM employees
WHERE employee_id=(
	SELECT MIN(employee_id)
	FROM employees


)AND salary=(
	SELECT MAX(salary)
	FROM employees

);


#二、select后面
/*
仅仅支持标量子查询
*/

#案例：查询每个部门的员工个数


SELECT d.*,(

	SELECT COUNT(*)
	FROM employees e
	WHERE e.department_id = d.`department_id`
 ) 个数
 FROM departments d;
 
 
 #案例2：查询员工号=102的部门名
 
SELECT (
	SELECT department_name,e.department_id
	FROM departments d
	INNER JOIN employees e
	ON d.department_id=e.department_id
	WHERE e.employee_id=102
	
) 部门名;



#三、from后面
/*
将子查询结果充当一张表，要求必须起别名
*/

#案例：查询每个部门的平均工资的工资等级
#①查询每个部门的平均工资
SELECT AVG(salary),department_id
FROM employees
GROUP BY department_id


SELECT * FROM job_grades;


#②连接①的结果集和job_grades表，筛选条件平均工资 between lowest_sal and highest_sal

SELECT  ag_dep.*,g.`grade_level`
FROM (
	SELECT AVG(salary) ag,department_id
	FROM employees
	GROUP BY department_id
) ag_dep
INNER JOIN job_grades g
ON ag_dep.ag BETWEEN lowest_sal AND highest_sal;



#四、exists后面（相关子查询）

/*
语法：
exists(完整的查询语句)
结果：
1或0



*/

SELECT EXISTS(SELECT employee_id FROM employees WHERE salary=300000);

#案例1：查询有员工的部门名

#in
SELECT department_name
FROM departments d
WHERE d.`department_id` IN(
	SELECT department_id
	FROM employees

)

#exists

SELECT department_name
FROM departments d
WHERE EXISTS(
	SELECT *
	FROM employees e
	WHERE d.`department_id`=e.`department_id`


);


#案例2：查询没有女朋友的男神信息

#in

SELECT bo.*
FROM boys bo
WHERE bo.id NOT IN(
	SELECT boyfriend_id
	FROM beauty
)

#exists
SELECT bo.*
FROM boys bo
WHERE NOT EXISTS(
	SELECT boyfriend_id
	FROM beauty b
	WHERE bo.`id`=b.`boyfriend_id`

);

```
