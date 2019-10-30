---
tags: [ruby,c]
date: 2018-09-21 17:03:09
---

因为 ruby 是解释性的语言，单纯计算的循环不是很快。如果将处理交给编译器，就会变的很快。

用 C 语言编写 ruby 扩展了=库相当简单。RubyInline 就是 ruby 中能够直接嵌入 C 语言代码，初次执行时，C 语言代码会自动编译，第二次以后的执行会自动装载缓存中的扩展库

示例:

```ruby
require "inline"
class MyTest
  inline do |builder|
    builder.include "<math.h>"
    builder.c "
    int sum(){
      int len = 100;
      sum = 0;
      int i = 0;
      for(i = 0; i<= len i++){
        sum += i;
      }
      return sum;
    }
    "
  end
end

t = MyTest.new
t.sum
```
