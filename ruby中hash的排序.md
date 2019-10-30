---
tags: [hash,sort]
date: 2019-01-15 11:45:27
---
> 翻译自 https://medium.com/@florenceliang/some-notes-about-using-hash-sort-by-in-ruby-f4b3a700fc33

ruby中的sort和sort_by 是两个非常有用的工具，我们可以按照自己的想法排序。比如我们可以按照名字字母表或者名字的长短进行排序。但是ruby文档中却没有过多的说明，因此本文主要说明一下关于Hash的sort和sort_by的用法

#### 返回数组

首先要知道的是 Hash 对象调用 sort 或者 sort_by 后，会返回一个数组，而不是Hash。如果想得到 Hash，需要使用 `to_h` 方法

假如有下面的 Hash

```ruby
hash = {a:1, b:2, c:4, d:3, e:2}
```

调用sort方法，将会返回一个嵌套数组

```ruby
hash.sort # 没有指定按什么值进行排序，所顺序不会发生变化
=> [[:a, 1], [:b, 2], [:c, 4], [:d, 3], [:e 2]]
```

#### 按照 hash 的值排序

```ruby
hash.sort_by {|k, v| v}
=> [[:a, 1], [:b, 2], [:e, 2], [:d, 3], [:c, 4]]
```

#### 按照 hash 的值倒序

```ruby
hash.sort_by {|k, v| -v}
=> [[:c, 4], [:d, 3], [:b, 2], [:e, 2], [:a, 1]]
```

#### 先按值倒序再按 key 正序

```ruby
hash = {“w”=>2, “k”=>1, “l”=>2, “v”=>5, “d”=>2, “h”=>4, “f”=>1, “u”=>1, “p”=>1, “j”=>1}

hash.sort_by {|k, v| [-v, k]}
=> [[“v”, 5], [“h”, 4], [“d”, 2], [“l”, 2], [“w”, 2], [“f”, 1], [“j”, 1], [“k”, 1], [“p”, 1], [“u”, 1]]
```
