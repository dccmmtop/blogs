---
tags: [模糊查询,postgesql,pg_trgm]
date: 2019-03-24 10:05:23
---

本文记录中文模糊查询优化的方向是 **使模式匹配使用索引**

有一张 2 千万多的 user 表，其中需要按照 users.chinese_name 字段进行模糊查找。

### 启用 pg_trgm 扩展

pg_trgm 模块提供函数和操作符测定字母，数字，文本基于三元模型匹配的相似性， 还有支持快速搜索相似字符串的索引操作符类。

这里提到了一个三元模型，其实很简单。打个比方 foo 的三元模型的集合为{" f"," fo","foo","oo "}, foo|bar 的三元模型的集合为{" f"," fo","foo","oo "," b"," ba","bar","ar "}。也就是说将字符串拆解成三个字符一组，每个字符串被认为有两个空格前缀和一个空格后缀。

Postgres 使用 trigram 将字符串分解成更小的单元便于有效地索引它们。pg_trgm 模块支持 GIST 或 GIN 索引，从 9.1 开始，这些索引支持 LIKE/ILIKE 查询。

要使用 pg_trgm 模块，首先要启用该扩展，然后使用 gin_trgm_ops 创建索引

```sql
CREATE EXTENSION pg_trgm;
```

### 创建索引

在字段上创建 GIN 类型的索引可以处理包含多个键的值，如数组等. 与 GIST 类似， GIN支持用户定义的索引策略，可以通过定义GIN索引的特定操作符类型实现不同的功能。 PostgreSQL的标准中发布了用于一维数组的GIN操作符类， 比如它支持 包含操作符 '@>'、被包含操作符 '<@'、相等操作符 '='、重叠操作符 '&&',等等。

但是这种索引对中文不起作用，需要把中文转换成字节（ASCII码），然后使用函数索引

```sql
create or replace function textsend_i (text) returns bytea as
$$

  select textsend($1);

$$
language sql strict immutable;
CREATE INDEX trgm_idx_users_chinese_name ON users USING GIN(text(textsend_i(chinese_name)) gin_trgm_ops);
```

### 查询语句

```sql
SELECT chinese_name FROM users WHERE text(textsend_i(chinese_name)) ~ ltrim(text(textsend_i('深圳')), '\x');
```
### 再优化
添加 GIN 索引后，查询性能提升很多。如上所说，GIN 不支持中文，在查询的时候，先把 chinese_name 字段转化为 bytea，然后进行匹配。这里也耽误了不少时间，我们可以在users 表上在添加一个 chinese_name_bytea 字段，存储 chinese_name 的字节形式，然后直接在该字段上进行创建 GIN 索引。也算是一种空间换取时间的方式。

```sql
ALTER TABLE users;
ADD COLUMN chinese_name_bytea VARCHAR;
UPDATE users SET chinese_name_bytes = textsend(chinese_name);
CREATE INDEX trgm_idx_users_chinese_name_bytea ON users USING GIN(chinese_name_bytea gin_trgm_ops);
```
查询时：

```sql
SELECT chinese_name FROM users WHERE chinese_name_bytea ~ ltrim(text(textsend_i('深圳')), '\x');
```
