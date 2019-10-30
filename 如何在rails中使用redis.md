---
tags: [rails,redis]
date: 2018-10-29 17:18:24
---

随着用户数量的增长，我们的程序将要面临着大量数据的请求，如果不处理好这些请求，我们的 application 将会变的很慢，甚至崩溃。本文使用 redis(内存数据存储)这个工具来解决部分问题。

首先我们可以使用 `brew`,`apt`,`docker`来安装 redis，当然了，我们也需要 rails。我们来构建一个在线的售票管理系统，下面是基本的数据结构。

```ruby
class User < ApplicationRecord
  has_many :tickets
end
class Event < ApplicationRecord
  has_many :tickets
end
class Ticket < ApplicationRecord
  belongs_to :user
  belongs_to :event
end
```

### 作为缓存使用

该程序的第一个要求是展现卖出票的数量以及利润。我们创造下面的方法。

```ruby
class Event < ApplicationRecord
  def tickets_count
    tickets.count
  end
  def tickets_sum
    tickets.sum(:amount)
  end
end
```

这段代码将会使用 SQL 来查询数据，如果数据量非常大，那么这个查询将会很慢很慢，为了更快的或者查询结果，我们可以暂时把这段查询的结果缓存起来。首先在我们的 rails application 中需要一个可以使用的缓存，向`Gemfile`文件添加`gem redis-rails`，然后在`config/environments/development.rb`，添加如下配置：

```ruby
config.cache_store = :redis_store, {
  expires_in: 1.hour,
  namespace: 'cache',
  redis: { host: 'localhost', port: 6379, db: 0 },
  }
```

指定缓存命名空间的`cache`是可选的，这段代码为缓存设置了 1 个小时的时效(time-to-live  TTL)，当时效到期时,将清除过时的数据，现在我们将方法包裹在缓存的块中:

```ruby
class Event < ApplicationRecord
  def tickets_count
    Rails.cache.fetch([cache_key, __method__], expires_in: 30.minutes) do
      tickets.count
    end
  end
  def tickets_sum
    Rails.cache.fetch([cache_key, __method__]) do
      tickets.sum(:amount)
    end
  end
end
```

**Rails.cache.fetch** 将会检查 Redis 中是否存在指定的 key，如果 key 存在，将会返回缓存中的数据，而不会执行块中的代码，如果不存在，就会执行块中的代码，并将执行结果存在 redis 中，cache_key 是 Rails 提供的一种方法，它将组合模型名称，主键和上次更新的时间戳来创建唯一的 Redis 密钥，也可以添加**method**，它将使用特定方法的名称来进一步统一密钥，我们可以选择在某些方法上指定不同的时效。 Redis 中的数据将如下所示。
```json
{"db":0,"key":"cache:events/1-20180322035927682000000/tickets_count:","ttl":1415,
"type":"string","value":"9",...},

{"db":0,"key":"cache:events/1-20180322035927682000000/tickets_sum:","ttl":3415,
"type":"string","value":"127",...},

{"db":0,"key":"cache:events/2-20180322045827173000000/tickets_count:","ttl":1423,
"type":"string","value":"16",...},

{"db":0,"key":"cache:events/2-20180322045827173000000/tickets_sum:","ttl":3423,
"type":"string","value":"211",...}
```

#### 缓存失效

如果我们缓存这些数据后，立即售出一张票怎么办？系统仍然会返回缓存中的数据（已过时）。刚刚说key的生成方式包含上次更新的时间戳，如果该event发生变化（主要是updated_at），key就会变化，但是新卖出一张票，只是代表这event多了一个ticket,event本身的属性不会发生变化 ，此时就需要`:touch`这个方法了，touch则能实现当子表的记录更新或增加时，父表的记录更新。如下

```ruby
class Ticket < ApplicationRecord
  belongs_to :event, touch: true
end
```
缓存的模式是：一旦我们创建了秘钥与内容的组合，就不会再去改变他，当数据改变时，我们会继续创建新的组合（秘钥-内容），
之前没用的缓存仍然在redis中，直到过了时效，再自动清除。这样做明显浪费了内存，但是简化了代码，我们不用手动去清除和生成缓存。


我们要合理的设置TTL，如果TTL过长，系统中就会生成很多没用的缓存，如果TTL太短，即使数据没有任何变化，也会生成缓存（到期了）。[这里](http://dmitrypol.github.io/redis/2017/05/25/rails-cache-variable-ttl.html)有些关于设置TTL的建议

**注意：** 缓存不应该是终极解决方案。我们应该寻找编写有效代码和优化数据库索引的方法。但有时候缓存仍然是必要的，可以快速解决为更复杂的问题

### Redis作为队列使用

TODO: ...

