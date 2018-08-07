---
tags: rails,console
date: 2018-08-07 23:58:40
---

用 app 来调用 routes，比如 app.posts_path, app.topic_path(1)

```ruby
irb > app.topics_path
=> "/topics"
irb > app.get(app.root_path)
......
=> 200
```

用 helper 来调用 Helper 方法，比如:

```ruby
irb > helper.link_to("Ruby China", "http://ruby-china.org")
=> "<a href=\"http://ruby-china.org\">Ruby China</a>"
irb > helper.truncate("Here is Ruby China.", length: 15)
=> "Here is Ruby..."
```

使用 source_location 方法查看方法在那里定义的, 比如:

```ruby
irb >Topic.instance_method(:destroy).source_location
=> ["/Users/jason/.rvm/gems/ruby-1.9.3-p0/gems/mongoid-2.4.8/lib/mongoid/persistence.rb", 30]
irb >Topic.method(:destroy_all).source_location
=> ["/Users/jason/.rvm/gems/ruby-1.9.3-p0/gems/mongoid-2.4.8/lib/mongoid/persistence.rb", 239]
```
