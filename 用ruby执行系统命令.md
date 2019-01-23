---
tags: ruby,system
date: 2019-01-03 09:50:00
---

### 反引号

1. 返回标准输出

```ruby
output = `ls`
puts "output is #{output}"
```

Result of above code is

```shell
$ ruby main.rb
output is lab.rb
```

2. 反引号执行系统命令时，会把异常抛给主线程

反引号会从主进程新开一个进程执行命令，如果子进程发生异常，会传递给主进程，如果主进程没有对异常进行处理，主进程就会终止。

下面的例子中 执行一个‘xxxxx’非法的命令

```ruby
output = `xxxx`
puts "output is #{output}"
```

执行结果：

```shell
$ ruby main.rb
main.rb:1:in ``': No such file or directory - xxxxxxx (Errno::ENOENT)
	from main.rb:1:in `<main>'
```

3. 阻塞进程

主进程会一直等待反引号中的子进程结束

4. 检查命令的执行状态

使用 `$?.success?` 来检查命令的执行状态

```ruby
output = `ls`
puts "output is #{output}"
puts $?.success?
```

结果:

```shell
$ ruby main.rb
output is lab.rb
main.rb
true
```

5. 允许使用字符串插值

例子：

```ruby
cmd = 'ls'
`#{cmd}`
```

6. `x%`

`x%` 和反引号一样，它可以使用不同的分隔符

```ruby
output = %x[ ls ]
output = %x{ ls }
```

### system

system 也可以执行系统命令，他和反引号有点相像。

1. 阻塞进程

2. 隐藏异常

system 不会向主进程传递异常。

```ruby
output = system('xxxxxxx')
puts "output is #{output}"
```

结果：

```shell
$ ruby main.rb
output is
```

3. 检查命令的执行状态

如果命令成功执行，则系统返回true（退出状态为零）。对于非零退出状态，它返回false, 如果命令执行失败，返回nil

```ruby
system("command that does not exist")  #=> nil
system("ls")                           #=> true
system("ls | grep foo")                #=> false
```

### exec

exec 会替换掉当前进程，请看下面的例子：

在 irb 中执行exec('ls')：

```shell
$ irb
e1.9.3-p194 :001 > exec('ls')
lab.rb  main.rb

nsingh ~/dev/lab 1.9.3
$
```

可以发现，执行完exec("ls")命令以后，已经退出irb，回到shell。

由于exec替换了当前进程，因此如果操作成功，则不会返回任何内容。如果操作失败，则引发`SystemCallError`

### sh

sh实际上是在呼叫系统, FileUtils在rake中添加了此方法。它允许以简单的方式检查命令的退出状态。

```ruby
require 'rake'
sh %w(xxxxx) do |ok, res|
   if !ok
     abort 'the operation failed'
   end
end
```

### popen3

如果你要捕获stdout和stderr，那么你应该使用popen3，因为这个方法允许你与stdin，stdout和stderr进行交互。

我想以编程方式执行git push heroku master，我想捕获输出。这是我的代码。

```ruby
require 'open3'
cmd = 'git push heroku master'
Open3.popen3(cmd) do |stdin, stdout, stderr, wait_thr|
  puts "stdout is:" + stdout.read
  puts "stderr is:" + stderr.read
end
```

输出：

```shell
stdout is:
stderr is:
-----> Heroku receiving push
-----> Ruby/Rails app detected
-----> Installing dependencies using Bundler version 1.2.1
```

这里需要注意的重要一点是，当我执行程序ruby lab.rb时，我的终端在前10秒内没有看到任何输出。然后我将整个输出视为一个转储。
另外需要注意的是，heroku正在将所有这些输出写入stderr而不是stdout。

所以我们应该捕获来自heroku的输出，因为它正在流式传输而不是在处理结束时将整个输出转储为一个单个的字符串块。

这是修改后的代码。

```ruby
require 'open3'
cmd = 'git push heroku master'
Open3.popen3(cmd) do |stdin, stdout, stderr, wait_thr|
  while line = stderr.gets
    puts line
  end
end
```

现在，当我使用ruby lab.rb执行上述命令时，我会逐步获得终端输出，就好像我输入了git push heroku master一样。 这是捕获流输出的另一个例子。

```ruby
require 'open3'
cmd = 'ping www.google.com'
Open3.popen3(cmd) do |stdin, stdout, stderr, wait_thr|
  while line = stdout.gets
    puts line
  end
end
```

在上面的例子中，您将在终端上获得ping的输出，就像您在终端上键入ping www.google.com一样。

现在让我们看看如何检查命令是否成功

```ruby
require 'open3'
cmd = 'ping www.google.com'
Open3.popen3(cmd) do |stdin, stdout, stderr, wait_thr|
  exit_status = wait_thr.value
  unless exit_status.success?
    abort "FAILED !!! #{cmd}"
  end
end
```

### popen2e

popen2e类似于popen3，但合并了标准输出和标准错误。

```ruby
require 'open3'
cmd = 'ping www.google.com'
Open3.popen2e(cmd) do |stdin, stdout_err, wait_thr|
  while line = stdout_err.gets
    puts line
  end

  exit_status = wait_thr.value
  unless exit_status.success?
    abort "FAILED !!! #{cmd}"
  end
end
```

在所有其他领域，此方法与popen3类似。

### Process.spawn

Kernel.spawn在子shell中执行给定的命令。它会立即返回进程ID。

```ruby
irb(main)> pid = Process.spawn("ls -al")
=> 81001
```
