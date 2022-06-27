# 视图


## 1 视图的创建

```sql
CREATE VIEW view_name
AS
SELECT * FROM table_name;
```

## 2 查看视图的对象

```sql
-- 和table一起显示
SHOW TABLES

-- 查看视图的结构
DESC view_name;

-- 查看视图信息
SHOW TABLE STATUS LIKE view_name;

-- 查看视图的详细定义信息
SHOW CREATE VIEW view_name;
```

## 3 更新视图的数据

以下几种情况将使得视图不支持 INSERT 和 DELETE 操作:
- 定义视图的时候指定了“ALGORITHM=TEMPTABLE”
- 视图中不包含基表中所有被定义为非空又未指定为默认值的列
- 定义视图的SELECT语句中使用了JOIN字段
- 定义视图的SELECT语句中使用了数学表达式或子查询
- 使用了 DISTINCT, 聚合函数, GROUP BY, HAVING, UNION

```sql
-- 更新视图中的数据, 会导致基表中数据的修改. 反之亦然. 当然不能更新基表中不存在的数据, 比如 view 中包含聚合函数的结果
UPDATE view_name
SET salary = 2000
WHERE employee_id = 101;
```

## 4 修改视图

```sql
CREATE OR REPLACE VIEW view_name(col1, col2, col3)
AS
SELECT * FROM table_name;

-- 或者
ALTER VIEW view_name
AS
SELECT * FROM table_name;
```

## 5 删除视图

```sql
DROP VIEW IF EXISTS view_name;
```
