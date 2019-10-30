---
tags: [rails,缓存,cache]
date: 2018-08-28 12:41:43
---

### 应用场景

有一组数据，在一定的时间内不会发生变化，而用户需要频繁的获取，这组数据的生成方式比较耗时，可能是从数据库中查询得来。若查询条件不会改变，短时间内，该数据就不会发生变化.

### 示例

```ruby
key = "#{condition}#{content}#{params[:collection_state]}"
tmp = Rails.cache.fetch(key,expires_in: 3.minutes) do
  Mortgage.overdue(query)
end
return tmp
```

解释： key 是查询条件，**如果缓存中，key 存在，并且没有过期，那么句直接返回他的值，否则返回空。如果有 block，则将 block 的返回值写入 缓存。**

可以将 rails 中此类缓存理解为 带有时间管理的 Hash
