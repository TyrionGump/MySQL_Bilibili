# 数据类型详解

- [数据类型详解](#数据类型详解)
  - [1 MYSQL的数据类型](#1-mysql的数据类型)
    - [2 整型数据类型](#2-整型数据类型)
      - [2.1 ZEROFILL](#21-zerofill)
      - [2.2 UNSIGNED](#22-unsigned)
    - [3 浮点类型](#3-浮点类型)
    - [4 定点数类型](#4-定点数类型)
    - [5 位类型](#5-位类型)
  - [6 日期时间类型](#6-日期时间类型)
    - [6.1 YEAR](#61-year)
    - [6.2 DATE](#62-date)
    - [6.3 TIME](#63-time)
    - [6.4 DATETIME](#64-datetime)
    - [6.5 对比 DATETIME 和 TIMESTAMP](#65-对比-datetime-和-timestamp)
  - [7 文本字符串类型](#7-文本字符串类型)
    - [7.1 CHAR](#71-char)
    - [7.2 VARCHAR](#72-varchar)
  - [8 TEXT 类型](#8-text-类型)
  - [9 ENUM 类型](#9-enum-类型)
  - [10 SET 类型](#10-set-类型)
  - [11 二进制字符串类型](#11-二进制字符串类型)
    - [11.1 BINARY](#111-binary)
    - [11.2 BLOB 类型](#112-blob-类型)
  - [JSON 类型](#json-类型)
  - [空间类型](#空间类型)

## 1 MYSQL的数据类型

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

### 2 整型数据类型

- TINYINT: 1 Byte
- SMALLINT: 2 Byte
- MEDIUMINT: 3 Byte
- INT (INTEGER): 4 Byte
- BIGINT: 8 Byte

#### 2.1 ZEROFILL

```sql
CREATE TABLE test_int2(
    xxx INT,
    xxx INT(5),
    xxx INT(5) ZEROFILL -- 当数字不满足5位时在左侧填充0
);
```

#### 2.2 UNSIGNED

```sql
CREATE TABLE test_int2(
    xxx INT UNSIGNED -- 只存取正数那么所表示数据范围翻倍
);
```


### 3 浮点类型

- FLOAT: 4 Byte
- DOUBLE: 8 Byte
- FLOAT(M, D): M 为精度, D 为 标度. M = 整数位 + 小数位. D = 小数位
- DOUBLE(M, D)

**实际使用中不要对浮点类型数据使用 “=”, 因为二机制的计算精度确实往往和想要的期望值差一点点.**
**所以需要使用定点数DECIMAL**

### 4 定点数类型

- DECIMAL(M, D), DEC, NUMERIC
- 在一些对精度要求不高的场景下, 比起占用同样自己长度的定点数, 浮点数表达的数值范围可以更大
- 定点数在 MySQL 中以**字符串**的形式存储, 这就决定了它具有一定的精准度.
- 默认为 DECIMAL(10, 0) -> 超过部分以四舍五入进行处理

### 5 位类型

- BIT(M): 表示可以存宽度为 M 的二进制. M <=64

## 6 日期时间类型

|类型|字节|最小值|最大值|
|-|-|-|-|
|YEAR|1|1901|2155|
|TIME|3|-838:59:59|838:59:59|
|DATE|3|1000-01-01|9999-12-03|
|DATETIME|8|1000-01-01 00:00:00|9999-12-31 23:59:59|
|TIMESTAMP|4|1970-01-01 00:00:00 UTC|2038-01-19 03:14:07 UTC|

### 6.1 YEAR

```sql
-- 用字符串最好
INSERT INTO table_name(col)
VALUES ('2021'), (2022);
```

### 6.2 DATE

```sql
INSERT INTO table_name(col)
VALUES ('2020-10-01');
```

### 6.3 TIME

```sql
INSERT INTO table_name(col)
VALUES ('12:35:29'), ('2 12:30:29'); -- 2 代表多少天累加
```

### 6.4 DATETIME

```sql
INSERT INTO table_name(col)
VALUES ('2022-10-02 13:30:13');
```

### 6.5 对比 DATETIME 和 TIMESTAMP

```sql
CREATE TABLE temp_time(
    d1 DATETIME,
    d2 TIMESTAMP
);

INSERT INTO temp_time VALUES ('2022-10-02 12:30:30', '2022-10-02 12:30:30');

SET time_zone = '+9:00';
-- TIMESTAMP 会根据时区变化
SELECT * FROM temp_time;
```

## 7 文本字符串类型

### 7.1 CHAR

- CHAR(M): M 个字符. 没有指明字符长度的话, 默认为 1.
- 如果数据世纪长度比 CHAR 类型声明的长度小, 则会在右侧填充空格以达到指定的长度. 当 MySQL 检索 CHAR 类型的数据时, CHAR 类型的字段会去除尾部的空格.

### 7.2 VARCHAR

- VARCHAR(M) 定义时. 必须指定长度 M 个字符.

|类型|特点|空间上|时间上|适用场景|
|-|-|-|-|-|
|CHAR(M)|固定长度|浪费存储空间|效率高|存储不大, 速度要求高|
|VARCHAR(M)|可变长度|节省存储空间|效率低|非 CHAR 的情况|

## 8 TEXT 类型

- TEXT 不需要设置最大字符长度, 动态长度.
- 由于 TEXT 类型存比较大的文本段, 搜索速度稍慢, 无需要不时用. 同时 TEXT 和 BLOB 类型数据在删除时, 容易造成表空洞, 使得文件碎片较多,所以频繁使用的表不建议包含 TEXT 类型字段, 建议单独用一张表. 

## 9 ENUM 类型

```sql
CREATE TABLE table_name(
    season ENUM('只', '能', '选', '我', '们')
);

-- 也可以使用索引(从 1 开始)
INSERT INTO table_name
VALUES (1), (2);
```

## 10 SET 类型

```sql
CREATE TABLE table_name(
    s SET('A', 'B', 'C')
);

-- 插入重复的SET类型成员时, MySQL 会自动删除重复的成员. 当然不能插入 SET 类型中不存在的值.
INSERT INTO table_name VALUES ('A,B,C,A');
```

## 11 二进制字符串类型

### 11.1 BINARY

- BINARY: BINARY(M) -> 不指明长度, 则默认为1
- VARCHAR: VARCHAR(M) -> 必须指明长度
  
### 11.2 BLOB 类型
存储一个二进制的大对象, 比如图像, 音频和视频.

## JSON 类型

```sql
CREATE TABLE table_name(
  js json
);

INSERT INTO table_name(js)
VALUES ('{"name": "sun", "age": "18"}');

SELECT js -> '$.name' AS user_name, js -> '$.age' AS age
FROM table_name;
```

## 空间类型