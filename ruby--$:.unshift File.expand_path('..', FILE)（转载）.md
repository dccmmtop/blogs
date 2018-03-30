## [ruby--$:.unshift File.expand_path('..', __FILE__)（转载）](http://www.cnblogs.com/timsheng/archive/2013/04/13/3017911.html)

一直能看到一些gem里面会有这样一句代码：
`$:.unshift File.expand_path('..', __FILE__)`
这句话是干什么用的呢
\$:就是ruby的一个全局变量，也叫$LOAD_PATH，功能就是java中的classpath，用来加载类库的，当你require某个文件时，ruby就会从这个变量的值去查找，找不到会报LoadError。这个值其实就是一个包含了类库绝对路径的数组。
`__FILE__`这个变量代表文件自己的文件名，在foo.rb中puts__FILE__，结果就是foo.rb。
`File.expand_path`可以把路径转换成绝对路径，假设有这样一个文件`/Users/kenshin/foo.rb`，里面有`File.expand_path('..', __FILE__)`，返回的结果就是`/Users/kenshin`。
unshift是数组的一个方法，功能就是把指定的值加到数组的最前面，`[3,4].unshift(1,2) => [1,2,3,4]`
所以这段代码的意思就是把当前文件所在的目录加到ruby的loadpath的最前面，在require文件时，ruby就会先从当前目录下去查找了。