###mysql基本语法
*********
>登录--`mysql -u 用户名-p 密码 `  
退出数据库--`exit；`  
##
>查询所的数据库--`show databases；`   
切换数据库--`use 数据库名； `   
显示数据库中所有的ID--`select * from 数据库名；`   
显示某一个项--`select * from 数据库名 where 索引 = 值；`    
##
>创建数据库--`create database 数据库名；`   
查看数据库中的列表名--`show tables;  `  
创建数据表--`create table 表名(表中成员名字以及属性)；`   
查看数据表的结构--`describe 表名；或者desc 表名；`    
添加数据记录--`insert into 表名 values(数据表列)；`   
修改数据--`update 表名 set 属性名=欲修改的值 where (用于定位)属性名=属性值；`    
删除数据记录--`delete from 表名 where 属性名=属性值；`   
##
>约束方式{主键约束 ；联合主键约束；自增约束；唯一约束；非空约束；默认约束；外键约束}  
主键约束：`primary key`，该项不能重复而且不能为空。    
联合主键约束：`primary key(表名，表名)`，两项加起来不能重复且不为空。   
自增约束：`primary key auto_increment`，某一列表的数据不赋值时可以自己增加。   
唯一约束：`unique(属性名)`，该属性的值不能重复。   
非空约束：`not null`，该属性的值不允许为空。   
默认约束：`default 值`，当赋值为空时以此值代替。   
外键约束：`foreign key(被约束的属性) references 表名(属性名)`。子表值包含于父表值。  
******
**更多详细的用法**[**点这里**](https://blog.csdn.net/hallomrzhang/article/details/85010014 "来自CSDN用户kangswx")