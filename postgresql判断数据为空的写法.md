---
tags: [rails,postgersql]
date: 2018-08-27 14:26:46
---

有查询语句：

```sql
select * from topics where tag = null
```

**语法错误：**
为空的判断应该用 `is` -> `select * from topics where tag is null`

不为空：`select * from topics where is not null`

```sql
select * from topics where tag is 'rails'
```

**语法错误：**

值等于的判断应该用 `=` -> `select * from topics where tag = 'rails'`

不为空：`select * from topics where != null`
