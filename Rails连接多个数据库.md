---
tags: rails
date: 2019-04-03 11:48:31
---

假如已经连接的的数据库名是 `word_development`, 现在需要添加一个名为 `trademark_development` 和 `trademark_test` 的本地数据库

步骤如下:

### 添加配置信息

在 `config/database.yml` 添加如下信息

```yml
trademark_default: &trademark_default
  adapter: postgresql
  encoding: unicode
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>

trademark_development:
  <<: *trademark_default
  database: trademark_development

  # 如果是远程的数据库添加如下信息
  #username: username
  #password: 123456
  #host: 192.168.1.1
  #port: 3306

trademark_test:
  <<: *trademark_default
  database: trademark_test
```

### 关联 model

模仿 `ApplicationRecord` 这个抽象类，新建一个 `TrademarkBase` 抽象类，然后指明这个类连接的数据库的配置

```ruby
class TrademarkBase < ActiveRecord::Base
  self.abstract_class = true
  establish_connection "trademark_#{Rails.env}".to_sym
end
```

### 继承抽象类

新建的 model 继承该类，然后指定表名

```ruby
class TrademarkUser < TrademarkBase
  self.table_name = :users
end
```

### 测试

进入控制台中

```txt
pry(main)> TrademarkUser
=> TrademarkUser (call 'TrademarkUser.connection' to establish a connection)
```

可以看到成功关联另一个数据库中的表对象
