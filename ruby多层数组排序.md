##ruby中数组进行多层排序
例子如下：

用sort_by
```ruby
def order_weight_sort_by(string)
  string.split(" ").sort_by do |a|
      sum_a = a.split("").inject(0) { |mem, var| mem += var.to_i }
      first_number = a[0].to_i
      [a.size, sum_a, first_number]
  end.join(" ")
end
```
string = "56 65 74 100 99 68 86 980 90" 
p order_weight_sort_by(string )
结果是"90 56 65 74 68 86 99 100 980"

上面的方法是先将字符串变成一个由数字字符串组成的数组。然后先按照字符串的长度进行排序，再按照字符串各数字之和进行排序，最后按照字符串的第一个数字大小进行排序。

关键代码[a.size, sum_a, first_number]

当最后是一个条件数组时，sort_by会按照该条件数组的顺序依次排序。

用sort
```ruby
def order_weight_sort(string)
  string.split(" ").sort do |a, b|
      sum_a = a.split("").inject(0) { |mem, var| mem += var.to_i }
      first_number_a = a[0].to_i
      size_a = a.size

      sum_b = b.split("").inject(0) { |mem, var| mem += var.to_i }
      first_number_b = b[0].to_i
      size_b = b.size

      [size_a, sum_a, first_number_a] <=> [size_b, sum_b, first_number_b]
  end.join(" ")
end
```
string = "56 65 74 100 99 68 86 980 90" 
p order_weight_sort(string )
结果与上面是一样的。

关键代码[size_a, sum_a, first_number_a] <=> [size_b, sum_b, first_number_b]

这样看起来sort_by比sort简洁很多。
确实sort_by只需要一个参数，而sort需要两个参数。但他们实现多层排序是一样的，最后都是用一个条件数组来表示。

区别

但sort要比sort_by灵活。因为最后的排序条件还可以这样写：

`[size_b, sum_a, first_number_b] <=> [size_a, sum_b, first_number_a]`
这样就相当于先按长度倒序排列，然后再按照字符串各数字之和进行排序，最后再按照首个字符的大小倒序排列。

作者：kamionayuki
链接：http://www.jianshu.com/p/319d0174f246
來源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
