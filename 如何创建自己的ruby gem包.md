### 如何创建自己的ruby gem包

 #### 编写一个最简单的例子
1. 建好如下文件夹
注意：lib目录下必须有个和你gem名字一样的rb文件。
``` shell
$ cd hola  
$ tree  
```
.  
├── hola.gemspec  
└── lib  
    └── hola.rb  
2. 编写代码
. hola.rb
```ruby
class Hola  
  def self.hi  
    puts "Hello world!"  
  end  
end  
```

.hola.gemspec
```ruby 
Gem::Specification.new do |s|  
  s.name        = 'hola'  
  s.version     = '0.0.0'  
  s.date        = '2010-04-28'  
  s.summary     = "Hola!"  
  s.description = "A simple hello world gem"  
  s.authors     = ["Nick Quaranto"]  
  s.email       = 'nick@quaran.to'  
  s.files       = ["lib/hola.rb"]  
  s.homepage    =  
    'http://rubygems.org/gems/hola'  
end  
```
上面字段的意思，比较简单。相信大家都能理解。

3.编译生成gem
```shell
% gem build hola.gemspec  
```

>  Successfully built RubyGem  
>
> Name: hola  
>
> Version: 0.0.0  
>
> File: hola-0.0.0.gem  

```shell
% gem install ./hola-0.0.0.gem 
```

>  Successfully installed hola-0.0.0  

>  1 gem installed  

4.测试使用
[ruby] view plain copy
% irb  
>> require 'hola'  
>> true  
>> Hola.hi  
  llo world!  
  ：在ruby 1.9.2之前到版本里面，需要先require 'rubygem'，才能使用我们写的gem.

5.发布到rubygems网站
[ruby] view plain copy
$ curl -u tom https://rubygems.org/api/v1/api_key.yaml >  
~/.gem/credentials  
Enter host password for user 'tom':  
设定完之后发布
[ruby] view plain copy
% gem push hola-0.0.0.gem  
Pushing gem to RubyGems.org...  
Successfully registered gem: hola (0.0.0)  
发布成功。
这样任何一个人都可以使用你写的gem了。

稍微复杂的rubygem例子
上面的例子只有一个ruby文件，一般gem应该没有这么简单的。
下面说下有多个ruby文件该怎么写。
1. 目录结构 
多了个hola目录和translator.rb文件
[ruby] view plain copy
% tree 
. 
├── hola.gemspec 
└── lib 
    ├── hola 
    │   └── translator.rb 
    └── hola.rb  
2. 代码
lib/hola/translator.rb
[ruby] view plain copy
% cat  lib/hola/translator.rb  
class Hola::Translator  
  def initialize(language)  
    @language = language  
  end  
  
  def hi  
    case @language  
    when :spanish  
      "hola mundo"  
    else  
      "hello world"  
    end  
  end  
end  
lib/hola.rb
[ruby] view plain copy
% cat lib/hola.rb  
class Hola  
  def self.hi(language = :english)  
    translator = Translator.new(language)  
    translator.hi  
  end  
end  
  
require 'hola/translator'  
.hola.gemspec
[ruby] view plain copy
% cat hola.gemspec  
Gem::Specification.new do |s|  
  s.name        = 'hola'  
  s.version     = '0.0.0'  
  s.date        = '2010-04-28'  
  s.summary     = "Hola!"  
  s.description = "A simple hello world gem"  
  s.authors     = ["Nick Quaranto"]  
  s.email       = 'nick@quaran.to'  
  s.files       = ["lib/hola.rb", "lib/hola/translator.rb"]  
  s.homepage    =  
    'http://rubygems.org/gems/hola'  
end  
红色是和上面不一样的地方。

其他步骤和上面一样了。很简单吧！

最后说下怎么写个 gem包含可执行文件的例子。
这个也很简单。像rake就是典型的包含可执行文件的gem.
1. 在刚才工程目录下建个bin文件夹
生成可执行文件，并且修改权限为可运行。
[ruby] view plain copy
% mkdir bin  
% touch bin/hola  
% chmod a+x bin/hola  

2. 修改可执行文件内容
bin/hola
[ruby] view plain copy
#!/usr/bin/env ruby  

require 'hola'  
puts Hola.hi(ARGV[0])  

测试下
% ruby -Ilib ./bin/hola  
hello world  

% ruby -Ilib ./bin/hola spanish  
hola mundo  

3 .最后修改gemspec
[ruby] view plain copy
% head -4 hola.gemspec  
Gem::Specification.new do |s|  
  s.name        = 'hola'  
  s.version     = '0.0.1'  
  s.executables << 'hola'  
其他就和上面一样了。很简单吧。