# 数据定义语言

- [数据定义语言](#数据定义语言)
  - [1 创建和管理数据库](#1-创建和管理数据库)
    - [1.1 创建数据库](#11-创建数据库)
    - [1.2 管理数据库](#12-管理数据库)
    - [1.3 修改数据库](#13-修改数据库)
    - [1.4 删除数据库](#14-删除数据库)
  - [2 MYSQL 中的数据类型](#2-mysql-中的数据类型)
  - [3 创建和管理表](#3-创建和管理表)
    - [3.1 创建表](#31-创建表)
    - [3.2 查看表结构](#32-查看表结构)
    - [3.2 修改表](#32-修改表)
      - [3.2.1 添加一个字段](#321-添加一个字段)
      - [3.2.2 修改一个字段](#322-修改一个字段)
      - [3.2.3 重命名一个字段](#323-重命名一个字段)
      - [3.2.4 删除一个字段](#324-删除一个字段)
      - [3.2.5 重命名表](#325-重命名表)
      - [3.2.6 删除表](#326-删除表)
      - [3.2.7 清空表](#327-清空表)

## 1 创建和管理数据库

### 1.1 创建数据库

```sql
-- 方式一 (默认字符集为 utf8md4)
CREATE DATABASE xxxx;
-- 方式二 (显式指明数据库的字符集)
CREATE DATABASE xxxx CHARACTER SET 'gbk';
-- 显示创建数据库这个操作的内容
SHOW CREATE DATABASE xxxx;
-- 方式三 (创建前判断是否存在该数据库)
CREATE DATABASE IF NOT EXISTS xxxx;
```

### 1.2 管理数据库

```sql
-- 查看当前连接中的数据库
SHOW DATABASES;
-- 切换数据库
USE xxxx;
-- 查看当前数据库中的表
SHOW TABLES;
-- 查看当前数据库的名字
SELECT DATABASE()
-- 查看指定数据库下的表
SELECT TABLES FROM xxxx;
```

### 1.3 修改数据库

```sql
-- 更改数据库字符集
ALTER DATABASE xxxx CHARACTER SET 'utf8';
```

### 1.4 删除数据库

```sql
DROP DATABASE xxxx
DROP DATABASE IF EXISTS xxxx;
```

## 2 MYSQL 中的数据类型
|类型|类型关键字|
|-|-|
|整型|TINYINT / SMALLINT / MEDIUMINT / INT (INTEGER) / BIGINT|
|浮点|FLOAT / DOUBLE|
|定点数类型| DECIMAL|
|位类型|BIT|
|日期|YEAR / TIME / DATE / DATETIME / TIMESTAMP
|文本字符串|CHAR / VARCHAR / TINYTEXT / MEDIUMTEXT / LONGTEXT|
|枚举| ENUM|
|集合|SET|
|二进制字符串| BINARY / VARBINARY / TINYBLOB / BLOB / MEDIUMBLOB / LONGBLOB|
|JSON| JSON Object / JSON Array|
|空间类型|GEOMETRY / POINT / LINESTRING / POLYGON / MULTIPOINT ...|

## 3 创建和管理表

### 3.1 创建表

```sql
-- 方式一
CREATE TABLE IF NOT EXISTS xxxx (
    id INT,
    emp_name VARCHAR(15),
    hire_dare DATE
);

-- 方式二: 基于现有的表 (同时还有原表的数据)
CREATE TABLE xxx AS SELECT xxxx FROM xxxx;
```

### 3.2 查看表结构

```sql
DESC xxxx;

SHOW CREATE TABLE xxxx;
```

### 3.2 修改表

#### 3.2.1 添加一个字段

```sql
ALTER TABLE xxx
ADD salary DOUBLE(10, 2);  -- 默认添加到表最后一个字段的位置

ALTER TABLE xxx
ADD phone_number VARCHAR(20) FIRST;

ALTER TABLE xxx
ADD email VARCHAR(45) AFTER emp_name;
```

#### 3.2.2 修改一个字段

```sql
ALTER TABLE xxx
MODIFY emp_name VARCHAR(35) DEFAULT 'aaa';
```

#### 3.2.3 重命名一个字段

```sql
ALTER TABLE xxx
CHANGE salary monthly_salary DOUBLE(3, 2);

ALTER TABLE xxx
CHANGE email my_email VARCHAR(50);
```

#### 3.2.4 删除一个字段

```sql
ALTER TABLE 
DROP COLLUMN xxx;
```

#### 3.2.5 重命名表

```sql
-- 方式一
RENAME TABLE xxxx
TO xxxx;

-- 方式二
ALTER TABLE xxxx
RENAME TO xxxx;
```

#### 3.2.6 删除表

```sql
DROP TABLE IF EXISTS xxx;
```

#### 3.2.7 清空表 

表数据被清空但是表结构保留

```sql
TRUNCATE TABLE xxxx;
```


