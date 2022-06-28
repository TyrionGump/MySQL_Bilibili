# 变量


## 1 系统变量

- global 全局系统变量
- seesion 会话系统变量
 
### 1.1 查看系统变量

```sql
-- 全局变量
SHOW GLOBAL VARIABLES;
SHOW GLOBAL VARIABLES LIKE 'xxxxx';

-- 会话变量
SHOW SESSION VARIABLES;
SHOW SESSION VARIABLES LIKE 'xxxxx';
SHOW VARIABLES;

-- 指定系统变量
SELECT @@global.xxx;
SELECT @@session.xxx;
SELECT xxx;  -- 若未指明哪个类型, 则先从session找, 再从global中找
```

### 1.2 修改系统变量

- 方式一: 在 MySQL 的配置文件中修改
- 方式二: 在 MySQL 服务运行过程中使用 SET 修改

```sql
-- 只针对当前数据库实例有效, 一旦数据库服务重启就失效了
SET @@global.max_connections = 100;
SET GLOBAL max_connections = 200;

-- 只针对当前会话有效, 一旦断开重连连接就失效了
SET @@session.character_set_client = 'gbk';
SET SESSION character_set_client = 'uft8';
```

## 2 用户变量

- 用户会话变量: 作用域和会话变量一样, 只对当前连接会话有效
- 局部变量: 只在 BEGIN 和 END 中有效 (存储过程和存储函数)

### 2.1 会话用户变量

```sql
-- 方式一
SET @m1 = 1;
-- 方式二
SET @m2 := 2;
SET @sum := @m1 + @m2;
SELECT @sum;

SELECT @count := COUNT(*) FROM employees;
SELECT AVG(salary) INTO @abg_sal FROM employees;
```

### 2.2 局部变量

```sql
DELIMITER $
CREATE PROCEDURE test_var()
BEGIN
    DECLARE a INT DEFAULT 0;
    DECLARE b INT; -- DECLARE a, b INT DEFAULT 0;
    DECLARE emp_name VARCHAR(25);

    -- 赋值
    SET a = 1;
    SET b := 2;

    SELECT last_name INTO emp_name FROM employees WHERE employee_id = 101;

    SELECT a, b, emp_name;
END $

DELIMITER ;

CALL test_var();
```



