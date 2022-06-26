# 约束

- [约束](#约束)
  - [1 查看表中的约束](#1-查看表中的约束)
  - [2 非空约束](#2-非空约束)

## 1 查看表中的约束

```sql
SELECT * FROM table_name.table_contraints
WHERE table_name = 'xxxxxx';
```

## 2 非空约束

**0 和 '' 不属于NULL**

```sql
CREATE TABLE table_name(
    id INT NOT NULL,
    last_name VARCHAR(15) NOT NULL,
    email VARCHAR(25),
    salary DECIMAL(10, 2)
);

-- 添加非空约束
ALTER TABLE table_name
MODIFY email VARCHAR(25) NOT NULL;
```

## 3 唯一性约束
 
```sql
CREATE TABLE table_name(
    id INT UNIQUE,  -- 列级约束
    last_name VARCHAR(15) NOT NULL,
    email VARCHAR(25),
    salary DECIMAL(10, 2),
-- 表级约束
CONSTRAINT contraint_name UNIQUE(email)
);

-- 方式一: 添加唯一性约束
ALTER TABLE table_name
ADD CONSTRAINT constraint_name UNIQUE(salary);

-- 方式二: 添加唯一性约束
ALTER TABLE table_name
MODIFY email VARCHAR(25) UNIQUE;

-- 复合的唯一性约束
CREATE TABLE table_name(
    id INT,
    `name` VARCHAR(15),
    password VARCHAR(25),
    CONSTRAINT contraint_name UNIQUE(`name`, `password`)
);

-- 删除唯一性约束
-- 添加唯一索引约束的列上会自动创建唯一索引(在table里的索引可以看到)
-- 要删除唯一约束只能通过删除唯一索引
ALTER TABLE table_name
DROP INDEX index_name; -- 唯一约束的索引名字和唯一约束的名字时一样的
```

## 4 PRIMARY KEY 约束

相当于唯一约束和非空约束的组合

```sql
-- 列级约束
CREATE TABLE table_name(
    id INT PRIMARY KEY,
    last_name VARCHAR(15),
    salary DECIMAL(10, 2),
    email VARCHAR(25)
);

-- 表级约束
CREATE TABLE table_name(
    id INT,
    last_name VARCHAR(15),
    salary DECIMAL(10, 2),
    email VARCHAR(25),
    PRIMARY KEY (id) -- PK 不需要给约束命名 (CONTRAINT 关键字是方便给约束起名)
);

-- 修改主键约束
ALTER TABLE table_name
ADD PRIMARY KEY (salary);

-- 删除主键约束
ALTER TABLE table_name
DROP PRIMARY KEY;
```

## 5 AUTO_INCREMENT

**一个表最多只能有一个字增长列**

```sql
CREATE TABLE table_name(
    id INT PRIMARY KEY AUTO_INCREMENT,
    last_name VARCHAR(15),
);

ALTER TABLE table_name
MODIFY id INT AUTO_INCREMENT;
```

## 6 FOREIGN KEY 约束

```sql

-- 先创建主表
CREATE TABLE dept1(
    dept_id INT PRIMARY KEY,
    dept_name VARCHAR(15)
);

-- 再创建从表
CREATE TABLE emp1(
    emp_id INT PRIMARY KEY AUTO_INCREMENT,
    demp_name VARCHAR(15),
    department_id INT,

    -- 表级约束
    CONSTRAINT fk_emp1_dept_id FOREIGN KEY (department_id) REFERENCES dept1(dept_id)
);
```

### 6.1 FOREIGN KEY 约束等级

- CASCADE: 在父表上 update / delete 记录时, 同步 update / delete 字表的匹配记录
- SET NULL: 在父表上 update / delete 记录时, 将子表上匹配记录的列设为 NULL
- NO ACTION: 如果子表中有匹配的记录, 则不允许对父表对应候键进行 update / delete 操作
- RESTRICT: 同 NO ACTION, 都是立即检查外键约束
- SET DEFAULT: 父表有变更时, 子表将外键列设置为一个默认的值, 但 InnoDB 不能识别

```sql
CREATE TABLE dept(
    did INT PRIMARY KEY,
    dname VARCHAR(5)
);

CRESTE TABLE emp(
    eid INT PRIMARY KEY,
    ename VARCHAR(5),
    deptid INT,
    FOREIGN KEY (deptid) REFERENCES dept(did) NO UPDATE CASCADE ON DELETE SET NULL -- 主表更改时子表相应更改, 主表删除时子表相应设置为 NULL
);

-- 最好采用 NO UPDATE CASCADE ON DELETE RESRRICT
```

## 7 CHECK 约束

```sql
CREATE TABLE test10(
    id INT,
    last_name VARCHAR(15),
    salary DEMICAL(10, 2) CHECK (salary > 2000)
);
```

## 8 DEFAULT 约束

```sql
CREATE TABLE test10(
    id INT,
    last_name VARCHAR(15),
    salary DEMICAL(10, 2) DEFAULT 2000
);

ALTER TABLE test10
MODIFY salary DECIMAL(8, 2) DEFAULT 2500;
```
