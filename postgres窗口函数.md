---
tags: [postgres,窗口函数]
date: 2019-03-05 09:29:57
---

我们来看一个实际的问题： 有一张人员薪水表，这张表包含 人员编号，人员所在部门，薪水。现在需要选出每个部门薪水排名前10的员工信息。总结一下就是求每个分组的前N名信息。

有一个思路是 按照部门进行分组，在组内按照薪水倒序，然后取出每组的前10条。

### ROW_NUMBER

postgres 提供一个计算行号的函数 ROW_NUMBER(), 这个函数可以计算出当前行在分组中的位置,如果在分组内按照某个字段排序，那么 ROW_NUMBER() 计算的行号就代表着这个规则中的排名。需要特别注意的是，ROW_NUMBER() 必须使用窗口函数的语法才能够正常使用，也就是这个函数后面必须接 OVER() 子句。

postgres 中内建了一些窗口函数，除了这些函数外，任何内建的或用户定义的普通聚集函数（但有序集或假想集聚集除外）都可以作为窗口函数。仅当调用跟着OVER子句时，聚集函数才会作为窗口函数；否则它们作为常规的聚集。

**内建窗口函数**

| 函数                                                         | 返回类型               | 描述                                                         |
| ------------------------------------------------------------ | ---------------------- | ------------------------------------------------------------ |
| `row_number()`                                               | `bigint`               | 当前行在其分区中的行号，从1计                                |
| `rank()`                                                     | `bigint`               | 带间隙的当前行排名； 与该行的第一个同等行的`row_number`相同  |
| `dense_rank()`                                               | `bigint`               | 不带间隙的当前行排名； 这个函数计数同等组                    |
| `percent_rank()`                                             | `double precision`     | 当前行的相对排名： (`rank`- 1) / (总行数 - 1)                |
| `cume_dist()`                                                | `double precision`     | 当前行的相对排名： (当前行前面的行数 或 与当前行同等的行的行数)/(总行数) |
| `ntile(*num_buckets* integer)`                               | `integer`              | 从1到参数值的整数范围，尽可能等分分区                        |
| `lag(*value* anyelement [, *offset* integer [, *default* anyelement ]])` | `和*value*的类型相同`  | 返回`*value*`， 它在分区内当前行的之前`*offset*`个位置的行上计算；如果没有这样的行，返回`*default*`替代。 (作为`*value*`必须是相同类型)。 `*offset*`和`*default*`都是根据当前行计算的结果。如果忽略它们，则`*offset*`默认是1，`*default*`默认是空值 |
| `lead(*value* anyelement [, *offset* integer [, *default* anyelement ]])` | `和*value*类型相同`    | 返回`*value*`，它在分区内当前行的之后`*offset*`个位置的行上计算；如果没有这样的行，返回`*default*`替代。(作为`*value*`必须是相同类型)。`*offset*`和`*default*`都是根据当前行计算的结果。如果忽略它们，则`*offset*`默认是1，`*default*`默认是空值 |
| `first_value(*value* any)`                                   | `same type as *value*` | 返回在窗口帧中第一行上计算的`*value*`                        |
| `last_value(*value* any)`                                    | `和*value*类型相同`    | 返回在窗口帧中最后一行上计算的`*value*`                      |
| `nth_value(*value* any, *nth* integer)`                      | `和*value*类型相同`    | 返回在窗口帧中第`*nth*`行（行从1计数）上计算的`*value*`；没有这样的行则返回空值 |

### OVER()

一个*窗口函数*在一系列与当前行有某种关联的表行上执行一种计算。这与一个聚集函数所完成的计算有可比之处。但是与通常的聚集函数不同的是，使用窗口函数并不会导致行被分组成为一个单独的输出行--行保留它们独立的标识。在这些现象背后，窗口函数可以访问的不仅仅是查询结果的当前行。


下面使用窗口函数 ROW_NUMBER() 来获取每个部门薪水排名前十的用户信息

```	sql
SELECT * FROM
(SELECT depname, empno, salary, ROW_NUMBER() OVER (PARTITION BY depname ORDER BY salary DESC) as row_index )
WHERE(row_index <= 10) FROM empsalary
```

使用 Rails 可以这样写

```ruby
user_group = User.select("id,depname, empno, salary, ROW_NUMBER() OVER (PARTITION BY depname ORDER BY salary DESC) as row_index").to_sql
User.select("agg.*").joins("RIGHT JOIN (#{user_group}) as agg ON agg.id = user.id").where("agg.row_index <= 10")
```

其中 `ROW_NUMBER() OVER (PARTITION BY depname ORDER BY salary DESC) as row_index` 的 `PARTITION BY depname` 意思是按照 `depname`  进行分区， `ORDER BY salary DESC ` 在当前分区内按照薪水倒序， 然后 `ROW_NUMBER()` 计算出当前行在所在分区的行号然后作为 row_index 列显示。**值得注意的是最后的输出结果是前十名，但并不是按照薪水的高低排序的**如果想要有序的数据还需要在最后使用 `ORDER  BY salary DESC` 进行排序

更多有关窗口函数的介绍请看手册 http://www.postgres.cn/docs/9.6/tutorial-window.html http://www.postgres.cn/docs/9.6/functions-window.html
