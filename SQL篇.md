### 1、查找最晚入职员工的所有信息

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612345140124/0BFB4D140D9C3E92AF681D9F9CB92D55)

```mysql
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL, 
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```

请你查找employees里最晚入职员工的所有信息，以上例子输出如下:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612345279057/D2ABA1E2F5834850B16146F168AC5476)

```mysql
select * from employees order by hire_date desc limit 0,1;

select * from employees order by hire_date desc limit 1 offset 0;

#若不止一个
SELECT * FROM employees WHERE hire_date = (SELECT MAX(hire_date) FROM employees);
```





### 2、查找入职员工时间排名倒数第三的员工所有信息



```mysql
select * from (select * from employees order by hire_date desc limit 0,3) as t order by hire_date limit 0,1;

SELECT *
FROM employees
WHERE hire_date = (
    SELECT DISTINCT hire_date
    FROM employees
    ORDER BY hire_date DESC       -- 倒序
    LIMIT 1 OFFSET 2              -- 去掉排名倒数第一第二的时间，取倒数第三
);
```





### 3、查找当前薪水详情以及部门编号dept_no

有一个全部员工的薪水表salaries简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612509678777/C7D94B6C9124C45835451E89458FFC3E)



有一个各个部门的领导表dept_manager简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612509697144/24EC0AAEA6EF7D01BD63D4F9CCFC42BF)

```mysql
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));

CREATE TABLE `dept_manager` (
`dept_no` char(4) NOT NULL,
`emp_no` int(11) NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
```

请你查找各个部门当前领导的薪水详情以及其对应部门编号dept_no，输出结果以salaries.emp_no升序排序，并且请注意输出结果里面dept_no列是最后一列，以上例子输出如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612509718797/4AD4F3D1E977477D6DF98854EC10149D)



```mysql

select dept_manager.emp_no as emp_no,salary,from_date, dept_manager.to_date as to_date ,dept_no  from dept_manager left join salaries 
on dept_manager.emp_no = salaries.emp_no 
order by dept_manager.emp_no;
```



###  查找所有已经分配部门的员工的last_name和first_name以及dept_no

有一个员工表，employees简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612333206099/2DD8A8D108E674BCB32D6E32B03492C1)

有一个部门表，dept_emp简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612333284641/4BF7C729BECEE5D9215DF0C0B22855A0)

建表语句如下:

```mysql
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));


CREATE TABLE `dept_emp` (
`emp_no` int(11) NOT NULL,
`dept_no` char(4) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
```

请你查找所有已经分配部门的员工的last_name和first_name以及dept_no，未分配的部门的员工不显示，以上例子如下:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612333512668/92EDD0ECE3222E00AE2F555AC9153034)



```mysql
select e.last_name,e.first_name,d.dept_no 
from dept_emp d left join employees e on e.emp_no =d.emp_no;


select e.last_name,e.first_name,d.dept_no from dept_emp d , employees e where e.emp_no =d.emp_no;
```



请你查找所有已经分配部门的员工的last_name和first_name以及dept_no，也包括暂时没有分配具体部门的员工，以上例子如下:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612334245463/6CE804D2EED8A40F6DB3CA3B3F0E3BC8)

```mysql

select e.last_name,e.first_name,d.dept_no 
from employees e left join dept_emp d on e.emp_no =d.emp_no;
```



### 4、查找薪水记录超过15次的员工号emp_no以及其对应的记录次数t

有一个薪水表，salaries简况如下:



![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612337273467/8307279490CB9F89069769B3CDABC925)

建表语句如下:

```mysql
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```

请你查找薪水记录超过15次的员工号emp_no以及其对应的记录次数t，以上例子输出如下:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612337361503/85F393DA0762A39426D8D7C5958C8976)



```mysql

# 分组 组内计数
select emp_no,count(emp_no) t from salaries group by emp_no having t > 15;
```



###  5、找出所有员工当前薪水salary情况

有一个薪水表，salaries简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612339845721/0B07E4326822CEB36DFC0A8856794250)

建表语句如下:

```mysql
CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```

请你找出所有员工具体的薪水salary情况，对于相同的薪水只显示一次,并按照逆序显示，以上例子输出如下:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612339896739/D272D88115F2A8870C9D588A098CDD57)



```mysql
select distinct  salary from salaries order by salary desc;
```



### 6、获取所有非manager的员工emp_no

有一个员工表employees简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612344146571/013613CC3F594F2FB7444E6AD1DE4CDA)

有一个部门领导表dept_manager简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612344277205/EFBA0FC874C43A13F3732087E07217A6)

建表语句如下:

```mysql
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));

CREATE TABLE `dept_manager` (
`dept_no` char(4) NOT NULL,
`emp_no` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`dept_no`));
```



请你找出所有非部门领导的员工emp_no，以上例子输出:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612344409824/1EB9F3AEE88291A8FF5D3E30063143CF)



```mysql
#NOT IN+子查询
select emp_no from employees where emp_no not in (select emp_no from dept_manager);
#LEFT JOIN左连接+IS NULL
select e.emp_no as emp_no from employees  e left join dept_manager  d on e.emp_no =d.emp_no where dept_no is NULL; 
```



获取所有的员工和员工对应的经理，如果员工本身是经理的话则不显示，以上例子如下:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612347728685/C23E2BE72621CF021B0A53D9F763989B)

```mysql
select de.emp_no as emp_no,dm.emp_no as manager from dept_emp  de left join dept_manager  dm on de.dept_no = dm.dept_no where de.emp_no!=dm.emp_no;

```



### 7、获取每个部门中当前员工薪水最高的相关信息

有一个员工表dept_emp简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612351221393/34118A553B5FBFA94F49CD245C6D08FB)

有一个薪水表salaries简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210203/557336_1612351242250/04173C02C619D188DC48FAD2AC88B7AC)

获取每个部门中当前员工薪水最高的相关信息，给出dept_no, emp_no以及其对应的salary，按照部门编号升序排列，以上例子输出如下:（注意: Mysql与Sqlite select 非聚合列的结果可能不一样)

```mysql
CREATE TABLE `employees` (
`emp_no` int(11) NOT NULL,
`birth_date` date NOT NULL,
`first_name` varchar(14) NOT NULL,
`last_name` varchar(16) NOT NULL,
`gender` char(1) NOT NULL,
`hire_date` date NOT NULL,
PRIMARY KEY (`emp_no`));
```

请你查找employees表所有emp_no为奇数，且last_name不为Mary的员工信息，并按照hire_date逆序排列，以上例子查询结果如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612406764319/75058B99CE294E78DB66CA2FAAE53B88)



```mysql
select * from employees where emp_no%2=1 and last_name != 'Mary' order by hire_date desc;

select * from employees where MOD(emp_no, 2)=1 and last_name is not 'Mary' order by hire_date desc;

select * from employees where emp_no & 1 and last_name <> 'Mary' order by hire_date desc;

# 判断偶数 emp_no=(emp_no>>1<<1)
```



### 9、统计出当前各个title类型对应的员工当前薪水对应的平均工资

有一个员工职称表titles简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612407668581/E5553A8E790B6E2EDA66C282AA827977)



有一个薪水表salaries简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612407714084/4E896A0A868FA22C39847419CA7A0938)

建表语句如下:

```mysql
CREATE TABLE titles (
`emp_no` int(11) NOT NULL,
`title` varchar(50) NOT NULL,
`from_date` date NOT NULL,
`to_date` date DEFAULT NULL);

CREATE TABLE `salaries` (
`emp_no` int(11) NOT NULL,
`salary` int(11) NOT NULL,
`from_date` date NOT NULL,
`to_date` date NOT NULL,
PRIMARY KEY (`emp_no`,`from_date`));
```

请你统计出各个title类型对应的员工薪水对应的平均工资avg。结果给出title以及平均工资avg，并且以avg升序排序，以上例子输出如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612407818920/A7336774C0A61EBF660CAAD47F6B5FDC)

```mysql

select title,avg(salary) from titles t inner join salaries s on t.emp_no=s.emp_no group by title  order by avg(salary)
```

### 10、获取当前薪水第二多的员工的emp_no以及其对应的薪水salary

有一个薪水表salaries简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612408244515/6C9242F7D3C8008B398406BC63A4CEF2)

请你获取薪水第二多的员工的emp_no以及其对应的薪水salary

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612408313394/A387FD5CA0C402A5D9623DE2325039BC)

```mysql

select emp_no,salary from salaries where salary =(select salary from salaries group by salary order by salary desc limit 1,1);

```





有一个员工表employees简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612411334878/D90875118E960F00405A3E8200D323BB)

有一个薪水表salaries简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612411421371/B11CDB910B447AF15705E5DE4A57B496)

请你查找薪水排名第二多的员工编号emp_no、薪水salary、last_name以及first_name，**不能使用order by完成**，以上例子输出为:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612411478987/A864E1C667401C06680F1FCE127DF314)



```mysql
select e.emp_no as emp_no,salary,last_name,first_name from employees e inner join salaries s on e.emp_no = s.emp_no 
where salary =(
    select max(s.salary) from salaries s   where s.salary<(            
        select  max(salary) from salaries
    ) 
);
```



### **11、查找所有员工的last_name和first_name以及对应的dept_name**

有一个员工表employees简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612425668678/6D9FFF5B1FDE1BF0E1E9D1396D67CA49)



有一个部门表departments表简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612425711152/C24CBBDE019ABEEEB1835C750CAAC219)



有一个，部门员工关系表dept_emp简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612426050115/3F560B6ECCA07CE59A293F9B6391D9A5)

请你查找所有员工的last_name和first_name以及对应的dept_name，也包括暂时没有分配部门的员工，以上例子输出如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612426091801/F02CEDCA7F2DA40C99E3BB9B269AF5CA)

```mysql

select last_name,first_name,dept_name from (employees e left join dept_emp d_e on e.emp_no=d_e.emp_no) 
    left join departments d on d_e.dept_no = d.dept_no 
```



### **12、查找在职员工自入职以来的薪水涨幅情况**

有一个员工表employees简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612427898891/6A455073C6426E8FDD2A87F772BD7FEC)

有一个薪水表salaries简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612427923597/2ACC33C101FA65EBC19E5D29837C488C)

请你查找在职员工自入职以来的薪水涨幅情况，给出在职员工编号emp_no以及其对应的薪水涨幅growth，并按照growth进行升序，以上例子输出为

（注: to_date为薪资调整某个结束日期，或者为离职日期，to_date='9999-01-01'时，表示依然在职，无后续调整记录）

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612428040490/74BFFC89978849110987F3072956D640)



```mysql

select s1.emp_no as emp_no, (s1.salary - s2.salary) as growth  from  
(select emp_no,salary  from salaries where to_date = '9999-01-01') s1 inner join
(select s.emp_no as emp_no ,s.salary salary from salaries s,employees e where e.hire_date =s.from_date and e.emp_no =s.emp_no) s2
on s1.emp_no = s2.emp_no
order by growth
```

### **13、统计各个部门的工资记录数**

有一个部门表departments简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612429865438/8AD27235034DA18734B6B3E7A5751B55)

有一个，部门员工关系表dept_emp简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612429903049/4788B24C7EB1E0BC617566EEA96D1363)

有一个薪水表salaries简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612429948973/87D9F6301D3B1216A803BF8C69BC3F96)

请你统计各个部门的工资记录数，给出部门编码dept_no、部门名称dept_name以及部门在salaries表里面有多少条记录sum，按照dept_no升序排序，以上例子输出如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612430009094/7FF5866CD1770ED899126D371E73AD5F)

```mysql
SELECT d.dept_no, d.dept_name, count(s.salary) AS SUM
FROM departments d right JOIN dept_emp de ON d.dept_no = de.dept_no
right JOIN salaries s ON de.emp_no = s.emp_no
GROUP BY d.dept_no
ORDER BY d.dept_no;

```

### **14、对所有员工的薪水按照salary进行按照1-N的排名**



有一个薪水表salaries简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612431764809/90BB53FD229FA71DC9DE5E1B8A43341C)

对所有员工的薪水按照salary进行按照1-N的排名，相同salary并列且按照emp_no升序排列：

![img](https://uploadfiles.nowcoder.com/images/20210204/557336_1612431811186/80C983CAFA18B7D88C56A3976AD988E6)

```mysql
select emp_no,salary,dense_rank() over (order by salary desc) as t_rank from salaries order by t_rank,emp_no ;
```



### 15、获取所有非manager员工当前的薪水情况

有一个员工表employees简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612497747565/26AFECBE75E980DEA1DFDF163E085983)

有一个，部门员工关系表dept_emp简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612497787496/095668522520FB1988913216A7A9185D)

有一个部门经理表dept_manager简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612497840657/7D6F286C71377A062F7BFF16DFBAE8BD)

有一个薪水表salaries简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612497875887/175A34988A2749897249CA2816ACFE9B)

获取所有非manager员工薪水情况，给出dept_no、emp_no以及salary，以上例子输出:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612497914291/510475DC218ACAB53FB17B1B6D649B07)

```mysql
select t.dept_no dept_no,t.emp_no emp_no,salary from 
((select de.dept_no dept_no,de.emp_no emp_no from dept_emp de,dept_manager dm where  de.dept_no =dm.dept_no and de.emp_no != dm.emp_no) as t
inner join salaries s on t.emp_no =s.emp_no)
```

### 16、获取员工其当前的薪水比其manager当前薪水还高的相关信息

有一个，部门关系表dept_emp简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612498993292/8232B00BADA1691E28A63212325D2E46)

有一个部门经理表dept_manager简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612499038439/3EB123B01D9B89A5E9E880FF977DAA01)

有一个薪水表salaries简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612499075011/00558E69C5D1A39BC0D5B16A6DE5700C)

获取员工其当前的薪水比其manager当前薪水还高的相关信息，

第一列给出员工的emp_no，
第二列给出其manager的manager_no，
第三列给出该员工当前的薪水emp_salary,
第四列给该员工对应的manager当前的薪水manager_salary

以上例子输出如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612499129726/11EC598A7AF7F05564579082F20916F0)

```mysql
select t.emp_no emp_no,t.manager_no manager_no,s1.salary emp_salary,s2.salary manager_salary from
(select de.emp_no emp_no,de.dept_no dept_no,dm.emp_no manager_no  from  dept_emp de inner join dept_manager dm on de.dept_no =dm.dept_no) t
inner join salaries s1 on  t.emp_no = s1.emp_no
inner join salaries s2 on  t.manager_no = s2.emp_no
where s1.salary>s2.salary


```





### 17、汇总各个部门当前员工的title类型的分配数目

有一个部门表departments简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612508332556/1EAFE9D27CFBE237FB8A5282F0CCD79C)

有一个，部门员工关系表dept_emp简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612508380774/A475E78CCFA8C64CF1EBA0E1C08E1354)

有一个职称表titles简况如下:

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612508421682/D79975CA2E30B011ACC394716A714C26)

汇总各个部门当前员工的title类型的分配数目，即结果给出部门编号dept_no、dept_name、其部门下所有的员工的title以及该类型title对应的数目count，结果按照dept_no升序排序

![img](https://uploadfiles.nowcoder.com/images/20210205/557336_1612508518851/E0ED943D642979484B08DF21D5F76C22)



```mysql

select de.dept_no dept_no,dept_name,title,count(*) count from dept_emp de 
inner join  titles t on de.emp_no = t.emp_no
left join departments d on de.dept_no =d.dept_no
group by de.dept_no,title
order by de.dept_no
```

### 18、查找描述信息中包含robot的电影对应的分类名称以及电影数目，而且还需要该分类对应电影数量>=5部



```mysql
CREATE TABLE IF NOT EXISTS film (
film_id smallint(5)  NOT NULL DEFAULT '0',
title varchar(255) NOT NULL,
description text,
PRIMARY KEY (film_id));

CREATE TABLE category  (
category_id  tinyint(3)  NOT NULL ,
name  varchar(25) NOT NULL, `last_update` timestamp,
PRIMARY KEY ( category_id ));

CREATE TABLE film_category  (
film_id  smallint(5)  NOT NULL,
category_id  tinyint(3)  NOT NULL, `last_update` timestamp);
```



```mysql
select n.name,count(c.film_id) from
(select category_id,name from category where category_id in 
(select category_id from film_category  group by category_id having count(film_id)>=5)) n
inner join film_category c on n.category_id =c.category_id
inner join film f on c.film_id = f.film_id
where f.description like '%robot%';
```



### 19、使用join查询方式找出没有分类的电影id以及名称

```mysql
select f.film_id,title from film f left join film_category fc on f.film_id =fc.film_id  where category_id is NULL;

```



### 20、使用子查询的方式找出属于Action分类的所有电影对应的title,description

```mysql
select title,description from  film where film_id  in (
    select film_id from film_category where category_id =(
        select category_id from category where name ="Action")
);
```

### 21、将employees表的所有员工的last_name和first_name拼接起来作为Name

```mysql

select concat(last_name,' ',first_name) Name from employees
```



### 22、**创建一个actor表，包含如下列信息**

![1622862646869](C:\Users\DD15\AppData\Roaming\Typora\typora-user-images\1622862646869.png)

```mysql

create table  if not exists actor(
    actor_id smallint(5) not null,
    first_name varchar(45) not null,
    last_name varchar(45) not null,
    last_update date not null,
    primary key(actor_id)
);
```



### 23、**批量插入数据**

题目已经先执行了如下语句:

```mysql
drop table if exists actor;
CREATE TABLE actor (
   actor_id  smallint(5)  NOT NULL PRIMARY KEY,
   first_name  varchar(45) NOT NULL,
   last_name  varchar(45) NOT NULL,
   last_update  DATETIME NOT NULL)
```

请你对于表actor批量插入如下数据(不能有2条insert语句哦!)

![1622864284816](C:\Users\DD15\AppData\Roaming\Typora\typora-user-images\1622864284816.png)

```mysql
insert into actor values(1,'PENELOPE','GUINESS','2006-02-15 12:34:33'),(2,'NICK','WAHLBERG','2006-02-15 12:34:33');

```

**批量插入数据，不使用replace操作**

题目已经先执行了如下语句:

```mysql
drop table if exists actor;
CREATE TABLE actor (
   actor_id  smallint(5)  NOT NULL PRIMARY KEY,
   first_name  varchar(45) NOT NULL,
   last_name  varchar(45) NOT NULL,
   last_update  DATETIME NOT NULL);
insert into actor values ('3', 'WD', 'GUINESS', '2006-02-15 12:34:33');
```

![1622864519256](C:\Users\DD15\AppData\Roaming\Typora\typora-user-images\1622864519256.png)

```mysql
insert ignore into actor values ('3', 'ED', 'CHASE', '2006-02-15 12:34:33') ;

```

### 24、**创建一个actor_name表**

![1622864652433](C:\Users\DD15\AppData\Roaming\Typora\typora-user-images\1622864652433.png)

```mysql

create table actor_name(
    first_name varchar(45) not null,
    last_name varchar(45) not null
);
insert into actor_name select first_name,last_name from actor;
```

### 25、	创建和删除索引

```mysql
alter table actor  add primary key idx_lastname (last_name);
ALTER TABLE tbl_name ADD FULLTEXT index_name (col_list);
alter table actor  add unique uniq_idx_firstname (first_name);
alter table actor  add index idx_lastname (last_name);

#删除索引
DROP INDEX index_name ON tbl_name;
# 或者
ALTER TABLE tbl_name DROP INDEX index_name；
ALTER TABLE tbl_name DROP PRIMARY KEY;
```

### 26、创建视图

```mysql
create view actor_name_view  as select first_name  first_name_v,last_name  last_name_v from actor  ;

CREATE [TEMP | TEMPORARY] VIEW view_name [(name1,name2)]AS
SELECT column1, column2.....
FROM table_name
WHERE [condition];
```

### 27、使用索引查询

```mysql
SELECT|DELETE|UPDATE column1, column2... from table_name
force index (idx_emp_no)
WHERE (CONDITION);

select * from salaries force index (idx_emp_no)   where emp_no =10005;

```

### 28、修改表

```mysql
alter table actor  add column (create_date datetime NOT NULL default '2020-10-01 00:00:00')

ALTER TABLE 表名 ADD 列名/索引/主键/外键等；
ALTER TABLE 表名 DROP 列名/索引/主键/外键等；
ALTER TABLE 表名 ALTER 仅用来改变某列的默认值；
ALTER TABLE 表名 RENAME 列名/索引名 TO 新的列名/新索引名；
ALTER TABLE 表名 RENAME TO/AS 新表名;
ALTER TABLE 表名 MODIFY 列的定义但不改变列名；
ALTER TABLE 表名 CHANGE 列名和定义都可以改变。

#增加外键约束
ALTER TABLE <表名>
ADD CONSTRAINT FOREIGN KEY (<列名>)
REFERENCES <关联表>（关联列）
```



### 30、创建触发器

```mysql
CREATE TRIGGER trigger_name
trigger_time trigger_event ON tbl_name
FOR EACH ROW
trigger_stmt

create trigger audit_log
after insert on employees_test
for each row
insert into audit values(new.id,new.name);
```



### last 增删改查

```mysql
insert into table values()
delete FROM  table WHERE condition
update titles_test  set to_date =NULL,from_date ='2001-01-01'  where to_date ='9999-01-01'
```



### other

```mysql
#查找字符串中‘，’的次数
select (length("10,A,B") - length(replace("10,A,B",",","")))as cnt;
#MySQL获取指定位置的字符串函数RIGHT和LEFT
SELECT first_name FROM employees ORDER BY RIGHT(first_name,2);

```



### 分页

```mysql
#SELECT * FROM TABLE_NAME LIMIT (PageNO-1)*PageSize,PageSize;
select * from employees
order by emp_no
limit 5,5;
# offset (PageNO-1)*PageSize,PageSize;

```

