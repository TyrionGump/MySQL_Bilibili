# 数据查询语言

[toc]

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
SELECT * FROM employees WHERE job_id IN ('xxx', 'xxx', 'xxx');
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
-- 返回 1
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

### 4.1.3 日期函数

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
|-----|--------------------------|
| %Y  | 4位的年份                    |
| %y  | 2位的年份                    |
| %m  | 月份 (01, 02, ..., 11, 12) |
| %c  | 月份 (1, 2, ..., 11, 12)   |
| %d  | 日 (01, 02 ...)           |
| %H  | 小时 (24小时制)               |
| %h  | 小时 (12小时制)               |
| %i  | 分钟 (00, 01, ..., 59)     |
| %s  | 秒 (00, 01, ..., 59)      |

### 4.1.4 其他函数

- VERSION()
- DATABASE()
- USER()

### 4.1.5 流程控制函数

- IF() 函数

```sql

SELECT  