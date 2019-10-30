---
tags: [分区表]
date: 2019-03-20 15:19:52
---

postgerSQL 是通过表继承来实现分区表的。表分区就是把逻辑上的一个大表分割成物理上的几个小块，分区可以提供若干好处。

- 删除历史数据更快，如果是按时间分区的，在删除历史数据时，直接删除历史分区就可以了，如果没有分区，通过 DELETE 删除数据时会比较慢，还容易导致 VACUUM 超载；

- 某些类型的查询性能可以得到极大的提升，特别是表中访问率较高的行位于一个单独分区或少数几个分区的情况下。如果在按时间分区的表中，大多数查询发生在时间最近的一个分区或几个分区中，而较早时间分区比较少查询，那么，在建分区表后各个分区表会有各自的索引，使用率较高的分区表的索引就可能完全缓存在内存中，这样效率就会高很多；

- 当查询或更新一个分区的大部分记录时，连续扫描那个分区而不是使用索引离散的访问整个表，可以获得巨大的性能提升；

- 很少用到的历史数据可以使用表空间的技术移动到便宜一些的慢速存储介质上，因为使用分区表可以将不同的分区安置在不同的物理介质上。

### 什么时候该使用分区表？

一般取决于具体应用，不过也有个基本的简单原则，即表的大小超过了数据库服务器的物理内存大小则应该使用

**使用分区表时，一般都让父表为空，数据都存在子表中**

### 建分区表的步骤

1. 创建父表，所有的分区都从它继承。这个表中没有数据，不要在这个表上定义任何检查约束，除非你希望约束所有的分区。同样，在其上定义任何索引或唯一约束也没有任何意义。

2. 创建几个子表，每个都是从主表上继承的。通常，这些表不会增加任何字段。我们把子表称作分区，实际上他们就是普通的 postgreSQL 表

3. 给分区表增加约束，定义每隔分区允许的键值。

4. 对于每个分区，在关键字字段上创建一个索引，也可以创建其它你想创建的索引。严格来说，关键字索引并非是必须的，但是大多情况下他是很有帮助的，如果你希望关键字是唯一的，那么应该总是给每个分区创建一个唯一约束或主键约束。

5. 定义一个规则或者触发器，把对主表的数据插入重定向到合适的分区表。

6. 确保 postgresql.conf 里的配置参数 constaint_exclusion(约束排除) 是打开的。打开后，如果查询中的 WHERE 子句的过滤条件与分区的约束条件匹配，那么这个查询会智能的只查询这个分区，而不会查询其他分区。 在 9.2.4 以后的版本中， 参数 constaint_exclusion 默认就是 partition。 如果设置成 off 则会扫描每张分区子表。

### 在 rails 中的应用

rails 没有提供专有的方法来设置分区表，需要我们编写 SQL 语句手动设置。分区表与触发器的创建自然是写在迁移文件中

下面是 按商标的长度不同，把商标数据存入不同的表中 的例子

### 创建父表

```ruby
class CreateTrademarkWords < ActiveRecord::Migration[5.2]
  def change
    create_table :trademark_words do |t|
      t.string :name
      t.integer :length
      t.string :origin

      t.timestamps
    end
  end
end
```

### 创建子表及触发器

```ruby
class CreatePartitionTableOfTrademarkWords < ActiveRecord::Migration[5.2]
  def up
    # 注意如何在 rails 中编写 SQL
    execute <<~SQL
    CREATE TABLE trademark_words_1 (CHECK (length = 1)) INHERITS (trademark_words);
    CREATE INDEX length_1_index_name ON trademark_words_1 (name);
    CREATE INDEX length_1_index_origin ON trademark_words_1 (origin);

    CREATE TABLE trademark_words_2 (CHECK (length = 2))   INHERITS (trademark_words);
    CREATE INDEX length_2_index_name ON trademark_words_2 (name);
    CREATE INDEX length_2_index_origin ON trademark_words_2 (origin);

    CREATE TABLE trademark_words_3 (CHECK (length = 3))   INHERITS (trademark_words);
    CREATE INDEX length_3_index_name ON trademark_words_3(name);
    CREATE INDEX length_3_index_origin ON trademark_words_3 (origin);

    CREATE TABLE trademark_words_4 (CHECK (length = 4))   INHERITS (trademark_words);
    CREATE INDEX length_4_index_name ON trademark_words_4(name);
    CREATE INDEX length_4_index_origin ON trademark_words_4 (origin);

    CREATE TABLE trademark_words_5 (CHECK (length = 5))   INHERITS (trademark_words);
    CREATE INDEX length_5_index_name ON trademark_words_5(name);
    CREATE INDEX length_5_index_origin ON trademark_words_5 (origin);

    # 触发器
    CREATE OR REPLACE FUNCTION trademark_words_insert_trigger()
    RETURNS TRIGGER AS $$
    BEGIN
        IF ( NEW.length = 1 ) THEN
             INSERT INTO trademark_words_1 VALUES (NEW.*);
        ELSIF ( NEW.length = 2) THEN
             INSERT INTO trademark_words_2 VALUES (NEW.*);
        ELSIF ( NEW.length = 3) THEN
             INSERT INTO trademark_words_3 VALUES (NEW.*);
        ELSIF ( NEW.length = 4) THEN
             INSERT INTO trademark_words_4 VALUES (NEW.*);
        ELSIF ( NEW.length = 5) THEN
             INSERT INTO trademark_words_5 VALUES (NEW.*);
        ELSE
        RAISE EXCEPTION 'Length out of range. Fix the trademark_words_insert_trigger() function!';
        END IF;
        RETURN NULL;
    END;
    $$
    LANGUAGE plpgsql;
    CREATE TRIGGER insert_trademark_words
        BEFORE INSERT ON trademark_words
        FOR EACH ROW EXECUTE PROCEDURE trademark_words_insert_trigger();
    SQL

  end

  # 回滚操作
  def down
    execute <<~SQL
            DROP TABLE trademark_words_1;
            DROP TABLE trademark_words_2;
            DROP TABLE trademark_words_3;
            DROP TABLE trademark_words_4;
            DROP TABLE trademark_words_5;
            DROP TRIGGER insert_trademark_words ON trademark_words;
          SQL

  end
end

```
