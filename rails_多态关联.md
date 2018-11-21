---
tags: rails
date: 2018-11-21 15:35:41
---

### 什么是多态关联

假如有三个模型，分别是 用户， 产品， 图片。图片为用户所有，也为产品所有。我们可以创建两个 picture 的模型，如下

`rails g modle picture_user user_id:integer name:string url:string`

`rails g modle picture_product product_id:integer name:string url:string`

```ruby
class PictureUser < ApplicationRecord
  belongs_to :user
end

class PictureProduct < ApplicationRecord
  belongs_to :product
end
```

这样我们就可以使用`user.prictures` 和 `product.pictures`来分别获得用户下和产品下的图片了。但是我们发现，两个图片模型除了外键不一样，其他字段都是一样的，那么有没有一种办法只创建一个 picture 模型，同时属于 user 和 product 呢，这种既属于一个模型又属于另外一个模型（可以是很多个）的关联就是多态关联。

### 多态关联的实现

为了能同时使用 `user.pictures` 和 `product.pictures` 来获得各自的图片，我们就需要对 picture 模型做一些修改，使其能够标识一张图片是属于 user 还是属于 product ，当然外键是必不可少的。我们还需要一个外键对应类的名称，如下：

`rails g modle picture pictureable_id:integer pictureable_type:string name:string url:string`

```ruby
class Picture < ApplicationRecord
  belongs_to :pictureable, polymorphic: true
end

class User < ApplicationRecord
  has_many :pictures, as: :pictureable
end

class Product < ApplicationRecord
  has_many :pictures, as: :pictureable
end
```

pictureable 相当于一个接口，凡是拥有图片的模型都可以像 User 那样使用关联。

可以使用`user.pictures.create(name: 'user_0', url: 'https://dcc.com')`来创建一条关联对象，创建之后我们发现在 picture 表中多了一条记录：

```txt
id: 1, pictureable_type: 'User', pictureable_id: 1, name:"user_0", url:'https://dcc.com'
```

pictureable_type: 'User' 就是所属对象的标识，这样才可以使用 user.pictures 进行查询。由此我们知道，多态关联中，`xxxable_type, xxxable_id`字段是必不可少的。
