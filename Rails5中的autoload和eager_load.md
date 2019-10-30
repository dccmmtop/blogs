---
tags: [自动加载]
date: 2019-01-19 10:21:48
---

> 翻译并整理： https://collectiveidea.com/blog/archives/2016/07/22/solutions-to-potential-upgrade-problems-in-rails-5
               https://blog.bigbinary.com/2016/08/29/rails-5-disables-autoloading-after-booting-the-app-in-production.html

### autoload和eager_load

**autoload**: 在常量使用之前不会加载，只有当使用一个当前不存在常量时，会在 `autoload_paths` 寻找，然后加载它，当在给定的目录中找不到这个常量时，就会触发错误。并且 `autoload` 是非线程安全的

**eager_load** 在使用常量之前，加载 `eager_load_paths` 中的所有常量

### Rails5的变化

在 Rails5 中的生产环境下， `autoload` 默认是被禁用的， 有三种办法解决这个问题

### 添加路径到 eager_load_paths

假如自己写的类在lib下

在 `config/application.rb` 中

```ruby
config.eager_load_paths << Rails.root.join('lib')
```

### 重新启用 autoload

你可以在任何环境中重新启用 autoload, 但是这种方法在高版本中可能被弃用

```ruby
config.enable_dependency_loading = true
```

### 把代码移动到 app/ 目录下
 
Rails 在默认的情况下会 autolaod 和 eager_load app/ 目录下的所有内容。这样可以减少你额外的配置。例如把 lib 目录 移动到 app/lib/ 

> 译注： 这种方式使 rails 的目录变得混乱， 不建议

