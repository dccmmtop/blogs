---
tags: [rails,分页]
date: 2018-08-28 12:26:47
---

在`controller`经常使用的方式是：

```ruby
User.page(params[:page]).per(2)。
```

但是有时会有比较复杂的查询，只依赖 ORM （对象关系映射）没有办法完成。需要用原生的 sql 语句。简单例子如下：

```ruby
@data = User.find_by_sql("select * from users,topics,likes,comments where topics.user_id = users.id and likes.type = comments.status")
```

此时`@data`是一个数组，该如何对他进行分页呢？

### 解决办法

```ruby
@data = Kaminari.paginate_array(@data, total_count: @data.size).page(params[:page]).per(10)
```

在 view 中

```erb
<div align="center">
  <%= paginate @all_overdue %>
</div>
```

### 思考

以上方式可以完成分页功能，但是当点击每一页时， 都会执行一次所有的查询，而不是 `LIMIT = 10 OFFSET = xxx`,影响效率
有一个解决办法是，对 `@data`加[缓存](https://dccmm.world/topics/rails-%E7%BC%93%E5%AD%98%E4%B9%8B%E4%BD%8E%E5%B1%82%E7%BC%93%E5%AD%98)

第二种办法就是手动分页 参考[这篇文章](http://jameshuynh.com/rails/paginate/find_by_sql/2017/09/30/how-to-paginate-rails-find-by-sql-result/),参考下面的翻译

## 翻译

对 rails 的开发者来说，分页对我们并不陌生，有一个`Kaminari`的分页插件，使用起来非常的爽，但是它有一个严重的问题，就是在`find_by_sql`方法中不起作用，因为`find_by_sql`返回的是一个数组，不是 ActiveRecord::Relation ,在这篇文章中，我们来看一下对于`find_by_sql`如何使用 Kaminari，并且在 View 层使用 Kaminari 提供的 hepler 方法

### 为什么会用到 find_by_sql

`ActiveRecord`, `ActiveRecord::Relation`可以链式的调用，大多情况下我们使用它就可以很方便的完成查询要求，例如下面的代码：

```ruby
# get the 1st page of 10 users who are created 7 days ago
User.where('created_at >= ?', 7.days_ago).page(1).per(10)
```

然而有时我们不能避免使用`find_by_sql`，当查询语句非常复杂时，或者没有对应的接口完成 sql 语句中的一些查询字段——如`UNION`

```sql
-- query all name of quizzes and surveys

SELECT name AS name, 'quizzes' AS entity_type
FROM quizzes
UNION
SELECT title AS name, 'survey' AS  entity_type
FROM surveys
```

对于这个示例来说，我们想从 Rails 得到数据，就得使用 `find_by_sql` 或者 `ActiveRecord::Base.connection.execute`了，下面就看一下`find_by_sql`的使用方式：

```ruby
sql = %(
  SELECT name AS name, 'quizzes' AS entity_type
  FROM quizzes
  UNION
  SELECT title AS name, 'survey' AS  entity_type
  FROM surveys
)

quizzes = Quiz.find_by_sql(sql)
```

`quizes`的值是`Quiz`对象数组，每个对象只有两个属性：`name`,`entity_type`,下面就开始对数组使用分页。有两种办法解决

### 原生的解决办法（效率低下）

一个简单的实现方式是使用 Kaminari 提供的方法：

```ruby
# ...
Kaminari.paginate_array(quizzes).page(1).per(10)
```

如果数据是通过`find_by_sql`方法得到的，我强烈建议开发者不使用这种方式来进行分页，举个简单的例子，如果数据有 1000 条，那么这 1000 条数据都会被加载到内存，然后 `Kaminari.paginate_array` 截取当前页的 10 条，然后删除剩下所有数据。它是非常浪费资源的，因为我们只需要 10 条数据，而这种方式却加载了 1000 条。我们需要想一种办法，让数据库只加载我们当前页需要的 10 条记录。

### 真实的分页

为了在数据库的层面使用分页，我们需要把 sql 语句做如下的包装：

```sql
-- query all name of quizzes and surveys

SELECT * FROM (
  SELECT name AS name, 'quizzes' AS entity_type
  FROM quizzes
  UNION
  SELECT title AS name, 'survey' AS  entity_type
  FROM surveys
) AS paginatable
```

为了查询当前页的记录，我们需要使用两个 SQL 函数，`LIMIT` and `OFFSET`,`LIMIT`函数只返回我们指定数目的记录，它应该等于每页的数量，`OFFSET`可以让我们指定从哪个位置开始获取数据，就像数组中的索引一样，我们可以编写一个方法，获得当前页面第一条数据的索引:

```ruby
def offset(page, per_page)
  (page - 1) * per_page
end
```

然后我们就可以使用`LIMIT` 和 `OFFSET`,如下：

```ruby
def query_report(page: 1, per_page: 10)
  sql = %(
    SELECT * FROM (
      SELECT name AS name, 'quizzes' AS entity_type
      FROM quizzes
      UNION
      SELECT title AS name, 'survey' AS  entity_type
      FROM surveys
    ) AS paginatable
    LIMIT :limit OFFSET :offset
  )

  Quiz.find_by_sql(
    [
      sql,
      {
        limit: per_page,
        offset: offset(page, per_page)
      }
    ]
  )
end
```

就像这样，我们可以仅查询当前页面的数据了，然而，这还没有结束，若是想用 Kaminari 提供的 Helper 方法，方便我们在 View 层使用，还需要花些功夫，下面提供了一种实现方式：

```ruby
# original SQL
def query_report_sql
  @query_report_sql ||=
    %(
      SELECT name AS name, 'quizzes' AS entity_type
      FROM quizzes
      UNION
      SELECT title AS name, 'survey' AS  entity_type
      FROM surveys
    )
end

# paginatable SQL
def query_report_paginate_sql
  @query_report_paginate_sql ||=
    %(
      SELECT *
      FROM (#{query_report_sql}) AS paginatable
      LIMIT :limit OFFSET :offset
    )
end

# count all records SQL
def query_report_total_count_sql
  @query_report_total_count_sql ||=
    %(
      SELECT COUNT(*) AS count
      FROM (#{query_report_sql}) AS paginatable
    )
end

def query_report(page: 1, per_page: 10)
  records =
    Quiz.find_by_sql(
      [
        query_report_paginate_sql,
        {
          limit: per_page,
          offset: offset(page, per_page)
        }
      ]
    )

  records
    .instance_variable_set(:@per_page, per_page)
  records
    .instance_variable_set(:@query_report_total_count_sql,
                           query_report_total_count_sql)

  add_pagination_methods(records)

  records
end

def add_pagination_methods(records)
  records.instance_eval do
    def total_count
      @total_count ||=
        Quiz
        .find_by_sql(@query_report_total_count_sql)
        .first
        .count
    end

    def total_pages
      @total_pages ||= (total_count * 1.0 / @per_page).ceil.to_i
    end
  end
end

private

def offset(page, per_page)
  (page - 1) * per_page
end
```

在前三个方法中，我们编写了需要用到的查询语句，之后我们还给查询结果添加了`total_count` 和 `total_pages`,方法，因为在 Kaminari 提供的 helper 方法中 需要知道总数据和总页数，这样才能正确的生成分页查询的相关 html
