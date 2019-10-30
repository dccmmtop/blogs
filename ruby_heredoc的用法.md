---
tags: [ruby,heredoc]
date: 2018-11-22 09:23:36
---

## 整理自[ruby-china](https://ruby-china.org/topics/25983)

### 基础的用法

```ruby
def print_heredoc
  puts <<EOF
this is the first line
this is the second line
EOF
end

print_heredoc
```

输出：

```txt
this is the first line
this is the second line
```

如果你觉得代码太难看（这根本不符合 Ruby 的风格），你可能会这样写：

```ruby
def print_heredoc
  puts <<EOF
    this is the first line
    this is the second line
  EOF
end

print_heredoc
```

你会发现高亮显示已经不对了，它还会报这样的一个错误：

```txt
test.rb:6: can't find string "EOF" anywhere before EOF
test.rb:2: syntax error, unexpected end-of-input, expecting tSTRING_CONTENT or tSTRING_DBEG or tSTRING_DVAR or tSTRING_END
```

### 可以缩进的用法

希望代码写的漂亮一点的话，就得多做点工作，在第一个 EOF 前加上一个减号就 OK 了：

```ruby
def print_heredoc
  puts <<-EOF
    this is the first line
    this is the second line
  EOF
end

print_heredoc
```

输出：

```txt
this is the first line
this is the second line
```

### ruby 2.3 引入一个新的语法

```ruby
def hello
  puts <<~HEREDOC
    I know I know
    You will like it.
  HEREDOC
end

hello
```

完美输出:

```txt
I know I know
You will like it.
```

### heredoc 的本质

有下面一个方法：

```ruby
def a_method(string, integer)
  puts "the string is #{string} and the integer is #{integer}"
end
```

一般这么用这个方法：

```ruby
a_method "the string", 1
```

如果想用 heredoc 呢？这里就需要说下那个`<<-EOF`到底是什么东西？其实`<<-EOF`只是个占位符，写上它以后，它就代表将要输入的字符串，这个字符串的判断是从`<<-EOF`的下一行开始计算，一直碰到只有`EOF`的一行（这一行只有一个`EOF`），这个字符串就这样计算出来的。如果上面的这个方法要用 heredoc 的话，就可以这样写：

```ruby
a_method <<-EOF, 1
  this is the first line
  this is the second line
  EOF
```

输出：

```ruby
the string is this is the first line
this is the second line
 and the integer is 1
```

可以看到，这个`<<-EOF`就好像一个实参一样，我们也可以把他完全当个实参来对待，它是个字符串，那么就可以调用字符串的方法，像这样：

```ruby
a_method <<-EOF.gsub("\n", ""), 1
  this is the first line
  this is the second line
  EOF
```

输出：

```txt
the string is this is the first linethis is the second line and the integer is 1
```

我们把它掰直了，哈哈。换个写法更能体现 heredoc 的本质：

```ruby
a_method(<<-EOF.gsub("\n", ""), 1)
  this is the first line
  this is the second line
  EOF
```

### heredoc 的小技巧

那个`<<-EOF`为什么叫`EOF`，为什么不叫`ABC`，你可以试试，叫`ABC`也可以，但是末尾那个也要写 ABC，要保持配对。在有些编辑器（Atom，RubyMine）中甚至会根据占位符将 heredoc 中的内容高亮显示，比如可以写`<<-RUBY`, `<<-HTML`等等。

假如前面的那个方法要传入两个字符串该怎么写呢？很简单：

```ruby
a_method <<-STR1, <<-STR2
  this is for STR1
  STR1
  this is for STR2
  STR2
```

输出：

```txt
the string is this is for STR1
 and the integer is   this is for STR2
```

有一点点晕，解释一下。`<<-STR1`在前面，它就会一直找到只包含`STR1`的那一行，并把其中的内容替换掉`<<-STR1`，而`<<-STR2`在后面，它不会包括`<<-STR1`和`STR1`中的部分， 会一直找到只包含`STR2`的那一行，然后替换`<<-STR2`。只需记住是只包含占位符的一行，像`this is for STR1`中虽然包含`STR1`，但是这一行还有其他字符，heredoc 就不会在这一行结束，而是接着往下找。同时需要知道，虽然是占位符，但是可以调用字符串的方法，在 Rails5 中最近的一个 pull request 中 DHH 就用了很多 heredoc。不过我找了半天没找到，等以后找到再把链接付在这里。

前面的输出结果中，大家一定发现一个问题，就是字符串的换行和行首的缩进，有时候你想要他们，有时候可不是，我们可以用`gsub`来替换。在 Rails 中已经有一个好用的方法了：

```ruby
2.times do
  puts <<-STR.strip_heredoc
    this is the first line
      this is the second line
  STR
end
```

输出：

```ruby
this is the first line
  this is the second line
this is the first line
  this is the second line
```

注意行首是没有空格的，和输入的格式保持了一致，很方便，实现也是很简单的，参考 github 吧，[strip.rb](https://github.com/rails/rails/blob/master/activesupport/lib/active_support/core_ext/string/strip.rb#L22)

### 那些太奇怪的写法

其实还有很多奇怪的写法，比如下面这个：

```ruby
puts <<-"I am the content"
line 1
line 2
line 3
I am the content
```

这是合法语法，但估计不会有人这么写，甚至有的编辑器都不能正常高亮显示它.
