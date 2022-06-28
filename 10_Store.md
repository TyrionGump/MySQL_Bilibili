## 存储

- [存储过程](#存储过程)
- [1 存储过程含义](#1-存储过程含义)
- [2 创建存储过程](#2-创建存储过程)

## 1 存储过程

### 1.1 存储过程含义

- 一组预先编译的 SQL 语句的封装
- 减少操作, 提高了 SQL 语句的重用性,
- 减少操作过程中的失误, 提高效率
- 减少网络传输量 (客户端不需要把所有 SQL 语句通过网络发送到服务器)
- 减少 SQL 语句暴露在网上的风险, 也提高了数据查询的安全性

### 1.2 创建存储过程

```sql
-- 无参数 无参数无返回
-- IN 有参数无返回
-- OUT 无参数有返回
-- INOUT 有参数有返回
CREATE PROCEDURE p_name(INT | OUT | INOUT 参数名 参数类型, ...)
[characteristics ...]  -- 表示存储过程中指定的对存储过程的约束条件
BEGIN
    存储过程体
END;
```

### 1.3 无参数的存储过程

```sql
-- 无参数
DELIMITER $  -- 因为存储过程体过程中我们用 ; 会结束执行过程, 但是我们又希望过程体中各个 sql 语句能够分开, 因此先转换分隔符符号为 $

CREATE PROCEDURE select_all_data()
BEGIN
    SELECT * FROM employees;
END $

DELIMITER ;

CALL select_all_data();
```

### 1.4 有 OUT 的存储过程

```sql
DELIMITER $

CREATE PROCEDURE show_minimum_salary(OUT min_salary DOUBLE)
BEGIN
    SELECT MIN(salary) INTO min_salary
    FROM employees;
END $

DELIMITER ;

CALL select_all_data(@min_salary);
SELECT @min_salary;
```

### 1.5 有 IN 的存储过程

```sql
DELIMITER $

CREATE PROCEDURE show_someone_salary(IN emp_name VARCHAR(20))
BEGIN
    SELECT salary
    FROM employees
    WHERE last_name = emp_name;
END $

DELIMITER ;

-- 调用方式一
CALL show_someone_salary('andrew');
-- 调用方式二 (创建变量)
SET @emp_name := 'andrew';
CALL show_some_salary('andrew');
```

### 1.6 带 IN 和 OUT 的存储过程

```sql
DELIMITER $

CREATE PROCEDURE show_someone_salary(IN emp_name VARCHAR(20), OUT emp_salary DECIMAL(10, 2)) -- 存在隐式转换
BEGIN
    SELECT salary INTO emp_salary
    FROM employees
    WHERE last_name = emp_name;
END $

DELIMITER ;

CALL show_someone_salary('andrew', @emp_salary);
SELECT @emp_salary;
```

### 1.7 带 INOUT 的存储过程

```sql
DELIMITER $

CREATE PROCEDURE show_someone_salary(INOUT emp_name VARCHAR(25)) -- 存在隐式转换
BEGIN
    SELECT last_name INTO emp_name
    FROM employees
    WHERE employee_id = (
        SELECT manager_id
        FROM employees
        WHERE last_name = emp_name
    );

END $

DELIMITER ;

CALL show_someone_salary('andrew', @emp_salary);
SELECT @emp_salary;
```

## 2 存储函数

### 2.1 存储函数

```sql
CREATE FUNCTION funtion_name(参数名 参数类型,...)
RETURNS 返回值类型 -- 一定有返回值类型
[characteristics...]  -- 存储函数必须要有这部分而存储过程不需要
BEGIN
    函数体 -- (RETURN SELECT 语句)
END
```

```sql
-- 无参数的
DELIMITER $

CREATE FUNCTION email_by_name()
RETURN VARCHAR(25)
DETERMINISTIC  -- 返回结果是确定的
CONTAINS SQL -- 包含 SQL语句
RESDS SQL DATA -- 要读取 SQL 数据
BEGIN
    RETURN (
        SELECT email 
        FROM employees
        WHERE last_name = 'andrew';
    )
END $

DELIMITER ;

SELECT email_by_name();

-- 有参数的

DELIMITER $

CREATE FUNCTION email_by_id(emp_id INT)
RETURN VARCHAR(25)
DETERMINISTIC  -- 返回结果是确定的
CONTAINS SQL -- 包含 SQL语句
RESDS SQL DATA -- 要读取 SQL 数据
BEGIN
    RETURN (
        SELECT email 
        FROM employees
        WHERE employee_id = emp_id;
    )
END $

DELIMITER ;

SELECT email_by_id(101);
```

## 3 存储过程和函数的查看

```sql
SHOW CREATE PROCEDURE xxxx;
SHOW CREATE FUNCTION xxxx;

SHOW PROCEDURE STATUS LIKE 'show_max_salary';

SELECT * FROM information_schema.Routines
WHERE ROUTINE_NAME='email_by_id' AND ROUTINE_TYPE = 'PROCEDURE';
```

## 4 存储过程和函数的(特性)修改

```sql
ALTER PROCEDURE show_max_salary
SQL SECURITY INVOKER
COMMENT 'what do you want?';
```

## 5 存储过程和函数的删除

```sql
DROP FUNCTION IF EXISTS count_by_id;
```

