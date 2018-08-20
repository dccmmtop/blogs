---
tags: rails
date: 2018-08-20 18:09:07
---

在 rails 中,model 的属性是默认的可读可写的，有时我们需要重写某个字段的访问器。当查询某个字段的值时，需要进行其他操作；

如： 当查询`recommand_code`的值时，若存在，则返回，若不存在则创建一个包含大写字母和数字的 6 为随机字符串

```ruby
def recommand_code
  #  重写 recommand_code 字段
  _code = read_attribute(:recommand_code)
   # _code  = self.recommand_code   错误，会引起无限递归
  if self.block.empty?
    self.recommand_code=nil;
    return
  end
  return _code if _code
  loop do
    _code =  (("0".."9").to_a +  ("A".."Z").to_a).sample(6).join
    break if User.find_by_recommand_code(_code).nil?
  end
  self.recommand_code = nil;
  return _code
end

def recommand_code=(value)
  write_attribute(:recommand_code,value)
end
```
