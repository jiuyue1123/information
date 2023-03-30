# DML

Data Manipulation Language(DML)
供用户实现对数据的追加、删除、更新等操作。

## 添加数据

* 给指定的列添加数据

  `INSERT INTO 表名(列名1, 列名2, ...) VALUES(值1, 值2, ...);`

* 给全部列添加数据

  `INSERT INTO 表名 VALUES(值1, 值2, ...);`

* 批量添加数据

  `INSERT INTO 表名(列名1, 列名2, ...) VALUES(值1, 值2, ...),(值1, 值2, ...),...;`

  `INSERT INTO 表名 VALUES(值1, 值2, ...),(值1, 值2, ...),...;`

## 修改数据

`UPDATE 表名 SET 列名1=值1, 列名2=值2, ... [WHERE 条件];`

如果没有加WHERE条件，就会修改全部数据。

## 删除数据

`DELETE FROM 表名 [WHERE 条件];`

如果没有加WHERE条件，就会删除全部数据。