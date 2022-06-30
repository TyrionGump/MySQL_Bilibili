# 触发器

- [触发器](#触发器)
  - [1 触发器的创建](#1-触发器的创建)
  - [2 查看触发器](#2-查看触发器)
  - [3 删除触发器](#3-删除触发器)

## 1 触发器的创建

创建针对某张表的某一个操作 (UPDATE / INSERT / DELETE)的触发器. 当该表执行该操作时, 其他表需要同时执行触发器中定义的一些操作.

```sql
-- Before Insert
DELIMITER $

CREATE TRIGGER trigger_name_1
BEFORE INSERT ON table_name_1
FOR EACH ROW
BEGIN
    INSERT INTO table_name_2(col_1)
VALUES('?', '?',  '?');
END $

DELIMITER ;

-- After Insert

DELIMITER $

CREATE TRIGGER trigger_name_1
AFTER INSERT ON table_name_1
FOR EACH ROW
BEGIN
    INSERT INTO table_name_2(col_1)
VALUES('?', '?', '?');
END $

DELIMITER ;

-- 当 employee 工资比 manager 工资高时报错
DELIMITER $

CREATE TRIGGER salary_check_trigger
AFTER INSERT ON employees
FOR EACH ROW
BEGIN
    DECLARE manager_salary DOUBLE;
    SELECT salary INRO manager_salry FROM employees WHERE employee_id = NEW.manager_id; -- NEW 表示新添加的那一条数据. 反之, OLD 代表删除的那一条老数据
    IF NEW.salary > manager_salary
        THEN SIGNAL SQLSTATE 'HY000' SET MESSAGE_TEXT = '薪资高于领导薪资';
    END IF;
END $

DELIMITER ;
```


## 2 查看触发器

```sql
SHOW TRIGGERS;
SHOW CREATE TRIGGER trigger_name;
SELECT * FROM information_schema.TRIGGERS;
```

## 3 删除触发器

```sql
DROP TRIGGER IF EXISTS trigger_name;

