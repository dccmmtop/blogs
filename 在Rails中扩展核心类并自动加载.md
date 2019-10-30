---
tags: [打开类]
date: 2019-01-19 10:52:07
---

### 打开 String 类

在 `lib/core_ext/` 目录下新建 `string.rb`  定义方法：

```ruby
class String
  def say_hello
     "hello #{self}"
  end
end
```

### 自动加载

在 `config/initializers/` 下新建 `core_ext.rb` 加载 String

```ruby
Dir[File.join(Rails.root, "lib", "core_ext", "*.rb")].each do |f| 
  require f
end
```

### Other

直接在 `config/initializers` 中新建 `string.rb`，重新打开类，添加方法
