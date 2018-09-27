---
tags: ruby,range
date: 2018-09-27 09:10:50
---

ruby 中有个 range 对象，可以自动推测范围内的数据，比如：

```ruby
(1..100).each do |i|
  puts i
end
```

会输出 1 到 100 内的所有数字

### 自定义

如果我们有一个自定义的对象，假如名字为`Ym`

```ruby
class Ym
  attr_accessor :year, :month
  def initialize
    @year, @month = year, month
  end
end
```

若是想在`Ym`上使用`((Ym.new(2009,1))..(Ym.new(2010,1))).each {|i| puts i}`,输出的结果按照正常的年月逻辑来显示，该如何实现呢？

其实要实现类似`(1..100)`的方法很容易，只需在该类中`include Compareable`然后实现 `succ`和`<=>`方法就行了。

```ruby
class Ym
  include Comparable
  attr_accessor :year, :month

  def initialize(year, month)
    @year, @month = year, month
  end

  def succ
    #如果月份满12，则年份增加一，月份再从一开始。
    # 可以按需求定制更复杂的推测方法
    yyy, mmm = @month == 12 ? [@year + 1, 1] : [@year, @month + 1]
    Ym.new(yyy, mmm)
  end

  def <=>(other)
    # 定义大小规则
    (@year * 12 + @month) <=> (other.year * 12 + other.month)
  end

  def to_s
    sprintf "%4d-%02d", @year, @month
  end
end

(Ym.new(2008,8)..(Ym.new(2019,9))).each do |y|
  puts y
end
```

结果如图:

![](http://ogbkru1bq.bkt.clouddn.com/1538011494.png)
