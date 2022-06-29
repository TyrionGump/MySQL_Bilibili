# 控制流程


## 1 IF

```sql
DELIMITER $

CREATE PROCEDURE test_if()
BEGIN
    DECLARE stu_name VARCHAR(15);
    IF stu_name IS NULL
    THEN SELECT 'stu_name is null';
    END IF
END $

DELIMITER ;

--

DELIMITER $

CREATE PROCEDURE test_if()
BEGIN
    DECLARE email VARCHAR(15);
    IF stu_name IS NULL
        THEN SELECT 'email is null';
    ELSE SELECT 'email is not null';
    END IF;
END $

DELIMITER ;

--

DELIMITER $

CREATE PROCEDURE test_if()
BEGIN
    DECLARE email VARCHAR(15);
    IF stu_name IS NULL
        THEN SELECT 'email is null';
    ELSE SELECT 'email is not null';
    END IF;
END $

DELIMITER ;

--

DELIMITER $

CREATE PROCEDURE test_if()
BEGIN
    DECLARE age INT DEFAULT 20;
    IF age > 40
        THEN SELECT 'old';
    ELSEIF  age > 18 
        THEN SELECT 'young';
    ELSEIF age > 8
        THEN SELECT 'naive';
    ELSE
        SELECT 'baby';
    END IF;
END $

DELIMITER ;
```

## 2 CASE

```sql
DELIMITER $

CREATE PROCEDURE test_if()
BEGIN
    DECLARE var INT DEFAULT 2;
    CASE var
        WHEN 1 THEN SELECT 'var = 1';
        WHEN 2 THEN SELECT 'var = 2';
        ELSE SELECT 'other value';
    END CASE;
END $

DELIMITER ;

--

DELIMITER $

CREATE PROCEDURE test_if()
BEGIN
    DECLARE var INT DEFAULT 10;
    CASE 
        WHEN var >= 100 THEN SELECT '三位数';
        WHEN var >= 10 THEN SELECT '两位数';
        ELSE SELECT 'other value';
    END CASE;
END $

DELIMITER ;
```

## 3 LOOP

三种循环都可以省略 label 标签, 但如果循环中添加了循环控制语句 (LEAVE 或 ITERATE) 则必须添加名称

```sql
DELIMITER $

CREATE PROCEDURE test_if()
BEGIN
    DECLARE num INT DEFAULT 1;
    
    loop_label:LOOP
        SET num = num + 1;
        IF num >= 100 THEN LEAVE loop_label;
    END LOOP;
END $

DELIMITER ;
```

## 4 WHILE 

```sql
DELIMITER $

CREATE PROCEDURE test_if()
BEGIN
    DECLARE num INT DEFAULT 1;
    
    WHILE num <= 100 DO
        SET num := num + 1;
    END WHILE;
END $

DELIMITER ;
```

## 5 REPEAT

就是 do-while 结构

```sql
DELIMITER $

CREATE PROCEDURE test_if()
BEGIN
    DECLARE num INT DEFAULT 1;
    
    repeate_label:REPEAT 
        SET num := num + 1;
        UNTIL num >= 10 -- 这个地方不要加分号
    END REPEAT repeate_label;

END $

DELIMITER ;
```

## 6 LEAVE

LEAVE 后面必须跟标签 label

```sql
DELIMITER $

CREATE PROCEDURE leave_begin(IN num INT)
begin_label:BEGIN
    IF num <= 0 
        THEN LEAVE begin_label;
    ELSEIF num = 1
        THEN SELECT AVG(salary) FROM employees;
    ELSE
        SELECT MAX(salary) FROM employees;
    END IF;

END $

DELIMITER ;
```

## 7 ITERATE

ITERATE 就是 continue

## 游标