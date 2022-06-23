# 数据处理语言

- [数据处理语言](#数据处理语言)
  - [1 添加数据](#1-添加数据)
  - [2 更新数据](#2-更新数据)
    - [3 删除数据](#3-删除数据)
    - [4 计算列](#4-计算列)
  
## 1 添加数据

```sql
-- 方式一(一条一条地添加数据)
-- 当没有指明添加的字段
INSERT INTO table_name VALUES (x, xxx, xxx, xxx);
-- 当指明添加字段
INSERT INTO table_name(col1, col2, col3) VALUES(xxx, xxx, xxx);
INSERT INTO table_name(col1, col2, col3) VALUES(xxx, xxx); -- 没有指明的字段为 null

-- 方式二(将查询结果插入到表中) -> dest 表的字段长度应该 >= src 表的字段长度
INSERT INTO table_name(col1, col2, col3) 
SELECT xxx FROM xxx;
```

## 2 更新数据

```sql
UPDATE table_name SET col_name = xxx WHERE condition;
UPDATE table_name SEt col1 = xxx, col2=xxx WHERE condition;
```

### 3 删除数据

```sql
DELETE FROM table_name WHERE condition;
```

### 4 计算列
提前设定某些列是由某些列通过计算获得的

```sql
CREATE TABLE xxx(
    a INT,
    b INT,
    c INT GENERATED ALWAYS AS (a + b) VIRTUAL
)

