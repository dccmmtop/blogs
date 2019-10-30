---
tags: [rails,activeRecirdStore]
date: 2018-08-07 22:53:19
---

### [转载](https://ruby-china.org/topics/32471)

<http://api.rubyonrails.org/classes/ActiveRecord/Store.html>

阅读 [http://api.rubyonrails.org](http://api.rubyonrails.org/) 相关的笔记

使用 Model 里面的一个字段作为一个序列化的封装，用来存储一个 key/value

文档里面提到，对应的存储字段的类型最好是 **text**， 以便确保有足够的存储空间

```
Make sure that you declare the database column used for the serialized store as a text, so there's plenty of room.
```

假设 Model 里面有一个字段 body

```ruby
class CreatePosts < ActiveRecord::Migration[5.0]
  def change
    create_table :posts do |t|
      t.string :title
      t.text :body  # 作为store序列化的字段
      t.boolean :published
      t.integer :status

      t.timestamps
    end
  end
end
```

接着设置对应的序列化属性

```ruby
class Post < ApplicationRecord

  # enum status: [ :active, :archived ] # 这里使用数组 与之对应的数字从0依次增加
  enum status: { active: 10, archived: 20 } # 明确指定对应的数字

  store :body, accessors: [ :color, :homepage, :email ], coder: JSON # 序列化属性

end
```

这样设置后，在 body 这一个字段上就可以存储多个 key/value 了

```ruby
irb(main):001:0> p = Post.create
   (0.1ms)  begin transaction
  SQL (1.2ms)  INSERT INTO "posts" ("created_at", "updated_at") VALUES (?, ?)  [["created_at", 2017-02-16 07:32:44 UTC], ["updated_at", 2017-02-16 07:32:44 UTC]]
   (1.9ms)  commit transaction
=> #<Post id: 4, title: nil, body: {}, published: nil, status: nil, created_at: "2017-02-16 07:32:44", updated_at: "2017-02-16 07:32:44">
irb(main):002:0> p.body
=> {}
irb(main):003:0> p.body.class
=> ActiveSupport::HashWithIndifferentAccess
irb(main):004:0> p.body[:color] = "red"
=> "red"
irb(main):005:0> p.body[:email] = "hello@126.com"
=> "hello@126.com"
irb(main):006:0> p.color
=> "red"
irb(main):007:0> p.email
=> "hello@126.com"
irb(main):008:0> p.body[:no_set] = "这个属性没有在model声明"
=> "这个属性没有在model声明"
irb(main):009:0> p.body[:no_set]
=> "这个属性没有在model声明"
irb(main):010:0> p.no_set #这个会报错
```
