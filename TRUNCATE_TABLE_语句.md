---
tags: [postgresql]
date: 2018-09-07 09:06:40
---

> 摘选 postgres 修炼之道-唐成

TRUNCATE TABLE 语句的用途是**清空表内容。**不带 WHERE 条件子句的 DELETE 语句也表示清空表的内容。

从执行结果看，两者实现了相同的功能，**但两者实现的原理是不样的。**

TRUNCATE TABLE 是 DDL 语句，即数据定义语句，相当于用重新定义一个新表的方法把原先表的内容直接丢弃了，所以 TRUNCATE TABLE 执行起来很快，而 DELETE 是 DML 语句，可以认为 DELETE 是把数据一条一条地删除，若要删除很多行数据，就会比较慢。如果想把表 student_bak 中的数据清理掉，则可以使用如下命令

```sql
TRUNCATE TABLE student_bak
```

![1543386685.png](https://i.loli.net/2018/11/28/5bfe36e4c0feb.png?filename=1543386685.png)
