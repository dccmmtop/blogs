---
tags: rails,分页
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
有一个解决办法是，对 `@data`加[缓存](http://dccmm.world/topics/rails-%E7%BC%93%E5%AD%98%E4%B9%8B%E4%BD%8E%E5%B1%82%E7%BC%93%E5%AD%98)

第二种办法就是手动分页 参考[这篇文章](http://jameshuynh.com/rails/paginate/find_by_sql/2017/09/30/how-to-paginate-rails-find-by-sql-result/)
