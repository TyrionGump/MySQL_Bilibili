# 数据控制语言
 
- [数据控制语言](#数据控制语言)
  - [1 COMMIT 和 ROLLBACK](#1-commit-和-rollback)

## 1 COMMIT 和 ROLLBACK

- COMMIT: 提交数据. 一旦执行 COMMIT, 则数据就被永久地保存在 database, 意味着数据不可以回滚.
- ROLLBACK: 回滚数据. 回滚到最近的一次 COMMIT 之后.
- TRUNCATE TABLE -> 数据一旦被清除, 数据不可以回滚.
- DELETE FROM -> 数据一旦被清除, 数据可以回滚 (**提前 SET autocommit = False**), 也可以不回滚.

