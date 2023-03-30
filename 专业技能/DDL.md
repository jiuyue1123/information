# DDL

Data Definition Language(DDL)
DDL使我们有能力创建或删除表格。也可以定义索引（键），规定表之间的链接，以及施加表间的约束。

## 操作数据库

### 查询

`SHOW DATABASE;`

### 创建数据库

`CREATE DATABASE 数据库名称;`

`CREATE DATABASE IF NOT EXISTS 数据库名称;`

### 删除

`DROP DATABASE 数据库名称;`

`DROP DATABASE IF NOT EXISTS 数据库名称;`

### 使用数据库

* 查看当前使用的数据库

  `SELECT DATABASE();`

* 使用数据库

  `USE 数据库名称;`

## 操作表

### 查询表

* 查询当前数据库下所有的表

  `SHOW TABLES;`

* 查询表结构

  `DESC 表名称;`

### 创建表

```mysql
CRETAE TABLE 表名 (
    字段名1	数据类型1,
    字段名2	数据类型2,
    字段名3	数据类型3,
    ...
    字段名n	数据类型n
);
```

#### SQL通用数据类型

| 数据类型                           | 描述                                                         |
| :--------------------------------- | :----------------------------------------------------------- |
| CHARACTER(n)                       | 字符/字符串。固定长度 n。                                    |
| VARCHAR(n) 或 CHARACTER VARYING(n) | 字符/字符串。可变长度。最大长度 n。                          |
| BINARY(n)                          | 二进制串。固定长度 n。                                       |
| BOOLEAN                            | 存储 TRUE 或 FALSE 值                                        |
| VARBINARY(n) 或 BINARY VARYING(n)  | 二进制串。可变长度。最大长度 n。                             |
| INTEGER(p)                         | 整数值（没有小数点）。精度 p。                               |
| SMALLINT                           | 整数值（没有小数点）。精度 5。                               |
| INTEGER                            | 整数值（没有小数点）。精度 10。                              |
| BIGINT                             | 整数值（没有小数点）。精度 19。                              |
| DECIMAL(p,s)                       | 精确数值，精度 p，小数点后位数 s。例如：decimal(5,2) 是一个小数点前有 3 位数，小数点后有 2 位数的数字。 |
| NUMERIC(p,s)                       | 精确数值，精度 p，小数点后位数 s。（与 DECIMAL 相同）        |
| FLOAT(p)                           | 近似数值，尾数精度 p。一个采用以 10 为基数的指数计数法的浮点数。该类型的 size 参数由一个指定最小精度的单一数字组成。 |
| REAL                               | 近似数值，尾数精度 7。                                       |
| FLOAT                              | 近似数值，尾数精度 16。                                      |
| DOUBLE PRECISION                   | 近似数值，尾数精度 16。                                      |
| DATE                               | 存储年、月、日的值。                                         |
| TIME                               | 存储小时、分、秒的值。                                       |
| TIMESTAMP                          | 存储年、月、日、小时、分、秒的值。                           |
| INTERVAL                           | 由一些整数字段组成，代表一段时间，取决于区间的类型。         |
| ARRAY                              | 元素的固定长度的有序集合                                     |
| MULTISET                           | 元素的可变长度的无序集合                                     |
| XML                                | 存储 XML 数据                                                |

### 删除表

`DROP TABLE 表名;`

`DROP TABLE IF EXISTS 表名;`

### 修改表

* 修改表名

  `ALTER TABLE 表名 REBANE TO 新的表名;`

* 添加一列

  `ALTER TABLE 表名 ADD 列名 数据类型;`

* 修改数据类型

  `ALTER TABLE 表名 MODIFY 列名 新数据类型;`

* 修改列名和数据类型

  `ALTER TABLE 表名 CHANGE 列名 新列名 新数据类型; `

* 删除列

  `ALTER TABLE 表名 DROP 列名;`