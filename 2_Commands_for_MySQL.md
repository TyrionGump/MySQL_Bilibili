# 一些简单命令

## 1 DBMS服务开关

```shell
mysql.server start
mysql.server stop
mysql.server restart
mysql.server status
```

## 2 DBMS登陆

```shell
# -p 意思用密码登陆
mysql -h [主机地址] -P [端口号] -u [用户名] -p
```

## 3 操作DB (后续会有更详细的)

```sql
# 查看所有存在的数据库
show databases;
# 打开数据库
use [database_name];
# 查看当前所在数据库的名称
select database();
# 查看当前数据库的表
show tables;
# 查看其他数据库的表
show tables from [database_name];
# 创建表 (表名为 stuinfo)
ceate table stuinfo(id int, name varchar(20));
# 查看表信息
desc stuinfo;
# 添加数据
insert into stuinfo (id, name) values(1, 'john');
# 修改数据
update stuinfo set name='lilei' where id=1;
```

## 4 SQL 的语法规范

1. 不区分大小写, 但建议关键字大写, 表明和列名小
2. 每条命令最好用 ; 结尾
3. 每条命令根据需要, 可以进行缩进或换行
4. 注释方法
   - 单行注释: # xxxx / -- xxxxx
   - 多行注释: /* xxxxx */
