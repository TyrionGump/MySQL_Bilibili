# 数据查询语言

- [数据查询语言](#数据查询语言)
  - [1 SELECT](#1-select)
    - [1.1 紧跟 SELECT 的选项](#11-紧跟-select-的选项)
    - [1.2 给选出的结果的列去另外一个名字](#12-给选出的结果的列去另外一个名字)
    - [1.3 查询结果去重](#13-查询结果去重)
    - [1.4 查询结果横向合并显示 (以同时显示 first_name 和 last_name 为例)](#14-查询结果横向合并显示-以同时显示-first_name-和-last_name-为例)
  - [2 条件查询 WHERE](#2-条件查询-where)
    - [2.1 运算符](#21-运算符)
    - [2.2 LIKE](#22-like)
    - [2.3 BETWEEN AND](#23-between-and)
    - [2.4 IN](#24-in)
    - [2.5 IS NULL](#25-is-null)
    - [2.6 安全等于](#26-安全等于)
  - [3 排序查询](#3-排序查询)
  - [4 函数](#4-函数)
    - [4.1 单行函数](#41-单行函数)
      - [4.1.1 字符函数](#411-字符函数)
      - [4.1.2 数学函数](#412-数学函数)
      - [4.1.3 日期函数](#413-日期函数)
      - [4.1.4 其他函数](#414-其他函数)
      - [4.1.5 流程控制函数](#415-流程控制函数)
    - [4.2 聚合 (分组) 函数](#42-聚合-分组-函数)
  - [5 分组查询](#5-分组查询)
    - [5.1 简单案例](#51-简单案例)
  - [6 连接查询 (sql192标准)](#6-连接查询-sql192标准)
    - [6.1 内连接](#61-内连接)
      - [6.1.1 等值连接](#611-等值连接)
      - [6.1.2 非等值连接](#612-非等值连接)
      - [6.1.3 自连接](#613-自连接)
  - [7 连接查询 (sql199标准)](#7-连接查询-sql199标准)
    - [7.1 内连接](#71-内连接)
      - [7.1.1 等值连接](#711-等值连接)
      - [7.1.2 非等值连接](#712-非等值连接)
      - [7.1.3 自连接](#713-自连接)
    - [7.2 外连接](#72-外连接)
      - [7.2.1 左外连接 / 右外连接](#721-左外连接--右外连接)
      - [7.2.3 全外连接 (MySQL 不支持)](#723-全外连接-mysql-不支持)
    - [7.3 交叉连接](#73-交叉连接)
  - [7 子查询](#7-子查询)
    - [7.1 WHERE 或 HAVING 后面](#71-where-或-having-后面)
      - [7.1.1 标量子查询](#711-标量子查询)
      - [7.1.2 列子查询 (多行子查询)](#712-列子查询-多行子查询)
      - [7.1.3 行子查询](#713-行子查询)
    - [7.2 SELECT 后面](#72-select-后面)
    - [7.3 FROM 后面](#73-from-后面)
    - [7.4 EXISTS()后面](#74-exists后面)
  - [8 分页查询](#8-分页查询)
  - [9 联合查询](#9-联合查询)

## 1 SELECT

### 1.1 紧跟 SELECT 的选项

1. 后面可以跟列名
2. 后面可以跟常量值 (e.g. 100 * 10, 'sdsafsf'). 帮助计算??
3. 可以跟函数返回函数值 (e.g. VERSION())

### 1.2 给选出的结果的列去另外一个名字

```sql
SELECT column_xxx FROM table_xxx AS [alias_name];
-- 或者
SELECT column_xxx [alias_name] FROM table_xxx;
```

### 1.3 查询结果去重

给查询列前修饰一个关键词 DISTINCT.

```sql
SELECT DISTINCT column_xxx FROM table_xxx;
```

### 1.4 查询结果横向合并显示 (以同时显示 first_name 和 last_name 为例)

```sql
SELECT CONCAT(last_name, first_name) AS full_name FROM table_xxx
```

注意: 如果其中一个字段的数据出现 null, 那么该行数据的拼接结果仍为 null.
可以使用 IFNULL(column_xxx, replacement_value) 来解决.

## 2 条件查询 WHERE

### 2.1 运算符

1. 条件运算符: <, >, =, != (<>)
2. 逻辑运算符: && (and), || (or), ! (not)
3. 模糊查询: like, between and, in, is null.

### 2.2 LIKE

1. 一般与通配符搭配使用
2. % 代表任意多个字符, 包含0个字符
3. _ 代表单个字符 (用于限定第几个字符是什么)
4. 如果想要查的东西包含通配符, 可使用 \ 进行转译
5. 其实也可以自定义转译符号, 利用关键词 ESCAPE
6. 也可以判断数值行

```sql
-- 利用 $ 作为我们的转译符号
SELECT last_name FROM employees WHERE last_name LIKE '_$_%' ESCAPE '$';
```

### 2.3 BETWEEN AND

1. 这样写节省我们使用两次条件判断
2. 注意 Between and 是包含临界值, 即 >= 第一个条件 AND <= 第二个条件;
3. 因此, 要注意第一个和第二个条件的顺序
4. 相反的可以直接写为 column_xxx NOT BETWEEN value_1 AND value_2;

```sql
SELECT * FROM employees WHERE employee_id(判断条件的列名) BETWEEN 90 AND 100;
```

### 2.4 IN

1. 查询某一列所包含的 value 满足 IN 后多个 value 的数据
2. 可以代替多个用 OR 连接的 = 判断条件
3. IN 列表中的值必须一致或者兼容 ('123' 和 123 兼容)
4. 不支持通配符进行模糊查询, 因为它本身是 = 判断条件的替代. 模糊查询是 LIKE 的东西
 
```sql
SELECT * FROM employees WHERE job_id IN('xxx', 'xxx', 'xxx');
```

### 2.5 IS NULL

1. = 判断条件是不能判断 null 值的
2. 注意这里 IS 就是和 NULL 搭配的. 不能用于类似 = 判断条

```sql
SELECT * FROM employees WHERE xxx IS NULL;
-- 相反的话, 如果想查询非 null 的
SELECT * FROM employees WHERE xxx IS NOT NULL;
```

### 2.6 安全等于

1. is null/is not null：仅可判断null值，可读性较高
2. <=>安全等于：既可以判断null值，又可以判断普通的数值
3. =不可判断null值，仅可判断数值

## 3 排序查询

1. 默认是升序 ASC
2. ORDER BY 在条件查询 WHERE 后, 一般放在查询语句的最后面, 除了 **LIMIT**.
3. 支持别名
4. 支持函数
5. 支持按多个字段 (attribute) 排序

```sql
SELECT column_xxx FROM table_xxx ORDER BY column2_xxx [ASC|DESC];
-- 支持别名
SELECT salary * 12 * (1 + IFNULL(commission_pct, 0)) AS year_salary 
FROM employees ORDER BY year_salary ASC;
--支持函数
SELECT LENGTH(last_name) AS string_length, last_name, salary FROM employees ORDER BY LENGTH(last_name) DESC;
-- 多字段排序 (先根据第一个字段排序, 再根据第二个字段排序)
SELECT * FROM employees ORDER BY salary ASC, employee_id DESC;
```

## 4 函数

1. 将一组逻辑语句封装在方法体中, 对外暴露方法名
2. 隐藏了实现细节, 提高代码的重用性
3. 分类
   - 单行函数: 针对每一行数据执行一次返回一个值 (concat, length, ifnull)
   - 聚合函数 / 分组函数 / 统计函数 / 组函数: 针对一列统计得到一个值

### 4.1 单行函数

#### 4.1.1 字符函数

- LENGTH() 获取参数值的字节个数 (**注意是字节长度不是字符长度**)

```sql
SELECT LENGTH('john');
-- 中文长度根据所选的字符集而定. e.g. utf-8 中一个中文字符长度为3
SELECT LENGTH('孙钰博haha');
-- 该例子返回数字为 13.
```

- CONCAT() 拼接字符串(**非字符串类型会转换为字符串再拼接**)

```sql
SELECT CONCAT(first_name, '_', last_name) FROM employees;
```

- UPPER() / LOWER() 大小写
  
```sql
SELECT CONCAT(UPPER(first_name), '_', LOWER(last_name)) FROM employees;
```

- SUBSTR() / SUBSTRING() 字符串截取

```sql
-- mysql的索引起点是 1. 截取 start_postion 本身及其之后的所有字符串
SELECT SUBSTR('1234567', start_postion) AS out_put;
-- 从 start_postion 本身开始截取长度为 selected_length 的字符串
SELECT SUBSTR('1234567', start_postion, selected_length) AS out_put;
```

- INSTR() 搜索某一字符串在另一字符串的起始位置

```sql
-- 返回5. 注意前一个字符串必须完全包括子字符串, 不存在部分匹配. 不存在则返回 0.
SELECT SUBSTR('1234567', '567') AS out_put;
```

- TRIM() 去除字符串前后空格或自定义字符

```sql
-- 返回 1234567
SELECT TRIM('  1234567  ') AS out_put;
SELECT TRIM('a' FROM 'aaaaaaaaaa1234567aaaaaaaaaaa') AS out_put;
-- 
```

- LPAD() 用指定的自负实现字符串的指定长度的左填充
- RPAD() 用指定的自负实现字符串的指定长度的右填充

```sql
-- 返回‘12’
SELECT LPAD('123', 2, '*');
-- 返回‘**123’
SELECT LPAD('123', 5, '*');
-- 返回'123**'
SELECT RPAD('123', 5, '*');
```

- REPLACE() 字符串替换
  
```sql
-- 返回 111111111
SELECT REPLACE('123123123', '123', '111') AS out_put;
```

#### 4.1.2 数学函数

- ROUND() 四舍五入

```sql
-- 返回 2
SELECT ROUND(1.5);
-- 返回 -2
SELECT ROUND(-1.5);
-- 返回 1.57
SELECT ROUND(1.567, 2)
```

- CEIL() 向上取整 (返回 >= 该参数的最小整数, **涉及到负数与上面不同的情况**)

```sql
-- 返回 2
SELECT CEIL(1.00000001);
-- 返回 -1
SELECT CEIL(-1.000000001);
```

- FLOOR() 向下取整 (返回 <= 该参数的最大整数, **涉及到负数与上面不同的情况**)

```sql
-- 返回 1
SELECT FLOOR(1.00000001);
-- 返回 -2
SELECT FLOOR(-1.000000001);
```

- TRUNCATE() 在某一位截断数字, 丢弃掉该位之后的所有.

```sql
-- 返回 1.5
SELECT TRUNCATE(1.59999, 1);
```

- MOD() 取余数

```sql
-- 返回 1
SELECT MOD(10, 3);
-- 返回 1
SELECT MOD(10, -3);
-- 返回 -1
SELECT MOD(-10, -3);
-- 返回 -1
SELECT MOD(-10, 3);
```

#### 4.1.3 日期函数

- NOW() 返回当前系统日期 + 时间
- CURDATE() 返回当前系统日期
- CURTIME() 返回当前时间
**接下来的几个函数的参数可以上上述三种函数的返回值, 也可以是字段为datetime或对应格式日期字符串**
- YEAR()
- MONTH()
- MONTHNAME() 返回英文月份
- DAY()
- HOUR()
- MINUTE()
- SECOND()
- STR_TO_DATE() 字符串解析为 datetime 的格式

```sql
-- 返回 1999-09-13. 第二个参数是解释第一个参数的 pattern
SELECT STR_TO_DATE('9-13-1999', '%m-%d-%Y');
```

- DATE_FORMAT() 将日期转换为字符

```sql
-- 返回 2018年06月06日. 第二个参数是想把第一个 datetime 变成什么样
SELECT DATE_FORMAT('2018/6/6', '%Y年%m月%d日');
```

| 格式符 | 功能                       |
| ------ | -------------------------- |
| %Y     | 4位的年份                  |
| %y     | 2位的年份                  |
| %m     | 月份 (01, 02, ..., 11, 12) |
| %c     | 月份 (1, 2, ..., 11, 12)   |
| %d     | 日 (01, 02 ...)            |
| %H     | 小时 (24小时制)            |
| %h     | 小时 (12小时制)            |
| %i     | 分钟 (00, 01, ..., 59)     |
| %s     | 秒 (00, 01, ..., 59)       |

#### 4.1.4 其他函数

- VERSION()
- DATABASE()
- USER()

#### 4.1.5 流程控制函数

- IF(condition, result1, result2) 判断条件, 满足返回第二个参数, 否则返回第三个参数 (类似 if else)

```sql
SELECT last_name, commission_pct, IF(commission_pct IS NULL, '???', 'rich') AS new_info
FROM employees;
```

- CASE() 函数的第一种用法 (判断某一列等于某个数不)
- CASE 选取哪一列作为基准判断
- WHEN [condition] THEN [operation]
- END -> 其实这个结构就是根据某一列的情况对改列或另一列数据的输出样式进行重新设计

```sql
-- salary 和 department_id 不影响CASE里面的使用. 因此第一行的两个列名写不写不影响后面.
SELECT salary, department_id, 
CASE department_id
WHEN 30 THEN salary * 1.1
WHEN 40 THEN salary * 1.2
WHEN 50 THEN salary * 1.3
ELSE salary
END AS real_salary
FROM employees;
```

- CASE() 函数的第二种用法
- CASE
- WHEN [conditino] THEN [operation]
- END -> **类似多重 if 判断**.

```sql
SELECT salary,
CASE
WHEN salary > 20000 THEN 'A'
WHEN salary > 15000 THEN 'B'
WHEN salary > 10000 THEN 'C'
ELSE 'D'
END AS salary_level
FROM employees;
```

### 4.2 聚合 (分组) 函数

对某一部分数据进行统计返回一个只值. **这些函数都忽略 null 值.**

- SUM()
- AVG()
- 虽然前两个函数在非数字类型的数字类型上可以正常运行, 但是结果往往不是我们想要的
- MAX()
- MIN()
- COUNT()

```sql
-- * 代表查询整个表一共有几行. 这样可以避免单独查某些存在 null 值的列而导致结果不准的情况.
SELECT COUNT(*) FROM employees;

-- 其实随便写一个常量在 count() 里都可以. 相当于添加了都等于该常量的列到表中, 然后统计该列的行数.
SELECT COUNT(1) FROM employees;
```

和分组函数能同时一起查询的只有group by后的字段.

## 5 分组查询

使用 GROUP BY 将表中的数据分成若干组.
一般语法结构如下:

```sql
-- 其中 column_1 如果存在必须出现在 group by 后面. 当然很少有不出现的情况, 不然统计出来没有意义..
SELECT column_1, group_function(column_2)
FROM table
WHERE condition
GROUP BY group_by_expression
ORDER BY column_1;
```

### 5.1 简单案例 

- 查询每个工种的最高工资

```sql
SELECT job_id, MAX(salary)
FROM employees
GROUP BY job_id;
```

- 查询每个位置上的部门个数

```sql
SELECT location_id, COUNT(*)
FROM departments
GROUP BY location_id;
```

- 查询邮箱中包含 a 字符的, 每个部门的平均工资

```sql
SELECT department_id, AVG(salary)
FROM employees
WHERE email LIKE '%a%'
GROUP BY department_id;
```

- 查询每个领导手下有奖金的员工的最高工资

```sql
SELECT MAX(salary), manager_id
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY manager_id;
```

- 查询哪个部门的员工个数大于2 **(分组后的筛选)**
  
```sql
-- 第一种笨办法, 先统计各部门的人数然后, 再从这个中间 view 结果中提取想要的结果 (注意, 中间view要有自己的名字 e.g. stats)
SELECT department_id
FROM (SELECT department_id, count(*) AS employee_num
      FROM employees
      GROUP BY department_id) AS stats
WHERE employee_num > 2;

-- 第二种方法 HAVING 里的COUNT应该不是函数, 应该是列名叫COUNT(*)的列. 和第一行的不一样. 我感觉...
SELECT department_id, COUNT(*)
FROM employees
GROUP BY department_id
HAVING COUNT(*) > 2
```

- 查询每个工种有奖金的员工的最高工资大于12000的工种编号和最高工资

```sql
SELECT job_id, MAX(salary)
FROM employees
WHERE commission_pct IS NOT NULL
GROUP BY job_id
HAVING MAX(salary) > 12000;
```

- 查询领导编号 > 120 的每个领导手下的最低工资 > 5000 的领导编号以及其最低工资.

```sql
SELECT manager_id, MIN(salary)
FROM employees
WHERE manager_id > 120
GROUP BY manager_id
HAVING MIN(salary) > 5000;

-- WHERE 放到 HAVING 好像也行 (考虑到性能, 能先用 WHERE 筛选就用 WHERE 筛选)
SELECT manager_id, MIN(salary)
FROM employees
GROUP BY manager_id
HAVING MIN(salary) > 5000 AND manager_id > 120;
```

WHERE 是分组前的筛选, HAVING 是分组后的筛选

- 按员工姓名的长度分组, 查询每一组的员工个数, 筛选员工个数大于5的结果

```sql
SELECT LENGTH(last_name), COUNT(*)
FROM employees
GROUP BY LENGTH(last_name)
HAVING COUNT(*) > 5;
```

- 查询每个部门每个工种的员工的平均工资 **(按多个字段分组)**

```sql
-- 注意多个字段分组用逗号连接而不是用 AND
SELECT AVG(salary)
FROM employees
GROUP BY department_id, job_id;
```

- 查询每个部门每个工种的员工的平均工资, 并且按平均工资从高到低显示 **(ORDER BY 放在最后面)**

```sql
-- 注意多个字段分组用逗号连接而不是用 AND
SELECT AVG(salary)
FROM employees
GROUP BY department_id, job_id
ORDER BY AVG(salary) DESC;
```

## 6 连接查询 (sql192标准)

### 6.1 内连接

#### 6.1.1 等值连接

表 1 的每一行和表 2 的每一行一一匹配 (笛卡尔乘积), 然后根据 WHERE 一一筛选出满足条件的结果

```sql
-- 查询员工名和对应的部门名
SELECT last_name, department_name
FROM employees, departments
WHERE employees.department_id = departments.department_id;

-- 查询员工名和工种号和工种名 (注意查询的列如果在两个表中都存在一样的名字, 要指明是查询哪个表中的该列)
SELECT last_name, employees.job_id, job_name
FROM employees, jobs
WHERE employees.job_id = jobs.job_id;

-- 查询城市名中第二个字符为 o 的部门名和城市名 (WHERE 里的条件顺序不影响查询结果)
SELECT department_name, city
FROM departments, locations
WHERE city LIKE '_o%' AND departments.location_id = locations.location_id;

-- 查询每个城市的部门个数
SELECT city, COUNT(*) AS department_num
FROM locations, departments
WHERE locations.location_id = departments.location_id
GROUP BY city;

-- 查询有奖金的每个部门的部门名和部门的领导编号和该部门的最低工资
SELECT department_name, departments.manager_id, MIN(salary)
FROM departments, employees
WHERE commission_pct IS NOT NULL AND departments.department_id = employees.department_id
GROUP BY departments.department_id;
```

#### 6.1.2 非等值连接

由于后续案例需要用到一张新表和数据, **记得执行 data 文件中 job_grade 的 query 文件添加表.**

```sql
-- 查询员工的工资和工资级别
SELECT salary, grade_level
FROM employees, job_grades
WHERE salary >= lowest_sal AND salary <= highest_sal;
-- 或者
SELECT salary, grade_level
FROM employees, job_grades
WHERE salary BETWEEN lowest_sal AND highest_sal;

```

#### 6.1.3 自连接

想要找的信息都在一张表里. 此时, 一张表被查两次, 看起来像是两张表一样.

```sql
-- 查询员工名和其上级的名字
-- 注意这里自连接两张表名会造成混淆, 没张表都应该有一个独特的名字
-- 因此给两张表分别取别名
-- 由于 query 语句先执行 FROM, 因此取完别名之后, 原表明就无效了, 以后对表操作就要用别名.
SELECT e.employee_id, e.last_name, m.employee_id, m.last_name
FROM employees AS e, employees AS m
WHERE e.manager_id = m.employee_id;
```

## 7 连接查询 (sql199标准)

### 7.1 内连接

核心是 FROM [table1] INNER JOIN [table2] ON [join_condition]

- INNER 可以省略
- 筛选条件放在 WHERE, 连接条件放在 ON 后面, 提高了可读性
- 这部分 199 和 192 结果一样, 原理也一样.

#### 7.1.1 等值连接

```sql
-- 查询员工名和部门名
SELECT last_name, department_name
FROM employees INNER JOIN departments
ON employees.department_id = departments.department_id;

-- 查询名字中包含 e 的员工名和工种名 (添加筛选)
SELECT last_name, job_title
FROM employees INNER JOIN jobs ON employees.job_id = jobs.job_id
WHERE last_name LIKE '%e%';

-- 查询部门个数 > 3 的城市名和部门个数 (添加分组 + 筛选)
SELECT city, COUNT(*) AS department_num
FROM departments INNER JOIN locations ON departments.location_id = locations.location_id
GROUP BY city
HAVING department_num > 3;

-- 查询哪个部门的员工个数大于3的部门名和员工个数, 并按个数降序 (添加排序)
SELECT department_name, COUNT(*) AS employees_num
FROM employees INNER JOIN departments ON employees.department_id = departments.department_id
GROUP BY departments.department_id
HAVING employees_num > 3
ORDER BY employees_num DESC;

-- 查询员工名, 部门名, 工种名, 并按部门名降序 (多表连接)
SELECT last_name, department_name, job_title
FROM employees
INNER JOIN departments ON employees.department_id = departments.department_id
INNER JOIN jobs ON employees.job_id = jobs.job_id
ORDER BY department_name DESC;
```

#### 7.1.2 非等值连接

```sql
-- 查询员工的工资级别
SELECT salary, grade_level
FROM employees
INNER JOIN job_grades
ON salary BETWEEN lowest_sal AND highest_sal;
```

#### 7.1.3 自连接

```sql
-- 查询员工的名字及其上级的名字
SELECT e.last_name, m.last_name
FROM employees AS e
INNER JOIN employees AS m
ON e.manager_id = m.employee_id;
```

### 7.2 外连接

如果从表有和主表匹配的值, 则显示匹配的值. 否则就在从表位置显示 null. 一般用于查询一个表中有, 另一个表没有的记录. 左外连接, LRFT JOIN 左边是主表. RIGHT JOIN 右边是主表.

#### 7.2.1 左外连接 / 右外连接

```sql
-- 哪个部门没有员工
SELECT departments.department_id
FROM departments
LEFT OUTER JOIN employees ON departments.department_id = employees.department_id
WHERE employee_id IS NULL;

SELECT departments.department_id
FROM employees
RIGHT OUTER JOIN departments ON departments.department_id = employees.department_id
WHERE employee_id IS NULL;
```

#### 7.2.3 全外连接 (MySQL 不支持)

全外连接 = 内连接的结果 + 表1有但表2没有的 + 表2有单表1没有的

### 7.3 交叉连接

就是笛卡尔乘积. 表一的每一行和表二的每一行分别连接.
表一的 size * 表二的size = 交叉连接结果的size

```sql
SELECT *
FROM employees
CROSS JOIN departments;
```

## 7 子查询

出现在其他语句中的 SELECT 语句

按子查询出现的位置分类:

- SELECT 后面 -> 仅支持标量子查询
- FROM 后面 -> 仅支持表子查询
- WHERE 或 HAVING 后面 -> 支持标量, 列, 行子查询
- EXISTS 后面(相关子查询) -> 支持表子查询

按结果集的行列数分类:

- 标量子查询(结果集为一行一列)
- 列子查询(结果集为多行一列)
- 行子查询(结果集为一行多列)
- 表子查询(结果集为多行多列)

### 7.1 WHERE 或 HAVING 后面

#### 7.1.1 标量子查询

```sql
-- 查询谁的工资比 Abel 高
SELECT *
FROM employees
WHERE salary > (
      SELECT salary
      FROM employees
      WHERE last_name = 'Abel'
);

-- 查询 job_id 与 141 号员工相同, salary 比 143 号员工多的员工姓名, job_id 和 工资.
SELECT last_name, job_id, salary
FROM employees
WHERE job_id = (
      SELECT job_id
      FROM employees
      WHERE employee_id = 141
) AND salary > (
      SELECT salary
      FROM employees
      WHERE employee_id = 143
);

-- 返回公司工资最少的员工的 last_name, job_id 和 salary
SELECT last_name, job_id, salary
FROM employees
WHERE salary = (
      SELECT MIN(salary)
      FROM employees
);

-- 查询最低工资大于50号部门最低工资的部门id和其最低工资
SELECT department_id, MIN(salary)
FROM employees
GROUP BY department_id
HAVING MIN(salary) > (
      SELECT MIN(salary)
      FROM employees
      WHERE department_id = 50
);
```

#### 7.1.2 列子查询 (多行子查询)

多行比较操作符

| 操作符    | 含义                                     |
| --------- | ---------------------------------------- |
| IN/NOT IN | 等于列表中的任意一个                     |
| ANY/SOME  | 和子查询返回的某一个值比较; 类似 OR 操作 |
| ALL       | 和子查询返回的所有值比较; 类似 AND 操作  |

```sql
-- 返回 location_id 是 1400 或 1700 的部门中的所有员工姓名
SELECT last_name
FROM employees
WHERE department_id IN (
      SELECT DISTINCT department_id
      FROM departments
      WHERE location_id IN (1400, 1700)
);

SELECT last_name
FROM employees
WHERE department_id = ANY (
      SELECT DISTINCT department_id
      FROM departments
      WHERE location_id IN (1400, 1700)
);

-- 返回其他部门中比 job_id 为 'IT_PROG' 部门任一工资低的员工的: 工号, 姓名, job_id 以及 salary
SELECT employee_id, last_name, job_id, salary
FROM employees
WHERE salary < ANY (
      SELECT DISTINCT salary
      FROM employees
      WHERE job_id = 'IT_PROG'
) AND job_id <> 'IT_PROG';

-- 返回其他部门中比 job_id 为 'IT_PROG' 部门所有工资低的员工的: 工号, 姓名, job_id 以及 salary
SELECT employee_id, last_name, job_id, salary
FROM employees
WHERE salary < ANY (
      SELECT DISTINCT salary
      FROM employees
      WHERE job_id = 'IT_PROG'
) AND job_id <> 'IT_PROG';
```

#### 7.1.3 行子查询

```sql
-- 查询员工编号最小且工资最高的员工信息
SELECT *
FROM employees
WHERE (employee_id, salary) = (
      SELECT MIN(employee_id), MAX(salary)
      FROM employees
);
```

### 7.2 SELECT 后面

```sql
-- 查询每个部门的员工个数 (各部门的人数是标量子查询)
SELECT departments.*, (
      SELECT COUNT(*)
      FROM employees
      WHERE employees.department_id = departments.department_id
)
FROM departments;
```

### 7.3 FROM 后面

```sql
-- 查询每个部门的平均工资的工资等级. 
-- 这里面要注意FROM 后面的表必须起别名, 不然找不到表.
-- 除此之外, 函数AVG计算结果要其别名才能在括号外面引用
SELECT department_id, grade_level
FROM (
      SELECT department_id, AVG(salary) AS ag
      FROM employees
      GROUP BY department_id
) AS ag_dep 
INNER JOIN job_grades ON ag_dep.ag BETWEEN job_grades.lowest_sal AND job_grades.highest_sal;
```

### 7.4 EXISTS()后面

EXISTS(完成的查询语句) -> 返回0/1告诉我们查询结果表是否存在数据

```sql
-- 查询有员工的部门名
SELECT department_name
FROM departments
WHERE EXISTS(
      SELECT *
      FROM employees
      WHERE departments.department_id = employees.department_id
);
```

## 8 分页查询

LIMIT offset, size
offset: 要现实条目的起始索引 (起始索引和之前不一样, 这地方是从0开始)
size: 要显示的条目数

```sql
-- 查询前五条员工信息 (offset是0的话可以省略)
SELECT *
FROM employees
LIMIT 0, 5;

SELECT *
FROM employees
LIMIT 5;

-- 查询第11条到25条
SELECT * 
FROM employees
LIMIT 10, 15;
```

## 9 联合查询

UNION: 将多条查询语句的结果合并成一个结果
我感觉就是多表的堆叠, 类似 pandas 里面的 concat
UNION会自动去重, 想要显示全部就用 UNION ALL

```sql
-- 查询部门编号 > 90 或游戏哪个包含 a 的员工信息
SELECT *
FROM employees
WHERE email LIKE '%a%' OR department_id > 90;

SELECT *
FROM employees
WHERE email LIKE '%a%'
UNION
SELECT * FROM employees
WHERE department_id > 90;