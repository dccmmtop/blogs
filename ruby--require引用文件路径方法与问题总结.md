### ruby--require引用文件路径方法与问题总结 

同一目录下的文件，如`/usr/local/ruby/foo.rb`与`/usr/local/ruby/bar.rb`两个文件。
如果直接在foo.rb中
`require 'bar'`
执行时会报找不到bar.rb错误。

这是因为运行
`$ ruby /usr/local/ruby/foo.rb`时会在ruby安装的lib目录和`/home/oldsong/`目录下查找bar.rb。而不会去rb文件的目录`/usr/local/ruby/`下查找。

所以除引用系统rb外，**require中不能用相对路径**

下面结合我个人经验介绍几种引用单个和目录下所有rb的方法。

1.引用一个文件
例: 引用当前rb同目录下的`file_to_require.rb`
先介绍3种方法
```ruby
require File.join(__FILE_, '../file_to_require')。
require File.expand_path('../file_to_require', __FILE__)
require File.dirname(__FILE__) + '/file_to_require' 
```
其中，File.expand_path是Rails常用的做法。
`__FILE__`为常量，表示当前文件的绝对路径，如`/home/oldsong/test.rb`
* 法四：
```ruby
$LOAD_PATH.unshift(File.dirname(__FILE__))
require 'bar' 
```
先把目录加入LOAD_PATH变量中，然后可直接引用文件名。

2.引用一个目录下所有文件

Ruby没有Java中的import java.io.\*;引用时不能用通配符，估计以后的版本有可能加上。

例：引用当前rb相同目录下lib/文件下所有*.rb文件。
* 法一
```ruby
 Dir[File.dirname(__FILE__) + '/lib/*.rb'].each{\|file\| require file } 
```

* 法二：

一个gem搞定
https://rubygems.org/gems/require_all